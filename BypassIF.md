app.py
```
# 취약한 md5 알고리즘으로 해싱되어있다.
KEY = hashlib.md5(FLAG.encode()).hexdigest()
guest_key = hashlib.md5(b"guest").hexdigest() # 084e0343a0486ff05530df6c705c8bb4

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

@app.route('/flag', methods=['POST'])
def flag():
     # POST request
    if request.method == 'POST':
        # /flag 페이지에 대해 POST 요청 시, key, cmd_input 값을 파라미터로 받는다.
        key = request.form.get('key', '')
        cmd = request.form.get('cmd_input', '') 
        if cmd == '' and key == KEY:
            return render_template('flag.html', txt=FLAG)
        elif cmd == '' and key == guest_key:
            return render_template('guest.html', txt=f"guest key: {guest_key}")
        if cmd != '' or key == KEY:
            if not filter_cmd(cmd):
                try:
                    output = subprocess.check_output(['/bin/sh', '-c', cmd], timeout=5)
                    return render_template('flag.html', txt=output.decode('utf-8'))
                except subprocess.TimeoutExpired:
                    return render_template('flag.html', txt=f'Timeout! Your key: {KEY}')
                except subprocess.CalledProcessError:
                    return render_template('flag.html', txt="Error!")
            return render_template('flag.html')
        else:
            return redirect('/')
    else: 
        return render_template('flag.html')
```
