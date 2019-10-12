---
title: BOJ 1525_PuzzleProblem
layout: page
date: 2018-02-04 20:00
permalink: test
---


:smile: 안녕하세요. <br>본 글은 [BOJ](http://www.icpc.me)에 수록된 [퍼즐](http://www.icpc.me/1525) 문제 풀이를 위한 글 입니다.<br>



<!--more-->
[boj 1525](http://www.icpc.me/1525)는 숫자퍼즐 게임을 가장 최소의 움직임으로 완성하는 문제입니다.<br><br>
![숫자퍼즐](http://smmedic.co.kr/data/editor/sn17210.jpg){:height="50%" width="50%"}
> [http://smmedic.co.kr/data/editor/sn17210.jpg](http://smmedic.co.kr/data/editor/sn17210.jpg)

해당 문제는 가로, 세로가 각각 3씩 할당된 공간에서 숫자를 상하좌우 움직여 오름차순 순서로 배치된 완성모양을 찾아야 합니다.<br>
단순히 9!번(1,451,520)의 탐색을 통해 문제를 해결해도 time out에 걸리지 않을 수 있으므로, BFS를 이용해 문제를 풀어낼 수 있습니다.<br>
하지만 문제를 풀기 위해서는 완전탐색을 해야하므로 모든 퍼즐 모양을 탐색할 필요가 있는데, 이때에는 메모리의 가용량을 고려하여 모든 퍼즐 모양을 기억하기 보다는 bitmask 혹은 string의 변환을 통해 좀 더 쉽게 문제를 풀 수 있습니다. 메모리 가용량을 최소화하기 위해서 두 가지 정도의 기법이 고려될 수 있는데, 문자열의 활용과 bitmask 기법의 활용입니다.<br>
본 글에서는 bitmask 기법보다 쉽게 구현할 수 있는 문자열을 활용하여 문제를 풀이 하겠습니다.<br>
<br>
이 풀이방법은 먼저, 입력으로 들어온 퍼즐모양을 문자열로 변환하여 저장합니다.<br>
<br>
1 &nbsp;&nbsp;&nbsp; 2 <br>
3 4 5  =>  "102345678" <br>
6 7 8 <br>
<br>
그 후 "0"(퍼즐 상 빈공간)의 위치를 BFS를 통해 상하좌우(퍼즐 모양 상의 상하좌우)로 근접한 숫자와 swap하며 완성된 퍼즐모양의 문자열인 "123456780"과 같을 때까지 완전탐색하는 방식을 취하고 있습니다.<br>
<br>

구현상 주의할 점들은 첫번째, 기존 BFS와 달리 방문한 node의 check를 위하여 array나, vector를 공간상의 문제로 사용하지 않고, set을 이용한 점입니다.
> STL set은 find하여 원소를 찾았을 때, 찾으면 iterator를 반환하며, 찾기 못하였을 경우 set의 end()를 반환합니다. 

두번째, while 문이 반복될 때마다, queue의 size를 전부 돌며 "0"의 위치를 이동시켜보아야 하므로, 다음 코드의 q_size를 for문 안에 넣어 queue size가 for문 내부에서 pop() 될 경우 size의 변화가 일어나 오작동을 하는점에 주의합니다.
{% highlight c++ %}
while(!q.empty())
{
    int q_size = q.size();
    for(int i = 0 ; i < q_size; ++i)
    {
        string curr_state = q.front();
        q.pop();
        ...
{% endhighlight %}
세번째, 퍼즐에서는 3x3 2차원 배열을 이용하지만, 구현상에서는 문자열을 이용하므로, d_row, d_col, d_zero("0"의 위치를 변화시키는 값 후보)의 서로간의 순서에 주목해야합니다.<br>
아래의 소스코드에서는 d_row, d_col이 우, 좌, 하, 상 순서로 변화하므로, d_zero 역시 "102345678"같은 형태의 문자열에서 우("0"의 위치값이 +1 변화하여 "120345678"), 좌("0"의 위치값이 -1 변화하여 "012345678"), 하("0"의 위치값이 +3 변화하여 "142305678"), 상("0"의 위치값이 -3 변화하여 "172345608") 방식으로 움직이게 됩니다.

{% highlight c++ %}
#include <cstdio>
#include <algorithm>
#include <string>
#include <queue>
#include <set>
using namespace std;
int d_zero[4] = {1, -1, 3, -3};
int d_row[4] = {0, 0, 1, -1};
int d_col[4] = {1, -1, 0, 0};

int main()
{
	string start_state = "";
	string dest_state = "123456780";
	int n, ans = 0;
	for(int i = 0 ; i < 9; ++i)
	{
		scanf("%d", &n);
		start_state += n + '0';
	}
	set<string> check;
	check.insert(start_state);
	queue<string> q;
	q.push(start_state);

	while(!q.empty())
	{
		int q_size = q.size();
		for(int i = 0 ; i < q_size; ++i)
		{
			string curr_state = q.front();
			q.pop();
			if(curr_state == dest_state)
			{
				printf("%d\n", ans);
				return 0;
			}
			
			int pos0 = 0;
			for(; curr_state[pos0] != '0'; pos0++)
				;
			int curr_row = pos0/3;
			int curr_col = pos0%3;

			for(int j = 0; j < 4; ++j)
			{
				int next_row = curr_row + d_row[j];
				int next_col = curr_col + d_col[j];
				if(next_row < 0 || next_col < 0 || next_row >= 3 || next_col >= 3)
					continue;
				string next_state = curr_state;
				swap(next_state[pos0], next_state[pos0 + d_zero[j]]);

				if(check.find(next_state) == check.end())
				{
					check.insert(next_state);
					q.push(next_state);
				}
			}
		}
		ans++;
	}
	printf("-1\n");
	return 0;
}
{% endhighlight %}