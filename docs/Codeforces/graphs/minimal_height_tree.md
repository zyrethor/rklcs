# D. Minimal Height Tree

```c++
#include <bits/stdc++.h>
using namespace std;

#define pb push_back

const int N = 100005;


// in minumim height tree, ascending vertices are at same level, if not, at next level
void solve() {
  int n;
  cin >> n;
  vector<int> a(n);
  for (int i = 0; i < n; i++) {
    cin >> a[i];
  }


  vector<int> h(n, 0);
  h[0] = 0;
  int lst = 0;
  for (int i = 1; i < n; i++) {
    // iterate last depth's vertices
    if (i - 1 > 0 && a[i-1] > a[i])
      lst++;
    h[i] = h[lst] + 1;
  }

  for (int i = 0; i < n; i++)
    cout << h[i] << " ";
  cout << endl;
  cout << h[n-1] << '\n';

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

