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
X-Forwarded-For: client, proxy1, proxy2, ...
