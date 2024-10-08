# [1857. Largest Color Value in a Directed Graph (Hard)](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/)

<p>There is a <strong>directed graph</strong> of <code>n</code> colored nodes and <code>m</code> edges. The nodes are numbered from <code>0</code> to <code>n - 1</code>.</p>

<p>You are given a string <code>colors</code> where <code>colors[i]</code> is a lowercase English letter representing the <strong>color</strong> of the <code>i<sup>th</sup></code> node in this graph (<strong>0-indexed</strong>). You are also given a 2D array <code>edges</code> where <code>edges[j] = [a<sub>j</sub>, b<sub>j</sub>]</code> indicates that there is a <strong>directed edge</strong> from node <code>a<sub>j</sub></code> to node <code>b<sub>j</sub></code>.</p>

<p>A valid <strong>path</strong> in the graph is a sequence of nodes <code>x<sub>1</sub> -&gt; x<sub>2</sub> -&gt; x<sub>3</sub> -&gt; ... -&gt; x<sub>k</sub></code> such that there is a directed edge from <code>x<sub>i</sub></code> to <code>x<sub>i+1</sub></code> for every <code>1 &lt;= i &lt; k</code>. The <strong>color value</strong> of the path is the number of nodes that are colored the <strong>most frequently</strong> occurring color along that path.</p>

<p>Return <em>the <strong>largest color value</strong> of any valid path in the given graph, or </em><code>-1</code><em> if the graph contains a cycle</em>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2021/04/21/leet1.png" style="width: 400px; height: 182px;"></p>

<pre><strong>Input:</strong> colors = "abaca", edges = [[0,1],[0,2],[2,3],[3,4]]
<strong>Output:</strong> 3
<strong>Explanation:</strong> The path 0 -&gt; 2 -&gt; 3 -&gt; 4 contains 3 nodes that are colored <code>"a" (red in the above image)</code>.
</pre>

<p><strong>Example 2:</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2021/04/21/leet2.png" style="width: 85px; height: 85px;"></p>

<pre><strong>Input:</strong> colors = "a", edges = [[0,0]]
<strong>Output:</strong> -1
<strong>Explanation:</strong> There is a cycle from 0 to 0.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>n == colors.length</code></li>
	<li><code>m == edges.length</code></li>
	<li><code>1 &lt;= n &lt;= 10<sup>5</sup></code></li>
	<li><code>0 &lt;= m &lt;= 10<sup>5</sup></code></li>
	<li><code>colors</code> consists of lowercase English letters.</li>
	<li><code>0 &lt;= a<sub>j</sub>, b<sub>j</sub>&nbsp;&lt; n</code></li>
</ul>


**Companies**:  
[Google](https://leetcode.com/company/google)

**Related Topics**:  
[Dynamic Programming](https://leetcode.com/tag/dynamic-programming/), [Topological Sort](https://leetcode.com/tag/topological-sort/)

## Solution 1. Topological Sort

**Intuition**: We can use BFS Topological Sort to visit the nodes. When visiting the next node, we can forward the color information to the next node. Also Topo-sort can help detect circle.

**Algorithm**:

Just do normal topo sort. One modification is that, for each node, we need to store a `int cnt[26]` array where `cnt[i]` is the **maximum** count of color `i` in **all** paths to the current node.

For example, assume there are two paths reaching the current node, `aba`, `bba`. Then `cnt['a'] = 2` and `cnt['b'] = 2` because both color `a` and `b` can be `2` in different paths.

The solution finds the largest color value in a directed graph where each node represents a color. It builds the graph and calculates the indegrees of each node. 
Using a queue, it performs a topological sort, processing nodes with zero indegree first. For each processed node, it updates the color counts of its neighbors, 
keeping track of the maximum color counts along paths. If all nodes are processed, it returns the highest color count; otherwise, it returns -1 if there are cycles preventing full traversal.

```cpp
// OJ: https://leetcode.com/contest/weekly-contest-240/problems/largest-color-value-in-a-directed-graph/
// Author: github.com/lzl124631x
// Time: O(V + E)
// Space: O(V + E)
class Solution {
    typedef array<int, 26> T; // Array to store color counts
public:
    int largestPathValue(string C, vector<vector<int>>& E) {
        unordered_map<int, vector<int>> G; // Graph representation
        vector<int> indegree(C.size()); // Count of incoming edges
        for (auto &e : E) {
            G[e[0]].push_back(e[1]); // Build graph
            indegree[e[1]]++; // Count indegrees
        }
        vector<T> cnt(C.size(), T{}); // Color counts for each node
        queue<int> q;
        
        // Initialize the queue with source nodes
        for (int i = 0; i < C.size(); ++i) {
            if (indegree[i] == 0) {
                q.push(i);
                cnt[i][C[i] - 'a'] = 1; // Start with current color count
            }
        }
        
        int ans = 0, seen = 0;
        while (q.size()) {
            auto u = q.front(); // Current node
            q.pop();
            int val = *max_element(begin(cnt[u]), end(cnt[u])); // Maximum color count
            ans = max(ans, val); // Update answer
            ++seen; // Count nodes seen
            
            // Update color counts for adjacent nodes
            for (int v : G[u]) {
                for (int i = 0; i < 26; ++i) {
                    cnt[v][i] = max(cnt[v][i], cnt[u][i] + (i == C[v] - 'a')); // Update color counts
                }
                if (--indegree[v] == 0) {
                    q.push(v); // Add to queue if all dependencies are resolved
                }
            }
        }
        
        return seen < C.size() ? -1 : ans; // Return -1 if not all nodes are seen
    }
};

```
