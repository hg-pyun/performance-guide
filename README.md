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

#### array.length
```javascript
// loop를 돌때마다 array 참조가 일어남.
var i;
for (i = 0; i < arr.length; i++){}

// 아래와 같이 구현하는게 성능에 더 좋습니다.
var i;
var l = arr.length;
for (i = 0; i < l; i++) {}
```

#### innerHtml
```javascript
// 루프를 돌때마다 reflow, repaint가 일어납니다.
// (특
for(var i=0; i<100; i++){
    document.getElementById("list").innerHTML += "<li>list</li>";
}

// innerHtml 횟수는 최소한으로 줄이는게 성능에 더 좋습니다.
let list = '';
for(var i=0; i<100; i++){
    list += "<li>list</li>";
}

document.getElementById("list").innerHTML = list;

```
#### try ~ catch
```javascript
try ~ catch 구문안에 있는 코드들은 컴파일러가 최적하지 못합니다.
따라서 성능에 민감한 작업들은 함수로 한번 감싸주세요.

function perf_sensitive() {
  // 성능에 민감한 작업을 여기에서 처리합니다.
}

try {
  perf_sensitive()
} catch (e) {
  // 예외를 여기에서 처리합니다.
}
```
#### array type
Array의 type이 변경되면 성능 저하가 발생하므로 type이 변경되지 않도록 하는것이 좋습니다.
또 Typped Array를 사용하는 것이 성능에 더 좋습니다.
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
#### array, object 할당
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

## Refnerence
- https://www.sitepoint.com/shorthand-javascript-techniques/
- https://www.html5rocks.com/ko/tutorials/speed/v8/