1. 웹 브라우저에 https://naver.com 접속 
2. 로컬에서 OS에따라 PC의 hosts에서 찾아보고, 없으면 브라우저 캐시에 해당 도메인 주소가 있는지 확인
3. 로컬에도 없으면 DNS 서버에서 확인
4. DNS는 GSLB에게 도메인의 IP를 질의
5. GSLB는 CDN이나 웹서버중 가장 상태가 좋은 서버의 IP를 DNS에게 알려주고 DNS는 다시 사용자에게 알려줌
6. 사용자는 IP를 가지고 TCP 3 way hand shacking을 통해 서버와 통신한다. https의 경우 SSL hand shake를 추가로 진행
7. 만약 CDN의 경우 사용자가 원하는 데이터가 없을 경우 네이버 웹서버로부터 최신 파일을 받아 사용자에게 제공
