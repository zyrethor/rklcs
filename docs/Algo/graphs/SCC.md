# Strongly connected components (SCC)

## Code

```c++
struct SCC {
  int n, dfn, nscc;
  vector<vector<int>> adj;
  vector<int> tin, low, idx;
  vector<bool> inStk;
  stack<int> stk;

  SCC(int n_) : n(n_), dfn(0), nscc(0), adj(n),
    tin(n, -1), low(n, -1), idx(n, -1), inStk(n, false){}

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
```

