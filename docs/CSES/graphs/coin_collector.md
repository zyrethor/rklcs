# Coin Collector

**Solution:** use scc $\rightarrow$ condensation graph $\rightarrow$ topological order to get ans

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

struct SCC {
  int n, dfn, nscc;
  vector<vector<int>> adj;
  vector<int> tin, low, idx;
  vector<bool> inStk;
  stack<int> stk;

  SCC(int n_) : n(n_), dfn(0), nscc(0), adj(n),
    tin(n, -1), low(n, -1), idx(n, -1), inStk(n_, false){}

  void add_edge(int u, int v) {
    adj[u].emplace_back(v);
  }

  void solve_scc() {
    for (int i = 0; i < n; i++) {
      if (tin[i] == -1)
        dfs(i);
    }
  }

  int get_id(int u) {
    return idx[u];
  }

  int count() {
    return nscc;
  }

  void dfs(int u) {
    tin[u] = low[u] = dfn++;
    stk.push(u);
    inStk[u] = true;
    for (int v : adj[u]) {
      if (tin[v] == -1) {
        dfs(v);
        low[u] = min(low[u], low[v]);
      }
      else if (inStk[v]) {
        low[u] = min(low[u], tin[v]);
      }
    }

    if (low[u] == tin[u]) {
      while (true) {
        int x = stk.top(); stk.pop();
        inStk[x] = false;
        idx[x] = nscc;
        if (x == u) break;
      }
      nscc++;
    }

  }

};

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int n, m;

  cin >> n >> m;

  vector<int> coins(n);
  for (int i = 0; i < n; i++) {
    cin >> coins[i];
  }

  vector<ll> sccCoins(n);
  SCC scc(n);
  int a, b;
  for (int i = 0; i < m; i++) {
    cin >> a >> b;
    scc.add_edge(a-1, b-1);
  }

  scc.solve_scc();

  queue<int> q;
  vector<int> in_degree(scc.nscc, 0);
  vector<vector<int>> adj_cond(scc.nscc);
  for (int u = 0; u < n; u++) {
    for (int v : scc.adj[u]) {
      if (scc.idx[u] != scc.idx[v]) {
        adj_cond[scc.idx[u]].emplace_back(scc.idx[v]);
        in_degree[scc.idx[v]]++;
      }
    }
  }

  for (int u = 0; u < n; u++) {
    sccCoins[scc.idx[u]] += (ll)coins[u];
  }

  for (int i = 0; i < scc.nscc; i++) {
    if (in_degree[i] == 0)
      q.push(i);
  }

  vector<int> topo;
  while (!q.empty()) {
    int u = q.front(); q.pop();

    topo.emplace_back(u);
    for (auto &v : adj_cond[u]) {
      in_degree[v]--;
      if (in_degree[v] == 0)
        q.push(v);
    }
  }

  vector<ll> dp(scc.nscc, -1e18);

  for (int i = 0; i < scc.nscc; i++) {
    dp[i] = sccCoins[i];
  }

  for (auto u : topo) {
    for (auto v : adj_cond[u]) {
      dp[v] = max(dp[v], dp[u] + sccCoins[v]);
    }
  }

  cout << *max_element(dp.begin(), dp.end());

  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

// Typedef for convenience
typedef long long ll;

// Structure to represent and compute Strongly Connected Components (SCCs)
struct SCC {
    int n;                      // Number of nodes in the graph
    int dfn;                    // Discovery time counter for DFS
    int nscc;                   // Number of SCCs found
    vector<vector<int>> adj;    // Adjacency list for the original graph
    vector<int> tin;            // Discovery times of nodes
    vector<int> low;            // Low-link values of nodes
    vector<int> idx;            // SCC ID for each node
    vector<bool> inStk;         // Flags to check if a node is in the stack
    stack<int> stk;             // Stack to keep track of the current path in DFS

    // Constructor to initialize the SCC structure
    SCC(int n_) : n(n_), dfn(0), nscc(0), adj(n),
                tin(n, -1), low(n, -1), idx(n, -1), inStk(n_, false) {}

    // Function to add a directed edge from node u to node v
    void add_edge(int u, int v) {
        adj[u].emplace_back(v);
    }

    // Function to initiate the SCC computation using DFS
    void solve_scc() {
        for (int i = 0; i < n; i++) {
            if (tin[i] == -1) // If node i has not been visited
                dfs(i);
        }
    }

    // Function to get the SCC ID of a node
    int get_id(int u) {
        return idx[u];
    }

    // Function to get the total number of SCCs
    int count() {
        return nscc;
    }

    // Depth-First Search to identify SCCs using Tarjan's algorithm
    void dfs(int u) {
        tin[u] = low[u] = dfn++; // Initialize discovery time and low-link value
        stk.push(u);             // Push the node onto the stack
        inStk[u] = true;         // Mark the node as being in the stack

        // Iterate through all adjacent nodes
        for (int v : adj[u]) {
            if (tin[v] == -1) { // If node v has not been visited
                dfs(v);         // Recursively visit it
                low[u] = min(low[u], low[v]); // Update low-link value
            }
            else if (inStk[v]) { // If node v is in the current stack
                low[u] = min(low[u], tin[v]); // Update low-link value
            }
        }

        // If the node is a root of an SCC
        if (low[u] == tin[u]) {
            while (true) {
                int x = stk.top(); stk.pop(); // Pop nodes from the stack
                inStk[x] = false;             // Mark them as not in the stack
                idx[x] = nscc;                // Assign the current SCC ID
                if (x == u) break;            // Stop when the root is reached
            }
            nscc++; // Increment the number of SCCs found
        }
    }
};

int main() {
    ios::sync_with_stdio(false); // Disable synchronization with C I/O for faster input
    cin.tie(nullptr);            // Untie cin from cout for faster input

    int n, m; // Number of nodes and edges
    cin >> n >> m;

    vector<int> coins(n); // Vector to store the number of coins at each node
    for (int i = 0; i < n; i++) {
        cin >> coins[i];
    }

    vector<ll> sccCoins(n); // Vector to store the total coins in each SCC
    SCC scc(n);             // Initialize the SCC structure with n nodes
    int a, b;               // Variables to read edge endpoints

    // Read all edges and add them to the SCC structure
    for (int i = 0; i < m; i++) {
        cin >> a >> b;
        scc.add_edge(a-1, b-1); // Convert to 0-based indexing
    }

    scc.solve_scc(); // Compute all SCCs

    // Initialize structures for the condensed graph
    queue<int> q; // Queue for BFS in topological sorting
    vector<int> in_degree(scc.nscc, 0); // In-degree of each SCC in the condensed graph
    vector<vector<int>> adj_cond(scc.nscc); // Adjacency list for the condensed graph

    // Build the condensed graph by connecting different SCCs
    for (int u = 0; u < n; u++) {
        for (int v : scc.adj[u]) {
            if (scc.idx[u] != scc.idx[v]) { // If edge crosses different SCCs
                adj_cond[scc.idx[u]].emplace_back(scc.idx[v]); // Add edge in condensed graph
                in_degree[scc.idx[v]]++; // Increment in-degree of the target SCC
            }
        }
    }

    // Sum up the coins for each SCC
    for (int u = 0; u < n; u++) {
        sccCoins[scc.idx[u]] += (ll)coins[u];
    }

    // Initialize the queue with SCCs that have zero in-degree (no dependencies)
    for (int i = 0; i < scc.nscc; i++) {
        if (in_degree[i] == 0)
            q.push(i);
    }

    vector<int> topo; // Vector to store the topological order
    while (!q.empty()) {
        int u = q.front(); q.pop(); // Get the next SCC with zero in-degree

        topo.emplace_back(u); // Add it to the topological order
        for (auto &v : adj_cond[u]) { // Iterate through its neighbors in the condensed graph
            in_degree[v]--; // Decrement the in-degree of the neighbor
            if (in_degree[v] == 0) // If the neighbor now has zero in-degree
                q.push(v);        // Add it to the queue
        }
    }

    // Initialize dynamic programming (DP) array to store the maximum coins for each SCC
    // Initialize with a very small number to represent negative infinity
    vector<ll> dp(scc.nscc, -1e18);

    // Base case: the maximum coins for each SCC is at least the sum of its own coins
    for (int i = 0; i < scc.nscc; i++) {
        dp[i] = sccCoins[i];
    }

    // Iterate through the SCCs in topological order to compute the DP values
    for (auto u : topo) {
        for (auto v : adj_cond[u]) { // For each neighbor of SCC u
            // Update the maximum coins for SCC v by considering the path through SCC u
            dp[v] = max(dp[v], dp[u] + sccCoins[v]);
        }
    }

    // Find and output the maximum coins that can be collected
    cout << *max_element(dp.begin(), dp.end());

    return 0;
}
```
