# E. Cyclic Components

```c++
#include <bits/stdc++.h>
using namespace std;

#define pb push_back

const int N = 200005;
int ans;
vector<int> g[N];
vector<int> comp;
int deg[N];
bool used[N];

// dfs recursive
void dfs(int v) {
  used[v] = true;
  comp.pb(v);

  for (auto to : g[v]) {
    if (!used[to]) {
      dfs(to);
    }
  }
}

// dfs iterative
void dfsi(int start) {
  stack<int> s;
  s.push(start);
  used[start] = true;

  while (!s.empty()) {
    int v = s.top();
    s.pop();
    comp.pb(v);

    for (auto to : g[v]) {
      if (!used[to]) {
        used[to] = true;
        s.push(to);
      }
    }
  }

}


int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int n, m, x, y;
  cin >> n >> m;
  for (int i = 0; i < m; i++) {
    cin >> x >> y;
    x--;
    y--;
    g[x].pb(y);
    g[y].pb(x);
    deg[x]++;
    deg[y]++;
  }

  for (int i = 0; i < n; i++) {
    if (!used[i]) {
      comp.clear();
      dfs(i);
      bool ok = all_of(comp.begin(), comp.end(), [&](int v){return deg[v] == 2;});
      if (ok) ans++;
    }
  }

  cout << ans;

  return 0;
}
```

