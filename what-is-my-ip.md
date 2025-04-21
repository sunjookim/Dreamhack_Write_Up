접속해보면 내 ip가 나온다.

문제에서 주어진 소스코드를 살펴보면, run() 함수를 이용하여 시스템 명령을 실행하고 있다.
'''
        result = run(
            ["/bin/bash", "-c", f"echo {user_ip}"],  # bash 쉘에서 사용자 IP를 출력하는 명령어 실행
            capture_output=True,  # 명령어 실행의 출력 결과를 캡처
            text=True,  # 출력 결과를 문자열 형태로 받아옴
            timeout=3,  # 3초를 초과하면 타임아웃 발생
        )
'''
