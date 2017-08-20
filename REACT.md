# React Performance Optimization
이 문서는 React에서 적용할 수 있는 최적화 기법에 대해 다룹니다

#### state를 Immutable하게 만들어 비교
일반적인 React 최적화 기법에서는 shouldComponentUpdate함수를 사용해서 비교합니다.
```javascript
// eaxample
shouldComponentUpdate(nextProps, nextState){
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
}
```
이 때, props나 state가 객체일 경우, 객체가 변화했을 때를 감지하려면 deep compare를 하는방법밖에 없습니다.
deep compare는 상당히 무거운 작업입니다. 따라서 이를 좀 더 빠르게 하기 위해선 주소값 연산을 사용합니다.
따라서 imuutable한 object라면 !== 를 이용해서 주소값을 비교해주면 되기 때문에 속도에 있어서 성능 향상이 이루어질 수 있습니다.

#### render함수 내에서는 오브젝트를 생성하는 것을 피하는게 좋습니다.
랜더링이 될때마다 메모리가 재할당되기 때문입니다.
```javascript
// bad
render() {
  return 
    <div>
         <Menu item={['category1', 'category2', 'category3', 'category4']} />
    </div>
}

// good
const categorys = ['category1', 'category2', 'category3', 'category4'];
render() {
  return 
    <div>
         <Menu item={categorys} />
    </div>
}
```

#### bind event는 render 함수 밖에서 호출합니다.
bind를 호출할 때마다 새로운 함수가 할당되기 때문입니다.
```javascript
// bad
render() {
  return
    <div>
        <SomeButton onClick={this.someEvent.bind(this))} />
    </div>
}    

// good
this.someEvent = this.someEvent.bind(this);
render() {
  return
    <div>
        <SomeButton onClick={this.someEvent} />
    </div>
}    

```