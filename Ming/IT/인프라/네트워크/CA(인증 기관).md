
## CA의 역할
- **인증 기관** 웹사이트, 이메일 주소, 회사, 개인 등의 신원을 확인하고 전자 문서 발행을 통해 이들을 암호화 키에 바인딩하는 역할을 하는 회사 또는 조직입니다

## 인증서 체인
- **tistroty.com의 인증서**는 그 상위 인증서인 **Thawte TLS RSA CA G1의 인증기관(Intermediate CA)의 비밀키로 암호화** 된 것이며,

- **Thawte TLS RSA CA G1 인증서**는 그 상위 인증서인 **DigiCert Global Root G2의 인증기관의 비밀키로 암호화** 된 것입니다.

- **DigiCert Global Root G2**는 **상위 인증기관이 없는** **Root CA**이기 때문에 **Self-Signed** 되어 있어요.
 ![[Pasted image 20240910151438.png]]