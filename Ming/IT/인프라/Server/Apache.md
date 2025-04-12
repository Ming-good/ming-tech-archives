## Apache 서버란?
 1.  웹 서버로, 웹브라우저에서 요청된 정적인 컨텐츠(HTML 파일, 이미지 파일)를 제공
 2.  Apache는 모듈형 구조를 가지고 있어서 다양한 추가 기능을 제공하며, PHP와 같은 스크립트 언어를 직접적으로 지원
 3.  C언어로 작성되었으며 Apache는 운영 체제의 프로세스로서 직접 실행됨.

## MOD_JK 설치 (WAS-WEB 커넥터)
	1. MOD_JK 다운로드후 아파치 폴더에 모듈 폴더로 이동
	2. httpd.conf  설정
		1. jk_module을 불러오기 (LoadModule)
		2. mod_jk 설정 파일 위치 정하기 (JKWorkersFile)
		3. 로그 지정 (JkLogFile)
		4. 로그 레벨 지정 (JkLogLevel)
		5. 어떤 URL을 MOD JK에서 처리할지 (JkMount)
	3. workers.properties 설정 (부하분산 할 인스턴스 정의)
		1.  워커 지정 (통신 타입 설정, HOST 지정, 포트 지정) 
		2.  로드밸러싱 타입 지정 (worker.load.type = lb)
	4.  톰캣 설정
		1. server.xml 커넥터 사용
		2. jvmRoute 설정 필수 
			1. ![[Pasted image 20250412192421.png]]
	

