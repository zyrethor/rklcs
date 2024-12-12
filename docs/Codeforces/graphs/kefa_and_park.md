# C. Kefa and Park

```c++
#include <bits/stdc++.h>
using namespace std;

#define pb push_back
vector<int> v[100005];
int m, n, x, y;
int cat[100005];
int ans;

// v = head of any row of adjacency list
// pr = parent
void dfs(int cur, int pr, int nCats) {
  if (nCats > n)
    return;

  int isLeaf = 1;

  for (int neighbor : v[cur]) {
    if (neighbor != pr) {
      isLeaf = 0;
      dfs(neighbor, cur, (cat[neighbor] ? nCats + 1 : 0));
    }
  }

  if (isLeaf)
    ans += isLeaf;
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  cin >> m >> n;
  for (int i = 0; i < m; i++)
    cin >> cat[i];
  for (int i = 1; i < m; i++) {
    cin >> x >> y;
    x--;
    y--;
    v[x].pb(y);
    v[y].pb(x);
  }

  dfs(0, -1, cat[0]);

  cout << ans;

  // for (int i = 0; i < m; i++) {
  //   for (int j = 0; j < m; j++) {
  //     cout << v[i][j] << " ";
  //   }
  //   cout << "\n";
  // }


  return 0;
}
```

