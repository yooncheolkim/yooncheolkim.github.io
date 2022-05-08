---
layout: post
title: spring only changes?
date: 2022-05-08 20:30
tags: spring put patch
description: spring 변경점만 보내고 싶을때
---

### fe에서 변경점만 보내고 싶다고 하면?

- fe form에서 임시저장한 form data를 조회해서, 사용자가 변경한 부분만 골라서, api call한다고 했을때, 서버에서는 어떻게 처리해야하나

#### nodejs에서는...

```javascript
data : {
name: 'yooncheol',
address: 'cheonju'
}
```

- 위 처럼 request body json data가 온다고 했을때, js에서는 json field가 undefined인지, 아예 field가 없는지 확인 할 수 있다.
- fe에서 변경되지 않은 field는 delete를 해서, 아예 보내지 않고, 변경된 field는 undefined/null/value를 선택하여 보내면 그대로 서버에서 처리하면된다.

```javascript
data : {
name: 'yooncheol',
address : undefined,
};

if(data.address === null){
// 빈값으로 세팅하는 것
}
else if(!data.address){
// field가 존재하지 않음. 변경하지 않는것
}
else if(data.address){
// value 존재, 변경
}
```

#### java/spring 에서는..?

- spring controller에서는 class로 requestbody를 class로 받아야한다.

```java
  @PatchMapping('')
  public void patchMemberInfo(@RequestBody MemberInfo memberInfo){
  ...
  }

class MemberInfo{
String name;
String address;
Integer age;
}
```

- spring에서는 json 데이터를 MessageConverter를 이용하여 자동 json request body를 class 형태로 convert해준다.
- 하지만, class로 변경되면서, 없는 field는 자연스럽게 null로 세팅된다.
- java에는 undefined가 없기 때문에, 필드가 존재하지 않는것과 필드가 null로 세팅된것을 구분할 방법이 없어진다.
- string의 경우는 null(필드 없음) / ''(빈값으로 세팅) / 'value'(값있음) 으로 구분하면 문제될게 없다.
- 하지만! 숫자형의 경우, 빈값이라는것을 표현할 수 없다. 0이 빈값은 아니니깐. 그렇다고 db의 default값이 빈값? 여러가지 애매모호한 상황들이 생긴다.
- 그렇다면 이렇게 spring에서 변경된 값만 받아서 처리하려면 어떻게 해야할까?

#### java/spring에서 처리 방법

- 여러 가지 방법이 있다.

  1. 특정 숫자(like. -2,147,483,648)를 빈값으로 정의하고 이것을 fe와 규약으로 사용한다.
     1번은 굉장히 이상한 방법이다.
     계속 투입되는 개발자는 의문이 생길거고, 계속 설명해줘야한다.

  2. 모든 숫자형태를 default 값을 정하고 사용한다.
     비즈니스가 어떻게 바뀔지 모르는 상황에서 이 방법 또한 이상하다.

  3. spring controller에서 map<String,Object>로 받는다.
     controller에서 map으로 받을 경우, fe에서 보낸 body fields만 받을 수 있다.
     가장 이상적인 방법이다...

#### 3번으로 했을때, 문제점

- 3번 방법으로 결정하고 개발을 진행하면서 겪은 많은 문제점? 수고로움이 있다.

##### map을 사용했을때 수고로움 1

- map을 사용하게 되면, 결국 비즈니스 로직을 처리하기 위해, dto or vo로 변환이 필요하고, map에 존재하는 값만 해당 dto or vo로 넣어줘야한다.
- 단순 primitive wrapper type 혹은 String이면 문제 없다.
- 하지만 비즈니스가 복잡해질수록, map/dto/vo의 깊이는 깊어진다.
- map to (dto or vo) 를 하기 위해, 각 필드마다 변환해줘야하는 convert 로직을 작성해야한다.

```java
class Dto{
    String name;
    String address;
    List<Comment> comments;
    List<Order> orders;
}
```

- Dto class 처럼 Comment/Order 와 같은 class의 경우 map to dto convert 로직을 작성해줘야한다.
- 타입에 맞춰서, 하위 class 형태를 맞춰서 convert해야하기 때문에 이 변환 로직은 굉장히 복잡할 것이다.
- class depth가 깊어지며 깊어질수록, 더욱 복잡해진다.

##### map을 사용했을때 수고로움 2

- 프로젝트에서 api 문서화를 위하여, swagger를 사용하고 있었다.
- map<String,Object> 으로 request body를 받고 있기 때문에, swagger에서 해당 map을 제대로 표현하지 못한다...

```java
  @PatchMapping('')
  public void patchMemberInfo(@RequestBody Map<String,Object> changes){
  ...
  }
```

- swagger에게 해당 map은 어떤 DTO class를 표현하고 있는것이라고 알려줘야한다.
- swagger config 설정시에 dto 패키지 하위에 있는 class들을 모두 swagger의 schema로 등록해줘야했다.

```java
@Configuration
@EnableWebMvc
public class SwaggerConfig{

    @Bean
    public Docket api(TypeResolver typeResolver){
        // some jobs.
        Set<Class> dtoClasses = findAllClasses("package.dto");

        Interator<Class> iterator = dtoClasses.iterator();

        while (iterator.hasNext()){
            docket.additionalModels(typeResolver.resolve(iterator.next()));
        }

    return docket;
    }

}
```

#### 결론

- 처음에는 fe와 be 사이의 필요한 최소한의 데이터만 주고 받아야지! 라고 생각하여 변경된 값만 fe에서 보내야지 라고 생각하였다.
- spring에서는 이런 과정이 너무 많은 공수와 노력을 들게 한다.
- 다음에는 "fe에서 변경된 값 + 기존에 있는 값 모두 다 보내주세요." 라고 하자.
