접속해보면 내 ip가 나온다.

문제에서 주어진 소스코드를 살펴보면, run() 함수를 이용하여 시스템 명령을 실행하고 있다.
```
    user_ip = request.access_route[0] if request.access_route else request.remote_addr
    try:
        result = run(
            ["/bin/bash", "-c", f"echo {user_ip}"],
            capture_output=True,
            text=True,
            timeout=3,
        )
```

### X-Forwarded-For 헤더
HTTP 헤더 중 하나로, 주로 프록시 서버나 로드 밸런서를 통해 클라이언트 요청이 전달될 때 원본 클라이언트의 IP 주소를 추적하기 위해 사용된다.
→ 여러 개의 서버나 프록시 서버를 거쳐서 클라이언트의 요청이 서버에 도달할 때, 실제 클라이언트의 IP 주소를 알 수 있도록 해준다.

여러 개의 프록시를 거친 경우, 이 헤더는 IP 주소들의 리스트 형태로 전달된다.
```
X-Forwarded-For: client, proxy1, proxy2, ...
```

이를 이용해서 버프슈트로 X-Forwarded-For 헤더 값을 보내면 플래그 값을 얻을 수 있다.
```
X-Forwarded-For: ;cat /flag;
```

### X-Forwarded-For 헤더 변조 방지법
1. 실제 요청을 전달하는 프록시 서버에서만 X-Forwarded-For 헤더를 설정하도록 해야 하며, 그 외의 서버나 사용자 요청에서는 X-Forwarded-For 헤더를 무시하거나 검사
2. 서버에서 요청을 받을 때, X-Forwarded-For 헤더의 첫 번째 IP 주소만 신뢰하고, 나머지 IP들은 무시하거나 검증
3. 만약 리스트의 첫 번째 IP가 외부에서 전달된 프록시 서버의 IP 주소라면, 이를 허용된 프록시 목록과 비교하여 유효성을 검사
4. 서버가 요청을 받았을 때, 클라이언트 IP와 X-Forwarded-For 헤더의 IP 주소를 비교하여 일치하지 않으면 경고를 발생시키거나 요청을 차단
5. Web Application Firewall (WAF) 사용
6. 세션 관리나 타임스탬프 기반으로 검증
