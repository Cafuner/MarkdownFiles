# 欧拉路径

**欧拉路径：如果图G中的一个路径包括每个边恰好一次，则该路径称为欧拉路径(Euler path)。**

**欧拉回路：如果一个回路是欧拉路径，则称为欧拉回路(Euler circuit)。
具有欧拉回路的图称为欧拉图（简称E图）。具有欧拉路径但不具有欧拉回路的图称为半欧拉图。**

## 存在条件

**一、无向图
1 存在欧拉路径的充要条件 : 度数为奇数的点只能有0或2个
2 存在欧拉回路的充要条件 : 度数为奇数的点只能有0个
二、有向图
1 存在欧拉路径的充要条件 : 要么所有点的出度均=入度；要么除了两个点之外，其余所有点的出度=入度 剩余的两个点:一个满足出度-入度=1(起点) 一个满足入度-出度=1(终点)
2 存在欧拉回路的充要条件 : 所有点的出度均等于入度**



在一个半欧拉图（欧拉图）中找欧拉路径（欧拉回路），可以使用Hierholzer算法。

Hierholzer 的基本思想是首先找到一个子回路，并逐步将其他回路合并到该子回路中，最终形成完整的欧拉回路。该证明被后人整理成 Hierholzer 算法，用于在已经判定无向图的欧拉回路存在的前提下，找出一条欧拉回路。算法流程如下：

1. （寻找子回路）从任意非零度节点u出发，沿着边遍历图。在遍历过程中，删除经过的边。如果遇到一个所有边都被删除的节点，那么该节点必然是 u，即我们找到了一个包含 u 的回路。将该回路上的节点和边添加到结果序列中。
2. （检查是否存在其它回路）检查刚刚添加到结果序列中的节点，看是否还有与节点相连，且未遍历的边。如果发现节点 u有未遍历的边，则从 u 出发重复步骤 1，找到一个包含 u 的新回路，将结果序列中的一个 u 用这个新回路替换。此时结果序列仍然是一个回路，只不过变得更长了。
3. （结束条件）重复步骤 2，直到所有边都被遍历。此时结果序列中的节点和边就构成了欧拉回路。算法结束。

在上面的算法中，如果是在半欧拉图中找欧拉路径，那么步骤一在第一次遍历时一定会停在欧拉路径的终点，这样也能找出欧拉路径。

在下面的例子中，每一个节点由字符串标识，求出的是字典序最小的欧拉路径。

```c++
class Solution {
public:
    unordered_map<string, priority_queue<string, vector<string>, greater<string>>> edges;
    
    void hierholzer(string cur, vector<string>& stk) {
        while(edges.count(cur) > 0 && edges[cur].size() > 0) {
            string branch = edges[cur].top();
            edges[cur].pop();
            hierholzer(branch, stk);
        }
        stk.push_back(cur);
    }

    vector<string> findItinerary(vector<vector<string>>& tickets) {
        vector<string> stk;
        for(vector<string>& ticket : tickets) {
            string& from = ticket[0];
            string& to = ticket[1];
            edges[from].push(to);
        }

        hierholzer("JFK", stk);

        reverse(stk.begin(), stk.end());
        return stk;
    }
};
```



