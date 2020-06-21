---
layout: post
title: segmentTree
date: 2020-06-21 21:00:00
tags: segmentTree 세그먼트트리
description: 트리, 구간 합, 세그먼트 트리
---

# 구간 트리 : 구간에 대한 질문 대답하기
- 저장된 자료들을 적절히 전처리해 그들에 대한 질의를 빠르게 대답할수 있는것

~~~
A = {1,2,1,2,3,1,2,3,4}
에서 구간이 주어졌을때, 최소 값을 구한다고 했을때,

[2.4] 라면, 최소치는 1이다.
계산하기 위해서는 O(n)
해당 배열을 전처리 하여, 구간 트리를 생성하면 같은 연산을 빠르게 구현할 수 있다.
~~~

- 기본 Idea : 주어진 배열의 구간들을 표현하는 이진트리를 만드는 것. 이대 구간 트리의 루트는 항상 전체 구간을 표현해야한다.
- 루트의 자식 두개는, 왼쪽 반, 오른쪽 반을 표현한다.

~~~
[0                                                14]
[0                       7][8                     14]
[0          3][4         7][8         11][12      14]
[0   1][2   3][4   5][6  7][8   9][10 11][12  13][14]
[0][1][2][3][4][5][6][7][8][9][10][11][12][13][14]

이렇게 되어있을때, [6 12] 구간의 질의를 구하려면, [6 7] [8 11] [12 12] 의 합으로 구할 수 잇다.
- 순차적으로 구할 때 : 7
- 구간을 이용할 때 : 3
-> O(n) -> O(log n)
~~~

## 구간 트리의 표현
- 구간 트리는 꽉찬 이진 트리 이기 때문에, 노드를 사용하는것 보다 배열을 사용하는것이 메모리상 이득,
- 루트 노드를 1번
- i 노드의 left = 2*i
- i 노드의 right= 2*i+1
- 일차원 배열로 표현 가능하다.

- 배열의 길이 : n 이상의 2의 거듭제곱 *2
- ex) n = 6 -> 8*2 = 16
- 귀찮은 경우, 4n으로 하면 된다.


## 구간 트리의 초기화
- 배열의 구간 최소 값 문제를 해결하는 초기화
{% highlight java %}
class RMQ{
    //배열의 길이
    int n;

    int rangeMin[];

    public RMQ(int []array){
        n = array.size();
        rangeMin = new int[array.length];
        init(array,0,n-1,1);
    }
//array  : 구간의 원래 값,
//left : 왼쪽 인덱스
//right : 오른쪽 인덱스
//node : left ~ right의 질의 값을 저장할 노드
    int init(int []array, int left,int right, int node){

        if(left == right) return rangMin[node]  = array[left];

        int mid = (left + right) /2;
        int leftMin = init(array, left, mid, node *2);
        int rightMin = init(array, mid+1, right, node * 2 +1);
        return rangeMin[node] = min(leftMin, rightMin);
    }

}
{% endhighlight %}

- 초기화 과정의 시간 복잡도 O(n)

## 구간 트리의 질의 처리
- 구간 트리의 순회를 응용해 간단하게 구현할 수 있습니다.

~~~
query(left, right, node, nodeLeft, nodeRight)
    = node가 표혆하는 [nodeLeft, nodeRight]에서 내가 원하는 left~right의 교집합의 최소 원소
~~~

- 교집합이 공집합인 경우: 찾을 수 없음
- 교집합이 [nodeLeft, nodeRight] 인 경우 : 그대로 미리 계산해둔 값 리턴
- 이외의 경우 : 두개의 자손 노드를 탐색하며, 더 작은 값을 택해 반환

{% highlight java %}

int query(int left, int right, int node, int nodeLeft, int nodeRight){
    //구간이 겹치지 않은 경우 무시
    if(right < nodeLeft || nodeRight < left) return null;

    //left, right를 완전히 포함하는 경우
    if(nodeLeft <= left && right <= nodeRight)
        return rangeMin[node];

    int mid = (nodeLeft + nodeRight) /2;
    return min(query(left, right, node*2, nodeLeft, mid),
                query(left,right, node*2+1, mid,nodeRight));
}

//외부에서 호출한다고 했을때, 전체 구간에서 left, right 의 값을 구함
int query(int left, int right){
    return query(left, right, 1, 0, n-1);
}

{% endhighlight %}



## 구간 트리의 갱신
- 값이 하나 바뀔 때는 빠르게 갱신 가능
- i 번째 값만 바뀌었다고 했을때, 이 위치를 포함하는 트리는 O(log n),

{% highlight java %}

int update(int index, int newValue, int node, int nodeLeft, int nodeRight){
    // index 가 노드가 표현하는 구간과 상관 없는 경우 무시
    if(index< nodeLeft || nodeRight < index){
        return rangeMin[node];
    }

    if(nodeLeft == nodeRight) retrun rangeMin[node] = newValue;

    int mid = (nodeLeft + nodeRight) /2;

    return rangeMin[node] = min (update(index, newValue, node*2, nodeLeft, mid),
                                update(index, newValue, node*2+1, mid+1, nodeRight));
}

int update(int index, int newValue){
    return update(index, newValue, 1, 0, n-1);
}

{% endhighlight %}