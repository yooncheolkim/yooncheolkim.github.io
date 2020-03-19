---
layout: post
title: "리플렉션으로 DI 구현"
date: 2020-03-12 21:52:00
tags: java
description: java DI 만들어보기
---

## 리플렉션

### 리플렉션의 시작은 Class\<T>
- 모든 클래스가 로딩되고, Class\<T>의 인스턴스가 생긴다.
- 타입.class 로 접근가능하다.
- 모든 인스턴스는 .getClass()를 가지고 있다. 이걸로도 접근가능하다.
- Class.forName("type") 으로도 가져올수 있다. 없으면 Exception
- 변수, 메소드, 상위클래스, 어노테이션, 인터페이스 가져올수 있다.

### 어노테이션과 리플렉션
- @Retention : 어노테이션 언제까지 유지할까요, 소스? 클래스? 런타임?

{:.filename}
{% highlight java %}
@Retention(RetentionPolicy.RUNTIME)
public @interface BookService{
}
{% endhighlight %}

- @Inherit : 해당 애노테이션을 하위 클래스에 전달할것인가?
- @Target : 어디에 사용할 수 있는가?
</br></br>

- getAnnotations() : 상속받은 애노테이션까지 조회
- getDeclaredAnnotations() : 자기 자신에만 붙어있는 애노테이션 조회


### reflection 으로 DI 구현하기

>Java
{:.filename}
{% highlight java %}
public class ContainerService {

    public static <T> T getObject(Class<T> classType){
        T instance = createInstance(classType);
        Arrays.stream(classType.getDeclaredFields()).forEach(f ->{

            if(f.getAnnotation(Inject.class) != null){
                Object fieldInstance= createInstance(f.getType());
                f.setAccessible(true);
                try {
                    f.set(instance,fieldInstance);
                } catch (IllegalAccessException e) {
                    throw new RuntimeException(e);
                }
            }
        });
        return instance;
    }

    private static <T> T createInstance(Class<T> classType){
        try{
            return classType.getConstructor(null).newInstance();
        } catch (InstantiationException | IllegalAccessException | NoSuchMethodException | InvocationTargetException e) {
            throw new RuntimeException(e);
        }
    }
}
{% endhighlight %}


{:.filename}
{% highlight java %}
@Retention(RetentionPolicy.RUNTIME)
public @interface Inject {
}
{% endhighlight %}