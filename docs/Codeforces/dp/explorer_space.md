# D. Explorer Space

```c++
#include <bits/stdc++.h>
using namespace std;

#define INF 1e9
#define fore(i, a, b) for (int i = (a); i < (b); i++)

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m, k;
  cin >> n >> m >> k;

  vector<vector<int>> h(n, vector<int>(m-1, 0));
  fore(i, 0, n) {
    fore(j, 0, m-1) {
      cin >> h[i][j];
    }
  }

  vector<vector<int>> v(n-1, vector<int>(m, 0));
  fore(i, 0, n-1) {
    fore(j, 0, m) {
      cin >> v[i][j];
    }
  }

  if (k % 2 != 0) {
    fore(i, 0, n) {
      fore(j, 0, m) {
        cout << "-1" << (j == m-1 ? "\n" : " ");
      }
    }

    return 0;
  }

  vector<vector<int>> previous(n, vector<int>(m, 0));
  vector<vector<int>> current(n, vector<int>(m, INF));
  k /= 2;
  fore(t, 0, k) {
    fore(i, 0, n) {
      fore(j, 0, m) {
        int min_cost = INF;

        // UP
        if (i > 0) { min_cost = min(min_cost, 2*v[i-1][j] + previous[i-1][j]); }

        // DOWN
        if (i < n-1) {
          min_cost = min(min_cost, 2*v[i][j] + previous[i+1][j]);
        }

        // LEFT
        if (j > 0) {
          min_cost = min(min_cost, 2*h[i][j-1] + previous[i][j-1]);
        }

        // RIGHT
        if (j < m-1) {
          min_cost = min(min_cost, 2*h[i][j] + previous[i][j+1]);
        }

        current[i][j] = min_cost;
      }
    }
    current.swap(previous);
  }

  fore(i, 0, n) {
    fore(j, 0, m) {
      if (previous[i][j] >= INF) {
        cout << "-1";
      }
      else {
        cout << previous[i][j];
      }
      cout << (j == m-1 ? "\n" : " ");
    }
  }

  return 0;
}
```

