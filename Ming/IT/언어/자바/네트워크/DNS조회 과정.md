1. 자바는 `InetAddress.getByName("호스트명")` 메서드를 사용해서 해당하는 IP 주소를 조회합니다.
2. 이 과정에서 시스템의 호스트 파일을 먼저 확인한다.
	- `/etc/hosts` (리눅스, mac)
	-  `C:\Windows\System32\drivers\etc\hosts` (윈도우,Windows)
3. 호스트 파일에 정의되어 있지 않다면, DNS 서버에 요청해서 IP 주소를 얻는다.