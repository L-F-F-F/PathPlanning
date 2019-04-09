# PathPlanning
---
![](https://i.imgur.com/6nvPKi0.jpg)

| 类型        	| 例子          	| 完备性		|最优性		|
| ------------- |:-------------:|:-----:	|:--:		|
| 基于搜索		| A* , Dijkstra	|完备		|最优		|
| 基于采样	    | PRM , RRT		|概率完备	|非最优(RRT* 渐进最优)|
| 基于启发 		| 遗传 ， 蚁群	|完备		|非最优		|

## 1 Dijkstra 

迪杰斯特拉算法，单源最短路径问题，无向图，权值都为正

设 **G=(V,E)** 是一个带权有向图，把图中顶点集合V分为两个集合，**S** ，已求出最短路径的顶点集合（初始时 **S** 中只有一个源点 **v** ，以后每求得一条最短路径 , 就将加入到集合 **S** 中，直到全部顶点都加入到 **S** ）。**U** 未确定最短路径的顶点集合，按照最短路径，把 **U** 中的添加到 **S** ，保证从 **v** 到 **S** 中各点最短路径长度不大于从源点 **v** 到 **U** 中任意点距离。此外，每个顶点对应一个距离， **S** 中的顶点的距离就是从 **v** 到此顶点的最短路径长度， **U** 中的顶点的距离，是从 **v** 到此顶点只包括 **S** 中的顶点为中间顶点的当前路径的最短长度。
![](https://i.imgur.com/hV75yqs.jpg)
![](https://i.imgur.com/QQ9eMJH.jpg)

## 2 A* ##
启发式搜索，能找到最短路径，快

介绍的博客，[一](https://blog.csdn.net/zhulichen/article/details/78786493)，[二](https://blog.csdn.net/denghecsdn/article/details/78778769)，[三](https://blog.csdn.net/dazhushenxu/article/details/77833023) ,博客动图 

![image](https://i.imgur.com/3d0KvPf.gif)

思路跟Dijkstra相近，两个集合分别存放已经搜寻的点和未完成搜寻的点，在选择下一个搜索点的时候， **f(n) = g(n) + h(n)** ， g(n)表示从初始结点到任意结点n的代价，h(n)表示从结点n到目标点的启发式评估代价。如果说 h(n)=0，那么f(n) = g(n)，A* 退化为 Dijkstra，如果 h(n) 非常大，远大于 g(n)，那么 f(n) = h(n) ，演变为 最佳优先搜索（BFS）。

所以说启发式函数 h(n) 至关重要。如果满足： **h(n) <= 从n移动到目标的实际代价** ，则 A\*保证能找到一条最短路径。h(n)越小，A\*扩展的结点越多，运行就得越慢。

网格地图中常用距离 h(n)： D 系数  
曼哈顿距离：h(n) = D * (abs ( n.x – goal.x ) + abs ( n.y – goal.y ) )  
对角线距离：h(n) = D * max(abs(n.x - goal.x), abs(n.y - goal.y))  
欧氏距离：h(n) = D * sqrt((n.x-goal.x)^2 + (n.y-goal.y)^2)  
欧式距离平方：h(n) = D * ((n.x-goal.x)^2 + (n.y-goal.y)^2)

A*算法和 Dijkstra 都需要维护两个表，open，closed。首先需要查找节点是否可扩展，也就是需要查找节点是否在close表中，频繁查找，降低复杂度，于是利用python封装好的set数据结构进行closed表的存储，set是使用 **红黑树** 实现的，因此查询速度很快。每次都要从open中找最小的节点，节点少的话遍历 O(N) 就行，如果节点数非常多，可以考虑使用 **二叉堆** 维护，每次调整堆 O(log N)。

## 3 PRM ##
随机路图（Probabilistic Road Maps，PRM）就是在规划空间内随机选取N个节点，之后连接各节点，并去除与障碍物接触的连线，由此得到一个随机路图。显然，当采样点太少，或者分布不合理时，PRM算法是不完备的，但是随着采用点的增加，也可以达到完备。所以PRM是概率完备且不最优的。

![image](https://i.imgur.com/zlp3E86.gif)

## 4 RRT ##
快速扩展随机树法,是基于树状结构的搜索算法，RRT算法是从起始点开始向外拓展一个树状结构，而树状结构的拓展方向是通过在规划空间内随机采点确定的。与PRM类似，该方法是概率完备且不最优的。

![image](https://i.imgur.com/FtMbhki.gif)