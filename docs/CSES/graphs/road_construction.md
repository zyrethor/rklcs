# Road Construction

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

struct DSU {
  vector<int> p, sz;
  int comp_cnt;
  int max_size;

  DSU() {};
  DSU(int n) {
    init(n);
  }

  void init(int n) {
    p.resize(n+1);
    iota(p.begin(), p.end(), 0);
    sz.assign(n+1, 1);
    comp_cnt = n;
    max_size = 1;
  }

  int find(int x) {
    if (x == p[x]) return x;
    return p[x] = find(p[x]);
  }

  bool same(int x, int y) {
    return find(x) == find(y);
  }

  bool merge(int a, int b) {
    a = find(a);
    b = find(b);
    if (a != b) {
      if (sz[a] < sz[b])
        swap(a, b);
      p[b] = a;
      sz[a] += sz[b];
      comp_cnt--;
      max_size = max(max_size, sz[a]);
      return true;
    }
    return false;
  }
};

struct Edge {
  int a, b;
  ll c;
};

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m;

  DSU dsu(n);
  int a, b;
  for (int i = 0; i < m; i++) {
    cin >> a >> b;
    dsu.merge(a, b);
    cout << dsu.comp_cnt << " " << dsu.max_size << "\n";
  }

  return 0;
}
```
