# [133. Clone Graph (Medium)](https://leetcode.com/problems/clone-graph/)

<p>Given a reference of a node in a <strong><a href="https://en.wikipedia.org/wiki/Connectivity_(graph_theory)#Connected_graph" target="_blank">connected</a></strong> undirected graph.</p>

<p>Return a <a href="https://en.wikipedia.org/wiki/Object_copying#Deep_copy" target="_blank"><strong>deep copy</strong></a> (clone) of the graph.</p>

<p>Each node in the graph contains a value (<code>int</code>) and a list (<code>List[Node]</code>) of its neighbors.</p>

<pre>class Node {
    public int val;
    public List&lt;Node&gt; neighbors;
}
</pre>

<p>&nbsp;</p>

<p><strong>Test case format:</strong></p>

<p>For simplicity, each node's value is the same as the node's index (1-indexed). For example, the first node with <code>val == 1</code>, the second node with <code>val == 2</code>, and so on. The graph is represented in the test case using an adjacency list.</p>

<p><b>An adjacency list</b> is a collection of unordered <b>lists</b> used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.</p>

<p>The given node will always be the first node with <code>val = 1</code>. You must return the <strong>copy of the given node</strong> as a reference to the cloned graph.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2019/11/04/133_clone_graph_question.png" style="width: 454px; height: 500px;">
<pre><strong>Input:</strong> adjList = [[2,4],[1,3],[2,4],[1,3]]
<strong>Output:</strong> [[2,4],[1,3],[2,4],[1,3]]
<strong>Explanation:</strong> There are 4 nodes in the graph.
1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/01/07/graph.png" style="width: 163px; height: 148px;">
<pre><strong>Input:</strong> adjList = [[]]
<strong>Output:</strong> [[]]
<strong>Explanation:</strong> Note that the input contains one empty list. The graph consists of only one node with val = 1 and it does not have any neighbors.
</pre>

<p><strong>Example 3:</strong></p>

<pre><strong>Input:</strong> adjList = []
<strong>Output:</strong> []
<strong>Explanation:</strong> This an empty graph, it does not have any nodes.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in the graph is in the range <code>[0, 100]</code>.</li>
	<li><code>1 &lt;= Node.val &lt;= 100</code></li>
	<li><code>Node.val</code> is unique for each node.</li>
	<li>There are no repeated edges and no self-loops in the graph.</li>
	<li>The Graph is connected and all nodes can be visited starting from the given node.</li>
</ul>


**Companies**:  
[Facebook](https://leetcode.com/company/facebook), [Amazon](https://leetcode.com/company/amazon), [Microsoft](https://leetcode.com/company/microsoft), [Google](https://leetcode.com/company/google), [Bloomberg](https://leetcode.com/company/bloomberg), [Salesforce](https://leetcode.com/company/salesforce), [Twitter](https://leetcode.com/company/twitter), [Oracle](https://leetcode.com/company/oracle)

**Related Topics**:  
[Hash Table](https://leetcode.com/tag/hash-table/), [Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Breadth-First Search](https://leetcode.com/tag/breadth-first-search/), [Graph](https://leetcode.com/tag/graph/)

**Similar Questions**:
* [Copy List with Random Pointer (Medium)](https://leetcode.com/problems/copy-list-with-random-pointer/)
* [Clone Binary Tree With Random Pointer (Medium)](https://leetcode.com/problems/clone-binary-tree-with-random-pointer/)
* [Clone N-ary Tree (Medium)](https://leetcode.com/problems/clone-n-ary-tree/)

## Solution 1. DFS

An `unordered_map` is used to record the mapping between the original and copied nodes, and to ensure each node is only copied once.

	Check Base Case: If the input node is nullptr, return nullptr.
	Check for Clones: If the node has already been cloned, return the clone from the map.
	Clone the Node: Create a copy of the current node and store it in the map.
	Clone Neighbors: Recursively clone all the neighbors of the current node and add them to the new node's neighbor list.
	Return Clone: Return the fully cloned node.

```cpp
// OJ: https://leetcode.com/problems/clone-graph/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(N)
class Solution {
    unordered_map<Node*, Node*> m; // Map to store cloned nodes
public:
    Node* cloneGraph(Node* node) {
        if (!node) return nullptr; // Return null for empty graph
        if (m.count(node)) return m[node]; // Return existing clone if already created
        auto cpy = new Node(node->val); // Create a copy of the current node
        m[node] = cpy; // Map original node to its clone
        for (auto &n : node->neighbors) 
            cpy->neighbors.push_back(cloneGraph(n)); // Recursively clone neighbors
        return cpy; // Return the cloned node
    }
};

```

## Solution 2. BFS

	Initialize BFS: Start by creating a clone of the input node and push the original node into a queue.
	Process Nodes: For each node in the queue, clone its neighbors if they haven't been cloned yet and add them to the queue.
	Link Neighbors: Attach the cloned neighbors to the cloned node.
	Return Clone: Once all nodes are processed, return the clone of the starting node.

```cpp
// OJ: https://leetcode.com/problems/clone-graph/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(N)

class Solution {
public:
    Node* cloneGraph(Node* node) {
        if (!node) return nullptr; // Return null for empty graph
        queue<Node*> q;
        unordered_map<Node*, Node*> m; // Map to store cloned nodes
        m[node] = new Node(node->val); // Clone the starting node
        q.push(node); // Start BFS with the given node
        while (q.size()) {
            auto p = q.front(); 
            q.pop();
            auto copy = m[p]; // Get the clone of the current node
            for (auto nei : p->neighbors) {
                if (!m.count(nei)) { // If neighbor is not cloned
                    m[nei] = new Node(nei->val); // Clone the neighbor
                    q.push(nei); // Add neighbor to the queue for BFS
                }
                copy->neighbors.push_back(m[nei]); // Add neighbor to the cloned node's list
            }
        }
        return m[node]; // Return the cloned graph starting node
    }
};

```
