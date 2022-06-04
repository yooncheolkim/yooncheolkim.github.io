---
layout: post
title: react addEventListner 시 주의사항
date: 2022-05-21 11:00
tags: react addEventListener
description: react addEventListener 시 주의사항
---

### react addEventListener

- react에서 input 태그를 사용하여 파일 드래그앤드롭을 구현한다고 했을때 다음과 같이 작성할 수 있다.

```javascript
const dragRef = (useRef < HTMLDivElement) | (null > null);
const [files, setFiles] = useState([]);

const addFile = (e: ChangeEvent<HTMLInputElement> | any): void => {
  const newFiles: File[] = Array.from(
    e.type === "drop" ? e.dataTransfer.files : e.target.files
  );

  setFiles([...files, newFiles]);
};

const handleDrop = (e: DragEvent): void => {
  addFile(e);
};

const initDragEvent = (): void => {
  dragRef.current.addEventlistener("drag", handleDrop);
};

useEffect(() => {
  initDragEvent();
}, []);

<div ref={dragRef}>
  <input type="file" accept={".jpg,.txt"} multiple onChange={onChangeFiles} />
</div>;
```

- 이렇게 했을때, addFile 안에 files는 어떻게 나올까?
- 첫번째 file 드롭할때는, 문제 없다.
- 하지만, 두번째 file 드롭할때는 files는 첫번째에 추가된 file이 보이지 않는다...

#### 이유는?

- files 는 과거의 value를 가지고 있다. 이유는
- 컴포넌트 마운트(useEffect)하면서 등록된 handleDrop은 ref로 가져온 React component가 아닌, 실제 html element event로 등록이 되었다.
- 첫번째 file drop 후, 리렌더 될때는 새로운 메소드가 handleDrop에 할당되지만, 여전히, dragRef.current 'drag' 이벤트에는 기존 메소드가 등록되어있는 상태이다.ㅋ
- 그러므로, 계속 old files를 바라보고 있게된다.
- 이 문제를 해결하려면, rerender 될때마다, 기존 등록된 event listener를 제거하고, 새로 생긴 event listener를 등록해주거나,
- useRef를 사용하여, 해결가능하다.

#### 결론

- html element 의 이벤트 리스너 안에서는 상태값을 정상적으로 가져올수 없다. set은 가능하다.

- 참고 : https://stackoverflow.com/questions/55265255/react-usestate-hook-event-handler-using-initial-state
