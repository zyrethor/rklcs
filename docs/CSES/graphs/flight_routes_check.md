# Flight Routes Check

**Solution:** Strongly connected components (SCC)

**Time Complexity:** Both $O(V + E)$

## Kosaraju's Algorithm

```c++
#include <bits/stdc++.h>
using namespace std;

void dfs(int u, const vector<vector<int>> adj, vector<bool> &vis) {
  stack<int> s;
  s.push(u);
  vis[u] = true;
  while (!s.empty()) {
    int cur = s.top();
    s.pop();
    for (auto nxt : adj[cur]) {
      if (!vis[nxt]) {
        vis[nxt] = true;
        s.push(nxt);
      }
    }
  }
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int n, m;


  cin >> n >> m;
  vector<vector<int>> adj(n+1);
  vector<vector<int>> adj_rev(n+1);
  vector<bool> visited(n+1, false);
  for (int i = 0; i < m; i++) {
    int a, b;
    cin >> a >> b;
    adj[a].push_back(b);
    adj_rev[b].push_back(a);
  }


  dfs(1, adj, visited);
  for (int i = 1; i <= n; i++) {
    if (!visited[i]) {
      cout << "NO\n" << 1 << " " << i << "\n";
      return 0;
    }
  }

  visited.assign(n+1, false);
  dfs(1, adj_rev, visited);
  for (int i = 1; i <= n; i++) {
    if (!visited[i]) {
      cout << "NO\n" << i << " " << 1 << "\n";
      return 0;
    }
  }

  cout << "YES\n";

  return 0;
}
```

## Tarjan's Algorithm

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

  SCC scc(n);
  int a, b;
  for (int i = 0; i < m; i++) {
    cin >> a >> b;
    scc.add_edge(a-1, b-1);
  }

  scc.solve_scc();
  for (int u = 0; u < n; u++) {
    cout << scc.get_id(u) << " ";
  }
  cout << endl;

  if (scc.nscc == 1) {
    cout << "YES";
  }
  else {
    cout << "NO\n";
    for (int u = 0; u < n; u++) {
      for (auto v : scc.adj[u]) {
        if (scc.get_id(u) != scc.get_id(v)) {
          cout << u+1 << " " << v+1;
          return 0;
        }
      }
    }
  }


  return 0;
}
```

