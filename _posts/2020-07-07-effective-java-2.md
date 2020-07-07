---
layout: post
title: 이펙티브 자바 빌더 패턴
date: 2020-07-07 22:00:00
tags: java
description: 이펙티브 자바 item 2
---

# 생성자에 매개변수가 많다면 빌더를 고려하라

## 점층적 생성자 패턴
- 필드가 여러개가 있고, 생성자에서 해당 필드를 선택적으로 받아서 객체를 생성한다고 할때,
- 선택적으로 받는 매개변수의 갯수 마다 생성자를 따로 만들어줘야한다.

~~~java
public NutritionFacts(int servingSize, int servings){
}
public NutritionFacts(int servingSize, int servings, int calories){
}
public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium){
}
public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate){
}
~~~

- 너무 많은 생성자가 발생한다.

## 자바 빈즈 패턴
- setter를 이용하여, 필드를 초기화 해준다.
~~~java
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(100);
cocaCola.setServing(200);
cocaCola.setSodium(450);
~~~
- 이렇에 하면,메서드를 여러개 호출해야 한다.
- 객체가 완전히 생성되기 전에 해당 객체를 사용하면, 일관성이 무너진 상태에 놓여, 버그를 유발할 수 있다.
- 자바빈즈 패턴에서는 클래스를 불변(아이템 17)으로 만들 수 없으며, 스레드 안전성을 얻으려면 프로그래머의 추가 작업이 필요하다.
- 객체 생성이 끝나면 freeze 하고, 얼리기 전에 사용할 수 없도록 할 수 있지만, 다루기 어려워서 사용되지 않는다.

## 대안, 빌더 패턴
~~~java
// 코드 2-3 빌더 패턴 - 점층적 생성자 패턴과 자바빈즈 패턴의 장점만 취했다. (17~18쪽)
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }
        public Builder fat(int val)
        { fat = val;           return this; }
        public Builder sodium(int val)
        { sodium = val;        return this; }
        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }
}
~~~
- 클라이언트는 필수 매개변수만으로 생성자 혹은 정적 팩터리를 호출해 빌더 객체를 얻는다.
- 이후 선택전 매개변수를 빌더를 반환하는 메서드를 이용하여, 설정한다.
- 이후 build하여, 최종 얻고자하는 객체를 뽑아낸다.

### 빌더패턴의 불변성
- 빌더 패턴과 자바 빈즈 패턴의 가장 큰 차이점은 불변성이다.
- 자바 빈즈 패턴은 객체를 생성한 후, setter를 사용하여 필드 값을 넣는다.
- 빌더 패턴은, 객체 생성전, builder를 사용하여 값을 넣고, build 메서드를 사용하여 객체를 사용한다.
- 그렇기 때문에, 빌더 패턴 사용시 해당 객체에는 public setter를 절대 만들면 안된다.


### 빌더패턴 장단점

#### 장점
- 클라이언트가 작성하기 쉽고, 가독성도 높아진다.(메서드 체인, fluent API)
- 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.(https://github.com/WegraLee/effective-java-3e-source-code/tree/master/src/effectivejava/chapter2/item2/hierarchicalbuilder)

#### 단점
- 빌더 부터 만들어야 하기 때문에, 성능에 문제가 발생 할 수 있다.
- 인자가 충분히 많을때 효과적이다.
- 앞으로 인자가 늘어날걸 대비해, 애초에 빌더패턴을 사용하는게 나을수 있다.