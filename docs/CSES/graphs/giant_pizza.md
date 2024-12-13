# Giant Pizza

**Solution:** 2-SAT

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

struct TwoSat : SCC {
  TwoSat(int n_) : SCC(n_) {}

  void orr(int x, int y) {
    if ((x ^ y) == 1) return;
    add_edge(x ^ 1, y);
    add_edge(y ^ 1, x);
  }

  vector<int> solve_2sat() {
    solve_scc();
    vector<int> res(n);
    for (int i = 0; i < n; i+=2) {
      if (idx[i] == idx[i+1])
        return {};
    }

    for (int i = 0; i < n; i++) {
      res[i] = idx[i] < idx[i^1];
    }
    return res;
  }
};


int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m;

  TwoSat ts(2*m);
  char c1, c2;
  int a, b;
  for (int i = 0; i < n; i++) {
    cin >> c1 >> a >> c2 >> b;
    a--; b--;
    a = 2 * a + (c1 == '-' ? 1 : 0);
    b = 2 * b + (c2 == '-' ? 1 : 0);
    ts.orr(a, b);
  }

  vector<int> ans = ts.solve_2sat();

  if (ans.empty()) {
    cout << "IMPOSSIBLE";
  }
  else {
    for (int i = 0; i < m; i++) {
      cout << "-+"[ans[2*i]] << " \n"[i == m-1];
    }
  }

  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

// Structure to find Strongly Connected Components (SCC) using Tarjan's algorithm
struct SCC {
    int n; // Number of nodes in the graph
    int dfn; // Timestamp counter for DFS discovery time
    int nscc; // Number of SCCs found
    vector<vector<int>> adj; // Adjacency list representation of the graph
    vector<int> tin; // Discovery time of each node
    vector<int> low; // Low-link values for each node
    vector<int> idx; // SCC identifier for each node
    vector<bool> inStk; // Flag to indicate if a node is in the stack
    stack<int> stk; // Stack to maintain the order of nodes during DFS

    // Constructor to initialize the SCC structure
    SCC(int n_) : n(n_), dfn(0), nscc(0), adj(n),
        tin(n, -1), low(n, -1), idx(n, -1), inStk(n_, false) {}

    // Function to add a directed edge from node u to node v
    void add_edge(int u, int v) {
        adj[u].emplace_back(v);
    }

    // Function to initiate the process of finding SCCs
    void solve_scc() {
        for (int i = 0; i < n; i++) {
            if (tin[i] == -1) // If node i has not been visited
                dfs(i); // Perform DFS starting from node i
        }
    }

    // Function to get the SCC identifier of node u
    int get_id(int u) {
        return idx[u];
    }

    // Function to get the total number of SCCs found
    int count() {
        return nscc;
    }

    // Depth-First Search (DFS) function used by Tarjan's algorithm
    void dfs(int u) {
        tin[u] = low[u] = dfn++; // Set discovery time and low-link value
        stk.push(u); // Push node u onto the stack
        inStk[u] = true; // Mark node u as being in the stack

        // Iterate over all neighbors of node u
        for (int v : adj[u]) {
            if (tin[v] == -1) { // If node v has not been visited
                dfs(v); // Recursively perform DFS on node v
                low[u] = min(low[u], low[v]); // Update low-link value
            }
            else if (inStk[v]) { // If node v is in the stack
                low[u] = min(low[u], tin[v]); // Update low-link value based on discovery time
            }
        }

        // If node u is a root node of an SCC
        if (low[u] == tin[u]) {
            while (true) {
                int x = stk.top(); stk.pop(); // Pop node x from the stack
                inStk[x] = false; // Mark node x as not being in the stack
                idx[x] = nscc; // Assign the current SCC identifier to node x
                if (x == u) break; // Stop if we've processed all nodes in this SCC
            }
            nscc++; // Increment the count of SCCs found
        }
    }
};

// Structure to solve the 2-SAT problem using the SCC structure
struct TwoSat : SCC {
    // Constructor to initialize the TwoSat structure with 2*m nodes (variables and their negations)
    TwoSat(int n_) : SCC(n_) {}

    // Function to add a clause (x OR y) to the 2-SAT problem
    void orr(int x, int y) {
        if ((x ^ y) == 1) return; // If x and y are negations, no need to add edges
        add_edge(x ^ 1, y); // Add implication: NOT x implies y
        add_edge(y ^ 1, x); // Add implication: NOT y implies x
    }

    // Function to solve the 2-SAT problem and return the variable assignments
    vector<int> solve_2sat() {
        solve_scc(); // Find all SCCs in the implication graph
        vector<int> res(n); // Vector to store the result (assignments)

        // Check for contradictions: if a variable and its negation are in the same SCC
        for (int i = 0; i < n; i += 2) { // Iterate over variables (assuming even indices are variables)
            if (idx[i] == idx[i + 1])
                return {}; // Return empty vector if a contradiction is found (unsatisfiable)
        }

        // Assign values to variables based on the SCCs' topological order
        for (int i = 0; i < n; i++) {
            res[i] = idx[i] < idx[i ^ 1]; // Assign true if the variable's SCC is before its negation's SCC
        }
        return res; // Return the assignments
    }
};

int main() {
    ios::sync_with_stdio(false); // Disable synchronization between C and C++ standard streams
    cin.tie(nullptr); // Untie cin from cout for faster input

    int n, m;
    cin >> n >> m; // Read the number of clauses (n) and the number of variables (m)

    TwoSat ts(2 * m); // Initialize TwoSat with 2*m nodes (each variable and its negation)
    char c1, c2; // Characters to represent the sign of literals ('+' or '-')
    int a, b; // Variables involved in each clause

    // Read each clause and add it to the TwoSat instance
    for (int i = 0; i < n; i++) {
        cin >> c1 >> a >> c2 >> b; // Read a clause in the form of (c1 a) OR (c2 b)
        a--; b--; // Convert variables to 0-based indexing
        // Encode literals: 2*a for a positive literal, 2*a+1 for a negative literal
        a = 2 * a + (c1 == '-' ? 1 : 0);
        b = 2 * b + (c2 == '-' ? 1 : 0);
        ts.orr(a, b); // Add the clause to the TwoSat instance
    }

    vector<int> ans = ts.solve_2sat(); // Solve the 2-SAT problem

    if (ans.empty()) { // If no solution exists
        cout << "IMPOSSIBLE";
    }
    else {
        // Output the assignment for each variable
        for (int i = 0; i < m; i++) {
            // ans[2*i] corresponds to the positive literal of variable i
            // If ans[2*i] is true, output '+', else output '-'
            cout << "-+"[ans[2 * i]] << " \n"[i == m - 1];
        }
    }

    return 0; // End of program
}
```

