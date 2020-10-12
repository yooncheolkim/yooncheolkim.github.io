---
layout: post
title: "react hooks 1"
date: 2020-10-11 14:51:00
tags: react hooks
description: 리액트 hooks-1
---

# Hooks

- v16.8에 새로 도입된 함수형 컴포넌트에서도 state 관리 가능하도록 다양한 작업을 할수 있도록 해줌.
- 클래스형 컴포넌트의 라이프사이플 느낌?

## useState

```javascript
const [value, setValue] = useState(0);
```

## useEffect

- 컴포넌트가 렌더링될 때마다 특정 작업을 수행하도록 설정하는 hooks
- 클래스형 컴포넌트의 componentDidMount, componentDidUpdate를 합친 형태

```javascript
    function useEffect(effect: EffectCallback, deps?: DependencyList): void;
```

```javascript
const [value, setValue] = useState("");
const [nickname, setNickname] = useState("");

useEffect(() => {
  console.log("렌더링이 완료되었습니다!");
  console.log({
    name,
    nickname
  });
});
```

### useEffect - 마운트 될때만 실행

```javascript
useEffect(() => {
  console.log("마운트될 때만 실행합니다.");
}, []);
```

- 컴포넌트가 처음 나타날때만 실행됨

### useEffect - 특정 값이 업데이트될때 실행

```javascript
useEffect(() => {
  console.log(name);
}, [name]);
```

- [] 배열안에 useState 상태를 넣어주어도 되고, props로 전달받은것을 넣어줘도 된다.

### useEffect - 뒷정리하기

- useEffect 는 기본적으로 렌더링되고 난 직후 실행되며, 두 번째 파라미터에 따라 달라진다.
- 언마운트 혹은 업데이트되기 직전에 어떠한 작업을 수행하고 싶다면 cleanup 함수를 반환해 주어야 한다.

```javascript
useEffect(() => {
  console.log("effect");
  console.log(name);
  return () => {
    console.log("cleanup");
    console.log(name);
  };
});
```

- 위 처럼 하면, 컴포넌트가 나타날때 'effect' 사라질때 'cleanup'
- 언마운트 될때만 함수를 호출하고 싶다면 ,[]); 두번째 파라미넡에 비어있는 배열을 넣으면 된다.

## useReducer

- 리덕스 보고 다시 보기.

```javascript
import React, { useReducer } from "react";

function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { value: state.value + 1 };
    case "DECREMENT":
      return { value: state.value - 1 };
    default:
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });

  return (
    <div>
      <p>
        현재 카운터 값은<b>{state.value}</b>입니다.
      </p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}> +1</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-1</button>
    </div>
  );
};

export default Counter;
```

## useMemo

- 컴포넌트 내부의 연산을 최적화할 수 있다.
- 렌더링 하는 과정에서 특정 값이 바뀌었을 때만 연산을 실행하고, 원하는 값이 바뀌지 않았다면 이전 결과를 다시 사용.

```javascript
import React, { useMemo, useState } from "react";

const getAverage = numbers => {
  console.log("평균값 계산 중..");
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState([]);
  const [number, setNumber] = useState("");

  const onChange = e => {
    setNumber(e.target.value);
  };

  const onInsert = e => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber("");
  };

  const avg = useMemo(() => getAverage(list), [list]);

  return (
    <>
      <input value={number} onChange={onChange}></input>
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => (
          <li key={index}>{value}</li>
        ))}
      </ul>
      <div>
        <b>평균값: </b>
        {avg}
      </div>
    </>
  );
};

export default Average;
```

## useCallback

- 컴포넌트가 리렌더링 될때마다, 새로 만들어진 함수를 사용하게 된다.
- 최적화 해주는것

```javascript
const onChange = useCallback(e => {
  setNumber(e.target.value);
}, []); //컴포넌트가 처음 렌더링 될때만 함수 생성

const onInsert = useCallback(() => {
  const nextList = list.concat(parseInt(number));
  setList(nextList);
  setNumber("");
}, [number, list]); // number, list가 바뀌었을 때만 함수 생성
```

- 함수에서 상태 값을 사용할때 무조건 두번째 인자에 넣어줘야함.

## useRef

- 함수형 컴포넌트에서 ref 사용할 수 있도록 해줌.

```javascript
const inputEl = useRef(null);

const onInsert = useCallback(() => {
  const nextList = list.concat(parseInt(number));
  setList(nextList);
  setNumber("");
  inputEl.current.focuse();
}, [number, list]);

<input value={number} onChange={onChange} ref={inputEl}></input>;
```

- current 값이 실제 엘리먼트를 가리킨다.
