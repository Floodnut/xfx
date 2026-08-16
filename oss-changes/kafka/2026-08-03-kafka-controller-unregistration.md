# 2026-08-03 Apache Kafka 컨트롤러 등록 해제

## 메타데이터

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | Apache Kafka (apache/kafka) |
| 관련 릴리스 | 릴리스 태그 없음. `trunk`에 머지된 변경이며, 동작에 필요한 MetadataVersion은 `4.4-IV2`(`IBP_4_4_IV2`, feature level 33) |
| 관련 PR | [#22191 (KAFKA-20395: Support unregistering controllers)](https://github.com/apache/kafka/pull/22191) (merged 2026-08-03) |
| 머지 커밋 | [`c274a7348fb66d1bde99566bdb072fbb52b03dfd`](https://github.com/apache/kafka/commit/c274a7348fb66d1bde99566bdb072fbb52b03dfd) |
| 관련 설계 문서 | [KIP-1312: Support unregistering controllers](https://cwiki.apache.org/confluence/spaces/KAFKA/pages/406623954/KIP-1312+Support+unregistering+controllers) |
| 관련 이슈 | KAFKA-20395 |
| 발견 출처 | `gh api repos/apache/kafka/commits`로 확인한 `trunk` 최신 커밋 목록 (Kafka는 GitHub Releases를 쓰지 않아 커밋 활동이 발견 신호) |
| 검증 근거 | PR #22191의 실제 diff (43개 파일), 머지 커밋 `c274a734` 기준의 `ClusterControlManager.java`, `FeatureControlManager.java`, `QuorumController.java`, `ControllerRegistrationManager.java`, `ControllerApis.scala`, `MetadataQuorumCommand.java` 원문, 새로 추가된 `UnregisterControllerRecord.json` / `UnregisterControllerRequest.json` 스키마, KIP-1312 본문 |

## 개요

KRaft 클러스터에서 컨트롤러 노드는 자기 자신을 메타데이터 로그에 `RegisterControllerRecord`로 등록한다. 그런데 브로커와 달리 이 등록을 지우는 방법이 없었다. 컨트롤러를 클러스터에서 빼려면 KIP-853의 `remove-controller`로 KRaft 보터 집합에서 제외하는 수밖에 없는데, 이 명령은 Raft 계층의 보터 목록만 건드리고 메타데이터 계층의 등록 정보는 그대로 남겨둔다.

남은 등록 정보는 단순한 표시상의 찌꺼기가 아니다. 액티브 컨트롤러가 피처 레벨(MetadataVersion 포함) 업그레이드를 승인할지 판단할 때 **등록된 모든 컨트롤러**의 지원 버전 범위를 훑기 때문에, 이미 클러스터에서 빠진 구버전 컨트롤러의 등록이 하나 남아 있으면 그 등록이 업그레이드를 계속 거부한다.

PR #22191은 브로커 쪽에 이미 있던 `UnregisterBroker`와 대칭되는 경로를 컨트롤러에도 만들었다. 새 RPC `UnregisterController`(API key 94), 새 메타데이터 레코드 `UnregisterControllerRecord`, 새 에러 코드 `CONTROLLER_ID_NOT_REGISTERED`(136), `Admin#unregisterController` API, 그리고 `kafka-cluster.sh unregister-controller` 명령과 `kafka-metadata-quorum.sh remove-controller --unregister` 플래그가 함께 들어갔다.

## 사전 지식

### KRaft 로그 위에 겹쳐 있는 두 계층

KRaft 클러스터에서 `__cluster_metadata` 로그는 한 개지만, 그 로그에 쓰이는 레코드는 성격이 다른 두 종류다.

- **Raft 제어 레코드(control record)**: 합의 알고리즘 자체가 쓰고 읽는 레코드. 보터 집합을 담는 `VotersRecord`, 스냅샷 헤더/푸터, 리더 변경 기록 등이 여기 속한다. 누가 투표권을 가진 노드인지는 이 계층이 관리한다.
- **메타데이터 레코드(metadata record)**: 컨트롤러가 관리하는 클러스터 상태. 토픽, 파티션, ACL, 그리고 브로커/컨트롤러 등록 정보가 여기 속한다. `QuorumController`가 이 레코드들을 재생(replay)해서 `MetadataImage`를 만든다.

즉 **어떤 노드가 보터인가**와 **어떤 노드가 등록되어 있는가**는 같은 로그에 실려 있지만 서로 다른 계층이 관리하는 별개의 상태다. 이 분리가 이번 변경의 배경 전부라고 해도 된다.

### 컨트롤러 등록(KIP-919)

KIP-919가 도입한 컨트롤러 등록은 컨트롤러 노드가 자신의 엔드포인트와 지원 피처 범위를 메타데이터 로그에 남기는 절차다. 각 컨트롤러 노드는 `ControllerRegistrationManager`를 돌리며, 자기 등록이 로그에 없거나 자기 incarnation ID와 다르면 액티브 컨트롤러에 `ControllerRegistrationRequest`를 보낸다.

```java
// server/.../ControllerRegistrationManager.java, MetadataUpdateEvent.run()
if (delta.clusterDelta().changedControllers().containsKey(nodeId)) {
    ControllerRegistration curRegistration = newImage.cluster().controllers().get(nodeId);
    if (curRegistration == null) {
        logger.info("Registration removed for this node ID.");
        registeredInLog = false;
    } else if (!curRegistration.incarnationId().equals(incarnationId)) {
        ...
        registeredInLog = false;
    } else {
        registeredInLog = true;
    }
}
maybeSendControllerRegistration();
```

`registeredInLog`가 false가 되면 `maybeSendControllerRegistration()`이 다시 등록 RPC를 보낸다. 이 재등록 루프는 이번 PR 이전부터 있던 코드이며, 뒤에서 보듯 등록 해제의 안전장치로 그대로 재사용된다.

액티브 컨트롤러 쪽에서는 `ClusterControlManager.registerController()`가 요청을 받아 `RegisterControllerRecord`를 만들고, 재생 시 `controllerRegistrations` 맵에 넣는다.

```java
public void replay(RegisterControllerRecord record) {
    ControllerRegistration newRegistration = new ControllerRegistration.Builder(record).build();
    ControllerRegistration prevRegistration =
        controllerRegistrations.put(record.controllerId(), newRegistration);
    ...
}
```

이 맵에는 `put`만 있고 `remove`가 없었다. 이것이 문제의 출발점이다.

### 브로커에는 이미 있던 등록 해제

브로커는 사정이 다르다. 브로커를 클러스터에서 영구히 빼는 `UnregisterBrokerRequest`와 `UnregisterBrokerRecord`, 그리고 이를 호출하는 `kafka-cluster.sh unregister --id` 명령이 오래전부터 있었다. `Controller` 인터페이스에도 `unregisterBroker()`가 있다. 컨트롤러에만 이 대칭이 없었다.

### KIP-853 동적 쿼럼의 remove-controller

KIP-853은 KRaft 쿼럼 구성을 동적으로 바꿀 수 있게 했고, `kafka-metadata-quorum.sh remove-controller --controller-id <id> --controller-directory-id <dir-id>` 명령을 제공한다. 이 명령은 `Admin#removeRaftVoter`를 통해 `RemoveRaftVoter` RPC를 보내고, KRaft가 검사를 통과시키면 새 `VotersRecord`를 쓴다.

```java
// raft/.../KafkaRaftClient.java, handleRemoveVoterRequest 주석
// When all checks pass, the voter is removed from the voter set and a new VotersRecord ...
```

여기서 쓰이는 것은 Raft 제어 레코드다. 메타데이터 계층의 `controllerRegistrations`는 이 경로에서 아무 영향도 받지 않는다.

보터에서 빠진 컨트롤러 노드는 사라지는 것이 아니라 **옵저버(observer)**가 된다. 옵저버는 로그를 계속 복제하지만 리더 선출에 참여하지 않는 노드로, 여전히 등록되어 있고 계속 재등록을 시도한다.

### 피처 업그레이드가 등록 정보를 읽는 지점

`kafka-features.sh upgrade`나 MetadataVersion 업그레이드는 `FeatureControlManager.updateFeatures()`를 거치고, 그 안의 `reasonNotSupported()`가 클러스터 전체가 목표 레벨을 지원하는지 확인한다.

```java
// metadata/.../FeatureControlManager.java, reasonNotSupported()
if (metadataVersionOrThrow().isControllerRegistrationSupported()) {
    for (Iterator<Entry<Integer, Map<String, VersionRange>>> iter =
         clusterSupportDescriber.controllerSupported();
         iter.hasNext(); ) {
        Entry<Integer, Map<String, VersionRange>> entry = iter.next();
        if (entry.getKey() == quorumFeatures.nodeId()) {
            continue;
        }
        reason = QuorumFeatures.reasonNotSupported(newVersion,
                "Controller " + entry.getKey(),
                entry.getValue().getOrDefault(featureName, QuorumFeatures.DISABLED));
        if (reason.isPresent()) return reason;
        ...
    }
}
```

`clusterSupportDescriber.controllerSupported()`가 실제로 무엇을 순회하는지가 핵심이다.

```java
// metadata/.../ClusterControlManager.java
Iterator<Entry<Integer, Map<String, VersionRange>>> controllerSupportedFeatures() {
    ...
    private final Iterator<ControllerRegistration> iter = controllerRegistrations.values().iterator();
```

보터 집합이 아니라 **등록 맵 전체**다. 등록이 남아 있는 한, 그 노드가 지금 클러스터에서 어떤 역할이든(보터든, 옵저버든, 아예 꺼져 있든) 그 노드의 지원 범위가 업그레이드 판정에 그대로 반영된다.

## 변경 분석

### 변경 전: 보터에서 빼도 등록은 남고, 그 등록이 업그레이드를 막는다

구버전 소프트웨어를 돌리는 컨트롤러 3번을 클러스터에서 빼고 신버전 컨트롤러로 교체한 뒤 MetadataVersion을 올리려는 상황을 생각하면 문제가 그대로 드러난다.

```
[변경 전] 보터 집합과 등록 맵이 따로 논다

  __cluster_metadata 로그
  ┌──────────────────────────────────────────────────────────────┐
  │ Raft 제어 레코드 계층                                          │
  │   VotersRecord: {1, 2, 4}          ← remove-controller 로     │
  │                                       3번을 뺀 결과            │
  ├──────────────────────────────────────────────────────────────┤
  │ 메타데이터 레코드 계층                                          │
  │   RegisterControllerRecord(1)                                │
  │   RegisterControllerRecord(2)                                │
  │   RegisterControllerRecord(3)  ← 지울 방법이 없다              │
  │   RegisterControllerRecord(4)                                │
  └──────────────────────────────────────────────────────────────┘
                              │ replay
                              ▼
              ClusterControlManager.controllerRegistrations
              { 1: reg, 2: reg, 3: reg(구버전), 4: reg }
                              │
                              │ controllerSupportedFeatures()
                              ▼
        FeatureControlManager.reasonNotSupported(metadata.version, 33)
              ├─ Controller 1 → OK
              ├─ Controller 2 → OK
              ├─ Controller 3 → 지원 범위가 33을 못 담음  ✗
              └─ 업그레이드 거부: "Controller 3 does not support ..."
```

정리하면 이렇다.

- `remove-controller`는 Raft 계층만 정리하므로, 메타데이터 계층에는 3번의 등록이 남는다.
- 등록은 로그에 영속화되어 있고 스냅샷에도 포함되므로, 컨트롤러를 재시작하거나 로그를 압축해도 사라지지 않는다.
- `reasonNotSupported()`는 보터 여부를 보지 않고 등록 맵을 순회하므로, 3번의 지원 범위가 계속 판정에 들어간다.
- 결과적으로 클러스터에 존재하지도 않는 노드가 피처 업그레이드를 무기한 거부한다.

KIP-1312의 Motivation이 지목하는 것도 정확히 이 지점이다. 특히 옵저버로 남은 노드의 오래된 등록이 클러스터 업그레이드를 막는 사례가 동기로 언급된다.

이 상황에서 운영자가 쓸 수 있는 우회책은 클러스터 메타데이터를 직접 조작하는 것이 아니라, 문제되는 ID로 신버전 컨트롤러를 다시 띄워 등록을 덮어쓰는 정도였다. KIP-1312 Compatibility 절도 기존 클러스터는 업그레이드 없이는 이 찌꺼기를 지울 수 없고, 대체 컨트롤러를 프로비저닝하거나 우회책을 쓰는 수밖에 없다고 명시한다.

### 변경 후 구현: 등록 맵에 remove 경로를 뚫는다

PR #22191이 추가한 것은 등록의 역연산이다. `ClusterControlManager`에 `unregisterController()`가 생겼다.

```java
ControllerResult<Void> unregisterController(int controllerId) {
    if (!featureControl.metadataVersionOrThrow().isControllerUnregistrationSupported()) {
        throw new UnsupportedVersionException("The current MetadataVersion is too old to " +
                "support controller unregistration.");
    }
    if (controllerRegistrations.get(controllerId) == null) {
        throw new ControllerIdNotRegisteredException("Controller ID " + controllerId +
            " is not currently registered.");
    }
    List<ApiMessageAndVersion> records = new ArrayList<>();
    records.add(new ApiMessageAndVersion(new UnregisterControllerRecord().
        setControllerId(controllerId),
            (short) 0));
    return ControllerResult.atomicOf(records, null);
}
```

그리고 재생 시 등록 맵에서 실제로 제거한다.

```java
public void replay(UnregisterControllerRecord record) {
    int controllerId = record.controllerId();
    ControllerRegistration registration = controllerRegistrations.remove(controllerId);
    if (registration == null) {
        throw new RuntimeException(String.format("Unable to replay %s: no controller " +
            "registration found for that id", record));
    } else {
        log.info("Replayed {}", record);
    }
}
```

새 레코드 스키마는 컨트롤러 ID 하나만 담는다.

```json
{
  "apiKey": 29,
  "type": "metadata",
  "name": "UnregisterControllerRecord",
  "validVersions": "0",
  "flexibleVersions": "0+",
  "fields": [
    { "name": "ControllerId", "type": "int32", "versions": "0+",
      "about": "The controller id." }
  ]
}
```

이미지 계층도 같이 따라간다. `ClusterDelta.replay(UnregisterControllerRecord)`가 `changedControllers`에 `Optional.empty()`를 넣어 해당 컨트롤러를 다음 `MetadataImage`에서 빼고, 그 결과 이후 스냅샷에도 포함되지 않는다.

```java
public void replay(UnregisterControllerRecord record) {
    changedControllers.put(record.controllerId(), Optional.empty());
}
```

RPC 경로는 브로커 등록 해제와 같은 모양이다. `UnregisterControllerRequest`(API key 94)는 브로커와 컨트롤러 양쪽 리스너에서 받고, 브로커로 들어오면 `forwardToController(request)`로 액티브 컨트롤러에 전달된다. `ApiKeys` 등록도 `clusterAction=false, forwardable=true`다.

```scala
// core/.../KafkaApis.scala
case ApiKeys.UNREGISTER_BROKER => forwardToController(request)
case ApiKeys.UNREGISTER_CONTROLLER => forwardToController(request)
```

권한은 Cluster 리소스의 `Alter`이고(`authHelper.authorizeClusterOperation(request, ALTER)`), 이는 `UnregisterBroker`와 동일한 수준이다.

자기 자신에 대한 호출은 `QuorumController`에서 막는다.

```java
return appendWriteEvent("unregisterController", context.deadlineNs(),
    () -> {
        if (nodeId == controllerId) {
            throw new InvalidRequestException("Controller cannot unregister itself while it is active.");
        }
        return clusterControl.unregisterController(controllerId);
    },
    EnumSet.noneOf(ControllerOperationFlag.class));
```

통합 테스트 `KRaftClusterTest.testUnregisterControllerError`가 이 두 에러를 실제로 확인한다. 등록되지 않은 ID에는 `ControllerIdNotRegisteredException`, 액티브 컨트롤러 자신의 ID에는 `InvalidRequestException`이 돌아온다.

## 설계 결정 및 트레이드오프

### 변경 후 구조

```
[변경 후] 메타데이터 계층에 등록 해제 경로가 생긴다

  운영자
    │  kafka-metadata-quorum.sh remove-controller --controller-id 3 \
    │       --controller-directory-id <dir> --unregister
    ▼
  ┌──────────────────────────────────────────────────────────────┐
  │ MetadataQuorumCommand.handleRemoveController()                │
  │   1) admin.removeRaftVoter(3, dirId)   → Raft 계층            │
  │   2) admin.unregisterController(3)     → 메타데이터 계층       │
  └──────────────────────────────────────────────────────────────┘
        │                                    │
        │ RemoveRaftVoter                    │ UnregisterController (key 94)
        │                                    │  (브로커 수신 시 forwardToController)
        ▼                                    ▼
  ┌───────────────────┐            ┌──────────────────────────────┐
  │ KafkaRaftClient   │            │ QuorumController              │
  │  VotersRecord     │            │  nodeId == 3 ? InvalidRequest │
  │  {1, 2, 4}        │            │  ClusterControlManager        │
  └───────────────────┘            │   .unregisterController(3)    │
                                   └──────────────────────────────┘
                                                │
                                                ▼
                                   UnregisterControllerRecord(3)
                                                │ replay
                                                ▼
                            controllerRegistrations = { 1, 2, 4 }
                            ClusterDelta: 3 → Optional.empty()
                                                │
                                                ▼
                            reasonNotSupported() 가 3번을 더 이상 보지 않음
                                  → MetadataVersion 업그레이드 통과

  ※ 3번 노드가 아직 살아 있으면 ControllerRegistrationManager 가
     registeredInLog=false 를 감지하고 다시 등록한다 (의도된 안전장치)
```

### 왜 별도 레코드와 새 MetadataVersion인가

KIP-1312가 검토하고 기각한 대안들을 보면 선택 이유가 분명하다.

- **KRaft가 등록까지 관리하게 한다**: 기각. KRaft는 로그를 복제하는 계층이지 메타데이터 계층의 멤버십 개념을 관리하는 계층이 아니다. 앞서 본 두 계층의 분리를 KRaft 쪽으로 무너뜨리는 방향이다.
- **옵저버 등록을 영속화하지 않는다**: 기각. 옵저버 엔드포인트를 영속화해야 하는 KIP-1141의 개선을 막는다.
- **`UnregisterBrokerRecord`를 재사용한다**: 기각. 혼합 버전 배포에서 안전하지 않고, 구버전 소프트웨어를 깨뜨릴 위험이 있다. 실제로 구버전 노드는 컨트롤러 ID를 브로커 ID로 해석하게 된다.
- **등록 해제를 비영속(non-durable)으로 만든다**: 기각. 노드 재시작이나 MetadataVersion 갱신 때 등록이 되살아나 UX가 혼란스러워진다.
- **피처 업그레이드 시 자동 등록 해제**: 기각. 업그레이드 UX를 불필요하게 복잡하게 만든다.

새 MetadataVersion `IBP_4_4_IV2`를 요구하는 것은 혼합 버전 클러스터에서 구버전 컨트롤러가 해석할 수 없는 레코드를 만나지 않게 하기 위한 표준 절차다. `isControllerUnregistrationSupported()` 가드가 액티브 컨트롤러 쪽에서 이를 강제한다.

```java
public boolean isControllerUnregistrationSupported() {
    return this.isAtLeast(MetadataVersion.IBP_4_4_IV2);
}
```

여기서 받아들인 트레이드오프는 명확하다. 이미 찌꺼기 등록 때문에 업그레이드가 막힌 클러스터는, 그 등록을 지우려면 먼저 MetadataVersion을 올려야 하는데 그 업그레이드 자체가 막혀 있다. KIP-1312 Compatibility 절은 이 순환을 인정하고, 기존 클러스터는 대체 컨트롤러 프로비저닝 같은 우회책을 쓰라고 안내한다. 즉 이 기능은 이미 발생한 문제의 소급 해결책이 아니라 앞으로의 재발 방지책이다.

### 재등록을 막지 않는 선택

가장 눈에 띄는 결정은 등록 해제가 노드를 영구히 차단하지 않는다는 점이다. 등록 해제된 컨트롤러가 아직 살아 있으면 `ControllerRegistrationManager`가 `registeredInLog = false`를 감지하고 곧바로 다시 등록한다. PR은 이를 명시적으로 테스트한다.

```scala
// core/.../ControllerRegistrationManagerTest.scala, testReRegistrationAfterUnregister
doMetadataUpdate(image, manager, MetadataVersion.IBP_4_4_IV2,
  _ => None,
  r => if (r.controllerId() == 1) Some(r) else None
)
assertFalse(registeredInLog(manager))
// The local manager should send a new registration RPC.
assertEquals((true, 0, 0), rpcStats(manager))
```

KIP은 이를 실수로 인한 제거를 막기 위한 성질로 설명한다. 대가는 운영 절차가 두 단계로 고정된다는 것이다. 문서도 이 순서를 못 박는다.

> The controller must be removed from the voter set (and should be shut down) first

`kafka-metadata-quorum.sh remove-controller --unregister`가 보터 제거와 등록 해제를 한 명령으로 묶어주기는 하지만, 노드 프로세스를 내리는 것은 여전히 운영자 몫이다. 살아 있는 노드에 등록 해제를 걸면 잠깐 사라졌다가 다시 나타난다.

### 두 단계를 한 명령으로 묶되, 실패는 분리해서 안내한다

`MetadataQuorumCommand`는 두 호출을 순차로 실행하면서 각 단계의 실패를 구분해 처리한다. 보터 제거 단계에서 `UnsupportedVersionException`이나 `VoterNotFoundException`이 나면(이미 보터가 아닌 경우 등) 등록 해제만 따로 하라고 안내한다.

```java
} catch (ExecutionException e) {
    Throwable cause = e.getCause();
    if (unregister && (cause instanceof UnsupportedVersionException ||
        cause instanceof VoterNotFoundException)) {
        throw new TerseException("Failed to remove KRaft voter " + controllerId
            + ": " + cause.getMessage()
            + ". To unregister the controller from the cluster, run "
            + "`kafka-cluster.sh unregister-controller --id "
            + controllerId + "`.");
    }
    throw e;
}
```

두 계층을 하나의 명령으로 감싸되 원자적 연산인 척하지는 않는 방식이다. 중간에 실패하면 남은 절반을 어떻게 직접 실행하는지 알려주고 끝낸다. 별도의 롤백이나 재시도 로직은 없다.

### 확인 필요

KIP-1312 본문은 `UnregisterControllerRequest`의 API key를 93으로 적고 있으나, 머지된 `UnregisterControllerRequest.json`과 `docs/security/authorization-and-acls.md`는 모두 94다. 실제 코드 기준은 94이며, KIP 본문이 갱신되지 않은 것으로 보인다.

## 더 살펴볼 점

- `reasonNotSupported()`가 보터 집합이 아니라 등록 맵을 순회하는 설계는 그대로 남았는데, 등록 해제라는 수동 절차 대신 판정 대상 자체를 좁히는 선택지는 왜 논의되지 않았을까.
- KIP-1141이 옵저버 엔드포인트를 영속화하면 옵저버 등록과 보터 등록의 수명 주기 차이가 더 벌어질 텐데, 그때도 같은 수동 등록 해제 모델로 충분할까.
- 등록 해제 후 살아 있는 노드가 재등록하는 동작을 운영자가 알아채기 쉬운 신호(로그, 메트릭)가 지금 충분한가.

## 참고 자료

- [Apache Kafka PR #22191: KAFKA-20395: Support unregistering controllers](https://github.com/apache/kafka/pull/22191) (Apache License 2.0)
- [머지 커밋 c274a734](https://github.com/apache/kafka/commit/c274a7348fb66d1bde99566bdb072fbb52b03dfd) (Apache License 2.0)
- [KIP-1312: Support unregistering controllers](https://cwiki.apache.org/confluence/spaces/KAFKA/pages/406623954/KIP-1312+Support+unregistering+controllers) (Apache 프로젝트 공식 설계 문서)
- [`ClusterControlManager.java` @ c274a734](https://github.com/apache/kafka/blob/c274a7348fb66d1bde99566bdb072fbb52b03dfd/metadata/src/main/java/org/apache/kafka/controller/ClusterControlManager.java) (Apache License 2.0)
- [`FeatureControlManager.java` @ c274a734](https://github.com/apache/kafka/blob/c274a7348fb66d1bde99566bdb072fbb52b03dfd/metadata/src/main/java/org/apache/kafka/controller/FeatureControlManager.java) (Apache License 2.0)
- [`ControllerRegistrationManager.java` @ c274a734](https://github.com/apache/kafka/blob/c274a7348fb66d1bde99566bdb072fbb52b03dfd/server/src/main/java/org/apache/kafka/server/controller/ControllerRegistrationManager.java) (Apache License 2.0)
- [`docs/operations/kraft.md` @ c274a734](https://github.com/apache/kafka/blob/c274a7348fb66d1bde99566bdb072fbb52b03dfd/docs/operations/kraft.md) (Apache License 2.0)
