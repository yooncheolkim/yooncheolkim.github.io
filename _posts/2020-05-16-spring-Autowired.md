---
layout: post
title: "spring Autowired"
date: 2020-05-16 14:30:00
tags: spring
description: spring Autowired
---


# spring Autowired


## 생성자에 위치 시키기

{%highlight java%}
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
}


@Repository
public class BookRepository {
}
{% endhighlight %}

- 잘 동작한다...
- 만약 BookRepository에 @Repository 를 하지 않아 bean등록이 되어 있지 않다면, BookService 빈 등록시 Inject 할수 없기 때문에 오류 발생,,


## setter에 위치시키기
{%highlight java%}
@Service
public class BookService {

    BookRepository bookRepository;
    
    @Autowired
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}

public class BookRepository {
}
{% endhighlight %}

- 위 코드 처럼, BookRepository에 bean등록 annotation이 없어도, BookService 만드는데는 문제 없어야 하는거 아닌가?
- 에러 발생함. Autowired(required = false)  로 하면, 오류 없이 잘 동작함.
- 기본적으로 @Autowired(required = true)기 때문,,, 


## 필드에 위치 시키기
{%highlight java%}
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;
}

@Repository
public class BookRepository {
}
{% endhighlight %}



# 해당 타입의 빈이 여러개인 경우...

{%highlight java%}
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;
}

@Repository
public class MyBookRepository implements BookRepository{
}

@Repository
public class YourBookRepository implements BookRepository{
}

@
{% endhighlight %}

- 빈이 두개 일 경우, 주입 하지 못한다.. 둘중 어떤걸 주입 받을지 모름
1. @Primary : 여러가지 BookRepository 중에 한가지를 주입 받도록 해줌
2. @Qualifier("MyBookRepository") : 주입 받는쪽에 설정,
3. List<BookRepository> bookRepositories : 여러개를 다 받아라

