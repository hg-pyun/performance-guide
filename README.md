# performance-guide
javascript 개발시 성능 최적화에 대한 팁들을 정리해 놓은 가이드입니다. 이 문서는 grammar, convention, shorthand 등 전반적인 내용을 다룹니다.

#### [short-hand] || 연산자
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

