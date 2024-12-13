# B. Erase First or Second Letter

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

void solve() {
  int n;
  cin >> n;
  string s;
  cin >> s;
  array<int, 26> cnt{};
  int dist = 0;
  int ans = 0;
  for (auto c : s) {
    dist += cnt[c - 'a']++ == 0;
    ans += dist;
  }
  cout << ans << "\n";
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int t;
  cin >> t;
  while (t--) {
    solve();
  }

  return 0;
}
```

