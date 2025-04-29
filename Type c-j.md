check.php
```php
    <?php
    // 10 길이의 랜덤 문자열 생성(숫자,영대소문자로 구성됨)
    function getRandStr($length = 10) {
        $characters = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
        $charactersLength = strlen($characters);
        $randomString = '';
    
        for ($i = 0; $i < $length; $i++) {
            $randomString .= $characters[mt_rand(0, $charactersLength - 1)];
        }
        return $randomString;

    }
    require_once('flag.php');
    error_reporting(0);
    $id = getRandStr(); // 길이 10의 랜덤 문자열
    $pw = sha1("1"); // 356A192B7913B04C54574D18C28D46E6395428AB
    // POST request
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
      $input_id = $_POST["input1"] ? $_POST["input1"] : "";
      $input_pw = $_POST["input2"] ? $_POST["input2"] : "";
      sleep(1);

      if((int)$input_id == $id && strlen($input_id) === 10){ // int형 10자리여야 함 & Type Confusion 취약점 발생
        echo '<h4>ID pass.</h4><br>';
        if((int)$input_pw == $pw && strlen($input_pw) === 8){ // int형 8자리여야 함 & Type Confusion 취약점 발생
            echo "<pre>FLAG\n";
            echo $flag;
            echo "</pre>";
          }
        } else{
          echo '<h4>Try again.</h4><br>';
        }
      }else {
      echo '<h3>Fail...</h3>';
     }
    ?> 
    </div> 
</body>
</html>
```

### Type Confusion 취약점
- 변수가 원래 예상된 자료형과 다른 자료형으로 처리되는 경우
- 의도하지 않은 코드 실행, 우회, 정보 노출 등이 발생할 수 있음

---

if((int)$input_id == $id && strlen($input_id) === 10) 이 부분을 보면, $input_id를 강제로 int 형변환하고 그것을 $id와 비교하고 있다.  
PHP는 == 연산자에서 자동으로 타입 변환(type juggling)을 수행하기 때문에 $id도 int 타입으로 변환되어 비교된다.

<br>
  
그리고 PHP에서 (int)abcd1234 = 0이고, (int)1234abcd = 1234이다.  
그래서 getRandStr()로 문자열 생성 시 문자가 먼저 올 확률이 더 크므로 $input_id는 0000000000으로 입력한다.

---

https://php.365ok.co.kr/migration80.incompatible.php  
위 링크에 따르면 php 7.x.x 버전에서는 42 == "42foo"라고 한다.  
(php 8.0.x 버전에서는 false)  

<br>

따라서 해시값 356A192B7913B04C54574D18C28D46E6395428AB은 356aasdf와 비교하여 True가 된다.  
id, pw를 각각 입력하면 플래그를 얻을 수 있다.  
Try again이 뜨는 경우도 있는데 getRandStr() 생성 시 숫자가 먼저 온 경우이므로 몇 번 더 해보면 플래그가 나온다.  

<br>

---

### Type Confusion 취약점 방지 방법
1. == 연산자 대신 === 연산자를 사용하여 엄격한 비교를 수행, 자동 형변환 방지(===는 값과 타입을 모두 비교한다)
2. 타입을 명시적으로 확인하거나, 사용자 입력이 예상한 형식인지 체크
3. 타입이 엄격한 언어를 사용(ex. Rust, Go)
4. 타입 안전성 보장하는 라이브러리 사용(ex. PHP의 filter_var())

