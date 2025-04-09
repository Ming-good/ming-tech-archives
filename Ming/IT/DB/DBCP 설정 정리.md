|기능|설명|설정 키워드|
|---|---|---|
|**검사 방법**|커넥션이 유효한지 확인할 때 실행할 쿼리|`validationQuery="SELECT 1"`|
|**언제 검사할지**|커넥션 꺼낼 때 검사|`testOnBorrow="true"`|
||커넥션 반환할 때 검사|`testOnReturn="true"`|
||백그라운드에서 유휴 커넥션 검사|`testWhileIdle="true"`|
|**백그라운드 검사 주기**|유휴 커넥션 검사하는 쓰레드 실행 주기 (ms)|`timeBetweenEvictionRunsMillis="30000"`|
|**유휴 커넥션의 최대 수명**|유휴 상태로 얼마나 지나면 제거할지 (ms)|`minEvictableIdleTimeMillis="60000"`|
|**close() 마다 검사할지**|반환 시 검사 → 위와 동일|`testOnReturn="true"`|
|**커넥션 누수 회수**|반환하지 않고 너무 오래된 커넥션 강제 close|`removeAbandoned="true"`|
|**커넥션 누수 시간 기준**|몇 초 이상 반환 안 하면 누수로 간주|`removeAbandonedTimeout="60"`|
|**누수 발생시 로그 남기기**|누수된 커넥션 close 시 스택트레이스 출력|`logAbandoned="true"`|
