# performance-guide
javascript 개발시 성능 최적화에 대한 팁들을 정리해 놓은 가이드입니다. 이 문서는 syntax, convention, shorthand 등 전반적인 내용을 다룹니다.

## Syntax

#### || 연산자
|| 연산자를 사용하면 코드량 및 연산 횟수를 줄일 수 있습니다.
```javascript
let result;
if (variable !== null || variable !== undefined || variable !== '') {
    result = variable;
}
else {
    result = 'somthing';
}

// 위 문법은 다음과 같습니다.
const result = variable  || 'somthing';
```

#### && 연산자
||와 마찬가지로 && 연산자를 사용하면 좀더 깔끔한 코드를 작성할 수 있습니다.
```javascript
// someProp이 존재하는지 체크한후 해당 함수 실행
if(someObj.someProp){
    someObj.someProp();
}

// 위 문법은 다음과 같습니다.
someObj.someProp && someObj.someProp();
```

## double exclamation mark
!! 연산자를 사용하면 값의 유무를 판별할 수 있습니다.
```javascript
// 아래 경우를 제외하곤 true를 반환합니다.
!!false === false
!!0 === false
!!"" === false
!!null === false
!!undefined === false
!!NaN === false
```

#### scope 활용
javascript는 객체를 내부 scope부터 외부로 점차적으로 탐색해 나갑니다.
따라서 scope와 지역변수를 잘 활용하면 성능을 향상시킬 수 있습니다.
```javascript
function local(){
    var id1 = document.getElementById("id1");
    var id2 = document.getElementById("id2");
}
 
// 아 구문이 성능이 더 효율적입니다.
function local(){
    var d = document;
    var id = d.getElementById("id1");
    var id2 = d.getElementById("id2");
}
```

#### innerHtml
innerHtml 횟수는 최소한으로 줄이는게 성능에 더 효율적입니다.
```javascript
// 루프를 돌때마다 reflow, repaint가 일어납니다.
for(var i=0; i<100; i++){
    document.getElementById("list").innerHTML += "<li>list</li>";
}

// 아래 구문이 성능이 더 효율적입니다.
let list = '';
for(var i=0; i<100; i++){
    list += "<li>list</li>";
}

document.getElementById("list").innerHTML = list;
```
#### loop syntax
특별한 이유가 없다면 for in, forEach보다 for문을 사용하는게 좋습니다.
```javascript
- for (item in array)
- forEach(function(item){})

// 아래 구문이 성능이 더 효율적입니다.
for(var i=0; i<length; i++){}
```

#### 변수 선언
왼쪽의 문법이 오른쪽 문법보다 효율적입니다.
```javascript
var arr = []; vs var arr = new Array();
arr[i] = i; vs arr.push(i);
var obj = {}; vs var obj = new Object();
obj.a = 1; vs obj["a"] = 1;
var str = "test"; vs var str = new String("test");
```

#### array.length
array 참조 횟수를 최소한으로 줄이는게 좋습니다.
```javascript
// loop를 돌때마다 array 참조가 일어남.
var i;
for (i = 0; i < arr.length; i++){}

// 아래와 같이 구현하는게 성능에 더 효율적입니다.
var i;
var l = arr.length;
for (i = 0; i < l; i++) {}
```

#### array type
Array의 type이 변경되면 성능 저하가 발생하므로 type이 변경되지 않도록 하는것이 좋습니다.
또 Typped Array를 사용하는 것이 성능에 더 효율적입니다.
```javascript
// 아래 배열은 마지막에 string을 넣음으로서 배열 type이 변경됩니다.
var array = new Array(1000);
for(var i=0; i<1000; i++){
    array[i] = i;
}
array[999] = "this is string";

// 아래 코드가 더 성능이 좋습니다.
var array = new Array(1000);

array[0] = "dummy";
for(var i=0; i<1000; i++){
    array[i] = i;
}
array[999] = "this is string"; 
```

#### 문자열 조합
+ 연산자보다 배열을 사용해서 조합하는게 성능에 유리합니다. 다만 최신브라우저의 경우에는 엔진이 최적화를 해주므로 + 연산자를 사용하는것이 유리합니다. (IE8 기준)
```javascript
var str;
for (var i=0; i<10000; i++) {
	str += 'prefix'+i+'suffix';
}

// IE8 이하에서는 아래 코드가 더 효율적입니다.
var array = [];
for (i=0; i<10000; i++) {
	array.push('prefix', i, 'suffix');
}
var str = array.join('');
```

#### array, object 할당
array와 object는 한번에 초기화 및 할당하는 것이 좋습니다.
```javascript
/* array */
var array = new Array();
array[0] = 1;
array[1] = 1.5;

// 아래 코드가 더 효율적입니다.
var array = [1, 1.5];
```
```javascript
/* object */
// var obj = {};
obj.hello = 'hello';
obj.world = 'world';

// 아래 코드가 더 효율적입니다.
var obj = {
    hello : 'hello',
    world : 'world'
}
```
#### Event
Event Binding으로 구현하는 것보다 Event Delegation이 성능이 더 효율적입니다.

#### requestAnimationFrame
시각적 변화를 위해 주기적으로 업데이트가 필요한 경우 setTimeout, setInterval보다 requestAnimationFrame을 사용하게는게 성능에 유리합니다.

### try ~ catch
try ~ catch 구문안에 있는 코드들은 컴파일러가 최적하지 못합니다.
따라서 성능에 민감한 작업들은 함수로 한번 감싸주세요.
```javascript
function perf_sensitive() {
  // 성능에 민감한 작업을 여기에서 처리합니다.
}

try {
  perf_sensitive()
} catch (e) {
  // 예외를 여기에서 처리합니다.
}
```

## HTML
#### script tag
브라우저가 script 태그를 만나면 script 태그가 처리될때까지 body의 로드가 지연됩니다. 따라서 script 태그는 head보다 body밑에 넣는것이 더 효율적입니다.
```
<html>
<head>
    <script>some script</script>
</head>
<body>
    something
</body>
<html>

// 아래 코드가 사용자 입장에서 더 효율적입니다.
<html>
<head>
</head>
<body>
    something
</body>
<script>some script</script>
<html>
```

## Build
#### concat
다수의 script를 따로 배포하는 것보다 하나의 파일로 합친 후에 서빙하는 성능에 유리합니다.
script를 합치기 위해서는 [gulp](http://gulpjs.com/), [grunt](https://gruntjs.com/) 같은 task runner를 사용하거나 [webpack](https://webpack.js.org/) 같은 bundler를 사용하면 좋습니다.

#### minify & uglify
minify는 불필요한 공백을 제거하는 작업이고, uglify는 코드 난독화 및 production에서 불필요한 코드(console.log)를 제거해주는 작업입니다.
해당 작업을 통해 용량을 줄이고 코드를 최적화 할수 있습니다. 대부분의 task runner나 webpack 등을 사용해서 작업할 수 있습니다.