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

#### double exclamation mark
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
(사실 적은 데이터에서는 실행 속도의 큰 차이는 없습니다.)
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
'+' 연산자보다 배열을 사용해서 조합하는게 성능에 유리합니다. 다만 최신브라우저의 경우에는 엔진이 최적화를 해주므로 '+' 연산자를 사용하는것이 유리합니다. (IE8 기준)
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

#### try ~ catch
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

#### 함수 선언문 vs 함수 표현식
함수 선언문을 사용하면 스코프 최상단으로 [호이스팅](https://perfectacle.github.io/2017/04/26/js-002-hoisting/)되므로 프로그램이 의도적으로 동작하지 않을 수 있고,  
스크립트를 로딩했을 때 [VO(Variable Object)](http://mohwa.github.io/blog/javascript/2015/10/14/vo-inJS/)에 할당하기 때문에 성능상 이슈도 존재합니다.  
```javascript
alert(x); // function x() {}
if(x === undefined) { // 실행되지 않습니다.
  alert('나는 아직 할당되지 않았어!');
}
var x = 2;
function x() {}
alert(x); // 2, 변수 x를 생성하고 할당한 것 이후에 함수 x를 선언했지만 호이스팅에 의해 의도치 않게 동작합니다.
```

위와 같은 이유로 인해 함수 표현식 사용을 **권장**합니다.  
```javascript
alert(x); // undefined
if(x === undefined) { // 드디어 실행됩니다.
  alert('나는 아직 할당되지 않았어!');
}
var x = 2;
x = function(){};
alert(x); // function(){}
```

#### var의 중복 선언
기존 프로그래밍 언어에서는 동일한 변수를 중복 선언하면 에러를 유발했습니다.  
하지만 자바스크립트에서는 오류가 나진 않지만 몇 바이트의 트래픽이라도 아끼려면 var의 중복 선언은 피합시다.
```javascript
var a = 1;
alert(a); // 1
var a = 2; // 그냥 a = 2; 라고 쓰면 된다.
alert(a); // 2
```

혹시 기존 프로그래밍 언어를 하던 사람은 변수의 [스코프](https://perfectacle.github.io/2017/04/27/js-003-scope/)를 당연히 블록(**{}**)이라고 생각하실 겁니다.  
하지만 자바스크립트에서는 함수 단위의 스코프를 가지기 때문에 쓸 데 없이 var의 중복 선언을 하지 않도록 조심합시다.  
```javascript
var sum = 0;
for(var i=0; i<10; i++) {
  sum += i;
}
alert(i); // 10, 함수 단위의 스코프이기 때문에 블록(for 문)에서 선언한 변수는 살아있습니다.
var i = 20; // i = 20; 이라고 적어서 var의 중복 선언을 피합시다.
alert(i); // 20;

// 아래가 함수 단위의 스코프를 보여주는 예제입니다.  
var a = function(b) {
  // 함수 안에서 a는 20
  var a = 20;
  return a + b;
};

// 함수 밖에서 a는 함수입니다.
alert(a);
```

#### 형변환(캐스팅) 연산자 성능
##### Any to Boolean  
* new Boolean(any).valueOf()
* Boolean(any)  
* !!any  
```javascript
var iterations = 10000000;
console.time('new Boolean(any).valueOf()');
for(var i=0; i<iterations; i++){
    new Boolean(1.1).valueOf(); // new Boolean(any).valueOf(): 691.537ms
}
console.timeEnd('new Boolean(any).valueOf()');
console.time('Boolean(any)');
for(i=0; i<iterations; i++){
    Boolean(1.1); // String(any): 27.272ms
}
console.timeEnd('Boolean(any)');
console.time('!!any');
for(i=0; i<iterations; i++){
    !!1.1; // !!any: 27.04ms
}
console.timeEnd('!!any');
```

##### Any to Number
* Number.parseInt(any[, radix])  
* Number.parseFloat(any)  
* new Number(any).valueOf()  
* Number(any)  
* +any, 1*any  
```javascript
var iterations = 10000000;
console.time('Number.parseInt(any)');
for(var i=0; i<iterations; i++){
    Number.parseInt('1.1'); // Number.parseInt(any): 561.062ms
}
console.timeEnd('Number.parseInt(any)');
console.time('Number.parseInt(any) with radix');
for(i=0; i<iterations; i++){
    Number.parseInt('1.1', 10); // Number.parseInt(any) with radix: 511.062ms
}
console.timeEnd('Number.parseInt(any) with radix');
console.time('Number.parseFloat(any)');
for(i=0; i<iterations; i++){
    Number.parseFloat('1.1'); // Number.parseFloat(any): 737.437ms
}
console.timeEnd('Number.parseFloat(any)');
console.time('new Number(any).valueOf()');
for(i=0; i<iterations; i++){
    new Number('1.1').valueOf(); // new Number(any).valueOf(): 1112.782ms
}
console.timeEnd('new Number(any).valueOf()');
console.time('Number(any)');
for(i=0; i<iterations; i++){
    Number('1.1'); // Number(any): 1066.577ms
}
console.timeEnd('Number(any)');
console.time('+any');
for(i=0; i<iterations; i++){
    +'1.1'; // +any: 20.724ms
}
console.timeEnd('+any');
console.time('1*any');
for(i=0; i<iterations; i++){
    1*'1.1'; // 1*any: 21.459ms
}
console.timeEnd('1*any');
```

##### Any to String
* Wrapper Object.prototype.toString()  
* new String(any).valueOf()
* String(any)  
* '' + any  
```javascript
var iterations = 10000000;
console.time('Wrapper Object.prototype.toString()');
for(var i=0; i<iterations; i++){
    1.1.toString(); // Wrapper Object.prototype.toString(): 268.619ms
}
console.timeEnd('Wrapper Object.prototype.toString()');
console.time('new String(any).valueOf()');
for(i=0; i<iterations; i++){
    new String(1.1).valueOf(); // new String(any).valueOf(): 213.537ms
}
console.timeEnd('new String(any).valueOf()');
console.time('String(any)');
for(i=0; i<iterations; i++){
    String(1.1); // String(any): 159.045ms
}
console.timeEnd('String(any)');
console.time('\'\' + any');
for(i=0; i<iterations; i++){
    '' + 1.1; // '' + any: 68.594ms
}
console.timeEnd('\'\' + any');
```

## HTML
#### script tag
브라우저가 script 태그를 만나면 script 태그가 처리될때까지 body의 로드가 지연됩니다. 
따라서 script 태그는 head보다 body밑에 넣는것이 사용자가 view를 더 빨리볼 수 있습니다.
```html
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

#### tree shaking
```javascript
// module.js
export const a = 123123123123;
export const b = 45645646456;

// app.js
import {a} from './module';
console.log(a);
```
위와 같은 코드가 있을 때 쓰지 않는 변수, 불필요한 변수 b를 제거하는 것을 **트리 쉐이킹**이라고 한다.  
나무를 흔들어 썩은 열매를 떨어뜨리는 행위에서 유래한 것 같다.  
[webpack](https://webpack.js.org/)이나 [rollup](https://rollupjs.org/)과 같은 모듈 번들러에서 제공한다.  

#### gzip
기존의 js, css 파일 등등은 위의 과정들을 거쳐 최적화를 시켜도 인터넷 속도가 느린 환경에서는 여전히 제약사항이 된다.  
이를 위해서 해당 asset들을 gzip 압축 알고리즘을 통해 압축을 하면 파일 용량이 훨씬 작아진다.    
이미 압축이 많이 된 이미지나 동영상 등등은 압축을 한다 한들 별 소용이 없다.  
또한 파일 용량이 매우 작은 경우에는 압축을 푸는 시간이 오히려 압축되지 않은 파일을 내려받는 속도보다 느릴 수도 있기 때문에 일정 용량 이상부터 압축을 해야한다.  
이를 위해서는 서버 쪽에서도 설정이 필요하다.

#### caching
HTTP 1.1 표준 스펙에는 하나의 요청 당 하나의 응답, 즉 하나의 파일만이 서비스가 가능합니다.  
요청을 보내고 응답을 받기까지 불필요한 작업들로 인해 오버헤드가 발생합니다.  
따라서 하나의 HTML 파일을 보기 위해 여러 파일(css, js, 이미지 등등)을 받아야하는 단점이 존재합니다.  
그럼 HTML 파일 안에 넣을 수 있는 소스들을 모두 넣어서 요청을 최소화하는 것이 더 좋지 않을까라는 의문점이 들게 되기 마련입니다.  
그에 대한 해법을 찾기 위해 다음과 같은 상황을 가정해봅시다. (로딩 속도도 임의로 가정해서 정확하지 않습니다.)

|                                     | HTML 다운로드 속도 | HTML 해석 속도 | 요청에 따른 오버 헤드 | asset 다운로드 속도 | asset 해석 속도 | 총 로딩 속도 |
|-------------------------------------|--------------------|----------------|-----------------------|---------------------|-----------------|--------------|
| HTML 파일에 전부 존재(처음 접속)    | 2s                 | 1s             | 0s                    | 0s                  | 1s              | 4s           |
| HTML과 asset 파일들 분리(처음 접속) | 1s                 | 1s             | 1s                    | 1s                  | 1s              | 5s           |

페이지를 처음 방문 했을 때는 HTML 파일 하나에 몰아넣은 경우가 더 빠릅니다.  
하지만 다양한 상황에 대해 페이지를 재방문 했을 때는 어떨까요?

* HTML 파일만 수정한 경우

|                                  | HTML 다운로드 속도 | HTML 해석 속도 | 요청에 따른 오버 헤드 | asset 다운로드 속도 | asset 해석 속도 | 총 로딩 속도 |
|----------------------------------|--------------------|----------------|-----------------------|---------------------|-----------------|--------------|
| HTML 파일에 전부 존재(재접속)    | 2s                 | 1s             | 0s                    | 0s                  | 1s              | 4s           |
| HTML과 asset 파일들 분리(재접속) | 1s                 | 1s             | 0s(caching)           | 0s(caching)         | 1s              | 3s           |

* asset 파일만 수정한 경우

|                                  | HTML 다운로드 속도 | HTML 해석 속도 | 요청에 따른 오버 헤드 | asset 다운로드 속도 | asset 해석 속도 | 총 로딩 속도 |
|----------------------------------|--------------------|----------------|-----------------------|---------------------|-----------------|--------------|
| HTML 파일에 전부 존재(재접속)    | 2s                 | 1s             | 0s                    | 0s                  | 1s              | 4s           |
| HTML과 asset 파일들 분리(재접속) | 0s(caching)        | 1s             | 1s                    | 1s                  | 1s              | 4s           |

* HTML 파일과 asset 파일 모두 수정한 경우

|                                     | HTML 다운로드 속도 | HTML 해석 속도 | 요청에 따른 오버 헤드 | asset 다운로드 속도 | asset 해석 속도 | 총 로딩 속도 |
|-------------------------------------|--------------------|----------------|-----------------------|---------------------|-----------------|--------------|
| HTML 파일에 전부 존재(재접속)    | 2s                 | 1s             | 0s                    | 0s                  | 1s              | 4s           |
| HTML과 asset 파일들 분리(재접속) | 1s                 | 1s             | 1s                    | 1s                  | 1s              | 5s           |

위와 같이 파일이 수정되지 않은 경우에는 브라우저의 임시 폴더에 존재하는 파일을 불러오는 **캐싱**을 이용할 수 있습니다.  
하지만 HTML 파일 하나에 모든 소스를 넣어두면 조그만 수정이 있다 하더라도 파일 전체를 새로 내려받아야해서 이런 캐싱의 장점을 이용할 수 없습니다.  
따라서 이런 캐싱의 장점을 살리기 위해 html, css, js 파일은 최대한 분리시켜 놓는 게 좋습니다.  

# Contribute
#### Document convention
가장 큰 카테고리는 '##'을 사용합니다.
카테고리 안에서 소분류는 '####'을 사용합니다.
```
// example
category : ## Javascript
s-category : #### closure
contents : this is closure 
example : use ```  
```

#### pull request
새로운 내용이나 수정은 Pull Request형식으로 보내주시길 바랍니다. 또한 이 문서는 누구나 수정할 수 있으며 재배포시 출처를 꼭 밝혀주시길 바랍니다.
