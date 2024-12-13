# Planets and Kingdoms

**Solution:** Strongly connected components (SCC)

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

  void solve() {
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

  SCC scc(n);
  int a, b;
  for (int i = 0; i < m; i++) {
    cin >> a >> b;
    a--; b--;
    scc.add_edge(a, b);
  }
  
  scc.solve();

  cout << scc.nscc << "\n";
  for (int i = 0; i < n; i++) {
    cout << scc.get_id(i) + 1 << " \n"[i == n-1];
  }

  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

// Structure to represent and compute Strongly Connected Components (SCCs) using Tarjan's algorithm
struct SCC {
    int n; // Number of nodes in the graph
    int dfn; // Timestamp counter used during DFS
    int nscc; // Number of SCCs found
    vector<vector<int>> adj; // Adjacency list representing the graph
    vector<int> tin; // Discovery times of nodes
    vector<int> low; // Low-link values of nodes
    vector<int> idx; // SCC identifier for each node
    vector<bool> inStk; // Boolean flag to check if a node is in the stack
    stack<int> stk; // Stack to maintain the nodes being processed

    // Constructor to initialize the SCC structure
    SCC(int n_) : n(n_), dfn(0), nscc(0), adj(n),
        tin(n, -1), low(n, -1), idx(n, -1), inStk(n_, false) {}

    // Function to add a directed edge from node u to node v
    void add_edge(int u, int v) {
        adj[u].emplace_back(v);
    }

    // Main function to compute SCCs
    void solve() {
        // Iterate through all nodes to ensure all components are covered
        for (int i = 0; i < n; i++) {
            if (tin[i] == -1) // If node i hasn't been visited yet
                dfs(i); // Perform DFS starting from node i
        }
    }
    
    // Function to get the SCC identifier of node u
    int get_id(int u) {
        return idx[u];
    }

    // Function to return the total number of SCCs found
    int count() {
        return nscc;
    }
    
    // Recursive DFS function implementing Tarjan's algorithm
    void dfs(int u) {
        tin[u] = low[u] = dfn++; // Set discovery time and low-link value
        stk.push(u); // Push node u onto the stack
        inStk[u] = true; // Mark node u as being in the stack

        // Iterate through all adjacent nodes of u
        for (int v : adj[u]) {
            if (tin[v] == -1) { // If node v hasn't been visited
                dfs(v); // Recursively perform DFS on node v
                low[u] = min(low[u], low[v]); // Update low-link value
            }
            else if (inStk[v]) { // If node v is in the stack
                low[u] = min(low[u], tin[v]); // Update low-link value
            }
        }

        // If u is a root node of an SCC
        if (low[u] == tin[u]) {
            // Pop nodes from the stack until u is reached
            while (true) {
                int x = stk.top(); stk.pop(); // Pop the top node
                inStk[x] = false; // Mark node x as not being in the stack
                idx[x] = nscc; // Assign the current SCC identifier to node x
                if (x == u) break; // Stop if we've reached the root node
            }
            nscc++; // Increment the SCC count
        }
    }

};

int main() {
    ios::sync_with_stdio(false); // Optimize I/O operations
    cin.tie(nullptr); // Untie cin from cout for faster input

    int n, m; // n = number of nodes, m = number of edges
    cin >> n >> m;

    SCC scc(n); // Initialize SCC structure with n nodes
    int a, b;
    for (int i = 0; i < m; i++) {
        cin >> a >> b; // Read edge from a to b
        a--; b--; // Convert to 0-based indexing
        scc.add_edge(a, b); // Add the edge to the graph
    }
    
    scc.solve(); // Compute the SCCs

    cout << scc.nscc << "\n"; // Output the number of SCCs
    for (int i = 0; i < n; i++) {
        cout << scc.get_id(i) + 1 << " \n"[i == n-1]; // Output SCC identifier for each node (1-based)
    }

    return 0;
}
```

