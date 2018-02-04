---
layout: post
date: 2018-01-20 19:00
title: Union Find
categories: 
- Algorithm
- UnionFind
---


:wink:안녕하세요. <br>이 글은 Algorithm 스터디를 위한 글 입니다.<br><br>
Union Find는 Disjoint Set이라고도 불리는 자료구조입니다. <br>



<!--more-->
본 자료구조는 두 집합의 관계(서로소인지의 여부 등)를 알아보기 위하여 주로 사용되며, Union과 Find라는 연산을 정의하고 사용합니다. 먼저 Find 연산은 tree 형태로 구성된 집합의 root를 찾는 역할을 수행합니다. 각 원소는 처음에 자기 자신을 root로 가지고 있다가 merge를 수행하면 원소들이 속해진 집합이 합쳐지며 하나의 root를 갖게 됩니다.<br>
{% highlight c++ %}
int find(int u)
{
    if(u == parents[u])
        return u;
    return parents[u] = find(parents[u]);
}
{% endhighlight %}
찾고자 하는 원소 u가 root인 경우 parents 배열의 u번째 원소 또한 u이므로 자기자신을 반환합니다.<br>
하지만 그렇지 않을경우, 자신의 parent의 root를 재귀적으로 탐색해 나감으로써, 자기가 속한 집합의 root를 찾을 수 있습니다.<br><br>

또한, Union 연산은 두 집합(tree)을 병합하는 역할을 수행합니다. Union은 예약어로 사용되기에 merge 혹은 union_set 등 변형된 이름으로 함수를 정의하곤 합니다. <br>
{% highlight c++ %}
void merge(int u, int v)
{
    u = find(u);
    v = find(v);
    if(u == v)
        return;

    parents[u] = v;
}
{% endhighlight %}
merge 연산은 find연산을 사용하여 구현하는데, 먼저 합치고자 하는 집합 u와 v의 root를 find 연산을 통해 찾은 후,<br>
root가 동일할 경우 합칠 필요가 없으므로, 종료합니다. 하지만 그렇지 않을경우, u의 root를 v로 설정함으로써, 해당 집합을 병합할 수 있습니다.<br><br>
본 구현에서는 tree의 depth 등은 고려되지 않았습니다.<br><br>

이를 활용한 문제로는 [boj 1717](https://icpc.me/1717) 와 [boj 1976](https://icpc.me/1976)이 있습니다. <br><br>

[boj 1717](https://icpc.me/1717) 본 문제는, 앞서 배운 Union Find를 이용해, 간단하게 구현할 수 있습니다.<br>
1) 문제에 사용될 수 있는 각 원소의 root를 자기자신으로 지정합니다.<br>
2) 0 연산시 merge 함수를 사용하여 Union 연산을 수행합니다.<br>
3) 1 연산시 find 함수를 통해 두 원소의 root를 비교한 후 동일하면 YES, 아니면 NO를 출력합니다.<br>

{% highlight c++ %}
#include <cstdio>
#define MAX 1000000
int n,m;
int parents[MAX];
int find(int u)
{
    if(u == parents[u])
        return u;
    return parents[u] = find(parents[u]);
}
void merge(int u, int v)
{
    u = find(u);
    v = find(v);
    if(u == v)
        return;
    parents[u] = v;
}
int main()
{
    scanf("%d %d", &n, &m);
    for(int i = 0 ; i < MAX; ++i)
        parents[i] = i;
    while(m--)
    {
        int a,b,c;
        scanf("%d %d %d", &a, &b, &c);
        if(a == 0)
            merge(b,c);
        else{
            if(find(b) == find(c))
                printf("YES\n");
            else
                printf("NO\n");
        }
    }
    return 0;
}
{% endhighlight %}