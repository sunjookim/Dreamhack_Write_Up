app.js
```javascript
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');

const nano = require('nano')(`http://${process.env.COUCHDB_USER}:${process.env.COUCHDB_PASSWORD}@couchdb:5984`);
const users = nano.db.use('users');
var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

/* GET home page. */
app.get('/', function(req, res, next) {
  res.render('index');
});

/* POST auth */
app.post('/auth', function(req, res) { 
    users.get(req.body.uid, function(err, result) {
        if (err) {
            console.log(err);
            res.send('error');
            return;
        }
        // 사용자 입력 값을 검증하지 않고 그대로 사용하고, 평문 패스워드를 저장하고 있는 취약점 존재
        // upw 값은 검사하면서 id 값은 검사하지 않고 있음
        if (result.upw === req.body.upw) { 
            res.send(`FLAG: ${process.env.FLAG}`); // 비밀번호가 일치하면 FLAG 호출
        } else {
            res.send('fail');
        }
    });
});

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```

<br/>
### _all_docs
CouchDB의 _all_docs는 데이터베이스 내의 모든 문서의 메타데이터(ID, revision 등)를 조회할 수 있는 내장(view) API이다.

id 값을 검사하지 않기 때문에, uid=admin으로 하고 pw를 입력하지 않은 채로 요청하면 undefined 결과가 나온다.

result를 undefined로 하고, 실제로 요청에서도 pw 파라미터 전달을 빼버리면 undefined가 된다.(버프슈트 이용)

이렇게 하여 요청하면 플래그 값을 얻을 수 있다.


