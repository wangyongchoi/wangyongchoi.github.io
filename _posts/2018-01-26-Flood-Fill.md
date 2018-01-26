---
layout: post
date: 2018-01-26 17:00
title: Flood Fill
---


:smile: 안녕하세요. <br>이 글은 Algorithm 스터디를 위한 글 입니다.<br><br>
Flood Fill은 다차원 배열에서 인접해있는 영역을 찾는 알고리즘입니다. <br>



<!--more-->
본 알고리즘은 주로 인접한 영역을 색칠하는 용도로 사용됩니다. Flood Fill은 인접한 영역을 탐색하며 현재 영역과 같은 색깔, 상태 등을 확인하고 완전탐색을 수행하며 반복됩니다. 보통 재귀적인 방법으로 구현하는데, 본 글에서는 [BOJ](https://icpc.me/)의 문제를 DFS를 활용하는 예를 이용하여 살펴보겠습니다.
<br><br>
본 글에서 활용할 문제는 [boj 2468](https://icpc.me/2468) 와 [boj 2583](https://icpc.me/2583)이 있습니다. <br>

[boj 2468](https://icpc.me/2468) 본 문제는, 앞서 배운 Flood Fill을 이용해, 구현할 수 있습니다.<br>
1) 물의 높이가 될 수 있는 1부터 100까지 순차적으로 물의 높이를 높여가며 최대 면적을 구합니다.<br>
2) 각 물의 높이에서는 map을 순회하며 현재 물 높이보다 높은 경우 dfs 형태로 구현된 Flood Fill 알고리즘을 수행합니다.<br>
3) dfs가 호출되면 현재 위치부터 상하좌우를 순회하며 현재 물의 높이와 각 칸의 높이를 비교하고 현재 칸의 높이가 더 높을 경우 재귀적으로 dfs를 호출하며 인접한 칸을 완전탐색을 진행합니다.<br>
4) 1부터 100까지 각 물의 높이마다 Flood Fill(dfs)를 진행하며 나온 면적값을 최고값으로 갱신하고 최대 면적을 출력합니다.

{% highlight c++ %}
#include <cstdio>
#include <string.h>
#include <algorithm>
#define MAX 101
using namespace std;
int n;
int cnt = 0 , res = 0;
int map[MAX][MAX];
int copy_map[MAX][MAX];
int d_row[4] = {0, 0, 1, -1};
int d_col[4] = {1, -1, 0, 0};
void dfs(int row, int col, int h)
{
    copy_map[row][col] = 0;
    for(int i = 0; i < 4; ++i)
    {
        int next_row = row + d_row[i];
        int next_col = col + d_col[i];
        if(copy_map[next_row][next_col] > h)
        {
            dfs(next_row, next_col, h);
        }
    }
}
int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; ++i)
    {
        for(int j = 1; j <= n; ++j)
        {
            scanf("%d", &map[i][j]);
        }
    }
    for(int i = 0; i <= 100; ++i)
    {
        memcpy(copy_map, map, sizeof(map));

        for(int j = 1; j <= n; ++j)
        {
            for(int k = 1; k <= n; ++k)
            {
                if(copy_map[j][k] > i)
                {
                    cnt++;
                    dfs(j,k,i);
                }
            }
        }
        res = max(cnt, res);
        cnt = 0;
    }
    printf("%d\n", res);
    return 0;
}
{% endhighlight %}

각 물의 높이마다 Flood Fill을 진행해야 하므로, 기존 map을 copy_map으로 복사하는 연산과, dfs가 호출되었을 때, 방문한 칸의 높이를 0으로 지정함으로써 check 배열없이 구현할 수 있었습니다.

<br><br>

[boj 2583](https://icpc.me/2583) 본 문제 또한 앞서 배운 Flood Fill을 이용해, 구현할 수 있습니다.<br>
1) 입력으로 주어지는 직사각형의 면적을 map에 true로 표시합니다.<br>
2) 다시 map 전체를 순회하면서 map이 false로 되어있거나(직사각형에 포함되지 않은 영역), 방문하지 않은 영역(Flood Fill로 check되지 않은 영역)의 경우 현재 위치를 방문하고 Flood Fill을 시작합니다. <br>
3) dfs 형태로 구현된 Flood Fill이 호출되면 현재 위치부터 상하좌우를 순회하며 직사각형에 포함되지 않은 영역인 경우, 방문하고 칸의 갯수를 늘려가며 재귀적으로 인접한 영역의 완전탐색을 실시합니다.<br>
4) 인접한 영역이 더이상없는 경우 함수는 종료되고 count된 영역을 vector에 저장하고 본 과정들을 반복합니다.<br>
5) 모든 map의 영역이 방문되면, 저장된 vector를 정렬하고 출력합니다. <br>

{% highlight c++ %}
#include <cstdio>
#include <algorithm>
#include <vector>
#define MAX 105
using namespace std;
int n,m,k;
int cnt = 0;
bool map[MAX][MAX];
bool check[MAX][MAX];
int d_row[4] = {0,0,1,-1};
int d_col[4] = {1,-1,0,0};
vector<int> v;
void dfs(int row, int col)
{
    for(int i = 0 ; i < 4; ++i)
    {
        int next_row = row + d_row[i];
        int next_col = col + d_col[i];
        if(next_row < 0 || next_col < 0 || next_row >= n || next_col >= m || check[next_row][next_col])
            continue;
        if(!map[next_row][next_col])
        {
            cnt++;
            check[next_row][next_col] = true;
            dfs(next_row, next_col);
        }
    }
}
int main()
{
    scanf("%d %d %d", &n, &m, &k);
    for(int i = 0; i < k; ++i)
    {
        int lx,ly,rx,ry;
        scanf("%d %d %d %d", &lx, &ly, &rx, &ry);
        for(int j = ly; j < ry; ++j)
        {
            for(int k = lx; k < rx; ++k)
            {
                map[j][k] = true;
            }
        }
    }
    
    for(int i = 0; i < n; ++i)
    {
        for(int j = 0; j < m; ++j)
        {
            if(!map[i][j] && !check[i][j])
            {
                check[i][j] = true;
                cnt = 1;
                dfs(i, j);
                v.push_back(cnt);
            }
        }
    }

    sort(v.begin(), v.end());
    printf("%d\n", v.size());
    for(int i = 0 ; i < v.size(); ++i)
    {
        printf("%d ", v[i]);
    }
    printf("\n");
}
{% endhighlight %}