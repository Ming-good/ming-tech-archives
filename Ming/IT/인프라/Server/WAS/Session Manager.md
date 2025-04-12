
| Manager           | 클러스터 | 저장 위치   | 복제 방식      | 장점             | 단점               |
| ----------------- | ---- | ------- | ---------- | -------------- | ---------------- |
| StandardManager   | ❌    | 메모리/디스크 | 없음         | 단순하고 빠름        | 장애 시 세션 손실       |
| PersistentManager | ❌    | 디스크/DB  | 없음         | 재시작 시 세션 유지 가능 | 느림, I/O 부담       |
| DeltaManager      | ✅    | 메모리     | 전체 세션 복제   | 설정 간편, 자동 복제   | 서버 많아지면 성능 하락    |
| BackupManager     | ✅    | 메모리     | 선택된 노드에 복제 | 성능, 확장성 적절히 균형 | 백업 노드 고장 시 문제 가능 |

## 1. 🟧 **StandardManager** 
#### (Tomcat의 기본 세션 관리자)

```<Manager className="org.apache.catalina.session.StandardManager" />```
### ✔️ 동작 방식
- 세션을 **메모리(RAM)**에 저장
- Tomcat 종료 시, 세션을 디스크에 직렬화해서 저장하고
- 재시작하면 다시 복원
### ✅ 장점
- 구성 간단 (기본값)
- 성능 빠름 (메모리 기반)
### ❌ 단점
- 클러스터 환경에서는 세션 공유 ❌
- Tomcat이 죽으면 **세션은 손실됨**
- 서버 간 failover 시 로그인 상태 유지 불가

---
## 2. 🟦 **PersistentManager** 
#### (세션을 파일 시스템이나 DB 등 **영구 저장소에 저장**)
```
<Manager className="org.apache.catalina.session.PersistentManager">     
	<Store className="org.apache.catalina.session.FileStore" /> 
</Manager>
```
### ✔️ 동작 방식
- 사용하지 않는 세션을 **디스크나 DB 등에 저장** (passivation)
- 요청 오면 다시 메모리로 올림 (activation)
### ✅ 장점
- 서버 재시작해도 세션 보존 가능
- 메모리 효율적 (idle session 저장소로 이동)
### ❌ 단점
- 디스크/DB I/O → **성능 저하**
- 클러스터 세션 공유는 직접 구현 필요

---
## 3. 🟩 **DeltaManager**
#### (**Tomcat 클러스터** 환경에서 쓰는 대표 세션 복제 방식)
`<Manager className="org.apache.catalina.ha.session.DeltaManager" />`
### ✔️ 동작 방식
- 세션에 변화가 생기면, **모든 Tomcat 노드에 전체 복제**
- `DeltaManager`라고 하지만 실제로는 **전체 세션을 복제함** (full replication)
### ✅ 장점
- 설정 간단하고, 기본적으로 클러스터에서 failover 가능
- 세션 자동 복제
### ❌ 단점
- 서버 수 늘어나면 복제량도 커져서 **스케일링에 불리**
- 성능 부담 큼 (전체 복제 → 네트워크 병목)
---
## 4. 🟥 **BackupManager**

#### (클러스터 환경에서 **선택된 백업 노드 1~2곳에만 복제**하는 방식)

`<Manager className="org.apache.catalina.ha.session.BackupManager" />`
### ✔️ 동작 방식
- 세션에 변화 생기면, **몇 개의 백업 노드에만 복제**
- 델타만 보내는 방식이 가능 (변경된 부분만 전송)
- failover 시 백업 노드에서 세션 복원
### ✅ 장점
- `DeltaManager`보다 **복제 오버헤드 작음**
- 성능과 확장성 모두 어느 정도 균형 잡힘
### ❌ 단점
- 백업 노드 고장 시 failover 안 될 수도 있음
- 구성 복잡도 약간 있음