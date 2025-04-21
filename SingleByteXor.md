#### XOR 암호화
XOR의 특성상, 동일한 값을 두 번 XOR하면 원래 값을 복구할 수 있다. 
즉, A XOR B XOR B = A

따라서
```
54586b6458754f7b215c7c75424f21634f744275517d6d XOR '어떤 단일바이트' = 평문
```

그리고 단일 바이트는 16진수로 0x00 ~ 0xFF 범위 내에 있다.
xor 복호화를 위한 코드는 다음과 같다.

```
def try_all_keys(ciphertext_hex):
    # 16진수 문자열을 바이트로 변환
    ciphertext = bytes.fromhex(ciphertext_hex)

    # 모든 가능한 단일 바이트 키(0-255)에 대해 시도
    for key in range(256):
        decrypted = []
        for byte in ciphertext:
            decrypted.append(byte ^ key)  # XOR 연산

        # 바이트를 문자로 변환
        try:
            result = bytes(decrypted).decode('ascii')
            # 가능한 플래그 형식(DH{...})을 확인
            if 'DH{' in result and '}' in result:
                print(f"키 {key} (0x{key:02x}): {result}")
        except UnicodeDecodeError:
            # 해독 불가능한 결과는 무시
            pass


# 주어진 암호문
ciphertext = "54586b6458754f7b215c7c75424f21634f744275517d6d"
try_all_keys(ciphertext)
```

암호문이 16진수로 이루어져있다는 것을 아는 것과, xor 연산의 성질을 아는 것이 중요했다.
