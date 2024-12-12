# C. Journey

```c++
#include <bits/stdc++.h>
using namespace std;

#define pb push_back

const int N = 100005;

vector<int> g[N];
double ans;
bool used[N];
vector<int> pathLen;

void dfs(int start, double prob, int depth) {
  used[start] = true;
  bool isLeaf = true;

  int below = 0;
  for (auto to : g[start]) {
    if (!used[to]) {
      below++;
    }
  }

  for (auto to : g[start]) {
    if (!used[to]) {
      isLeaf = false;
      dfs(to, prob/below, depth+1);
    }
  }

  if (isLeaf)
    ans += prob * depth;
}



int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int n, x, y;
  cin >> n;
  for (int i = 0 ; i < n-1; i++) {
    cin >> x >> y;
    x--;
    y--;
    g[x].pb(y);
    g[y].pb(x);
  }


  dfs(0, 1.0, 0);

  cout << fixed << setprecision(7) << ans;

  return 0;
}
```

