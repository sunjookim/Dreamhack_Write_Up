check.php
```php
    <?php
    // 62 길이의 랜덤 문자열 생성(숫자,영대소문자로 구성됨)
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
    $id = getRandStr(); 
    $pw = sha1("1"); // 356A192B7913B04C54574D18C28D46E6395428AB
    // POST request
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
      $input_id = $_POST["input1"] ? $_POST["input1"] : "";
      $input_pw = $_POST["input2"] ? $_POST["input2"] : "";
      sleep(1);

      if((int)$input_id == $id && strlen($input_id) === 10){ // getRandStr() 결과와 같아야 하고, int형 10자리여야 함
        echo '<h4>ID pass.</h4><br>';
        if((int)$input_pw == $pw && strlen($input_pw) === 8){ // sha1("1") 결과와 같아야 하고, int형 8자리여야 함
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
