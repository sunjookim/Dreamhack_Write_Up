app.py
```
# 취약한 md5 알고리즘으로 해싱되어있다.
KEY = hashlib.md5(FLAG.encode()).hexdigest()
guest_key = hashlib.md5(b"guest").hexdigest()

# filtering
def filter_cmd(cmd):
    alphabet = list(string.ascii_lowercase) # 알파벳 소문자(a부터 z까지)를 포함하는 리스트
    alphabet.extend([' ']) # 리스트에 공백문자 추가
    num = '0123456789'
    alphabet.extend(num) # 리스트에 0~9 추가
    command_list = ['flag','cat','chmod','head','tail','less','awk','more','grep']

    for c in command_list: # command_list 필터링
        if c in cmd:
            return True
    for c in cmd:
        if c not in alphabet: # cmd는 알파벳 소문자, 공백문자, 0~9로만 이루어질 수 있다.
            return True
```
