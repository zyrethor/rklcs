# Planets Queries I

**Solution:** Binary Lifting

**Time Complexity:** $O(n \times \log k +  q \times \log k)$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb emplace_back
#define pii pair<ll, int>
const int MOD = 1e9+7;
const ll INF = 1e18;


int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, q;
  cin >> n >> q;
  vector<int> t(n+1);
  for (int i = 1; i <= n; i++)  {
    cin >> t[i];
  }

  int LOG = 30;

  // up[j][i] = 2^j th step from i
  vector<vector<int>> up(LOG, vector<int>(n+1));
  for (int i = 1; i <= n; i++) {
    up[0][i] = t[i];
  }

  for (int j = 1; j < LOG; j++) {
    for (int i = 1; i <= n; i++) {
      up[j][i] = up[j-1][up[j-1][i]];
    }
  }

  while (q--) {
    int x, k;
    cin >> x >> k;
    int cur = x;
    for (int j = 0; j < LOG; j++) {
      if (k & (1 << j)) {
        cur = up[j][cur];
      }
    }
    cout << cur << "\n";
  }

  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

// Type definitions for convenience
#define ll long long
#define pb emplace_back
#define pii pair<ll, int>

// Constants
const int MOD = 1e9+7; // Not used in this particular code
const ll INF = 1e18;    // Representation of infinity
const int LOG = 30;     // Maximum power for binary lifting (covers k up to ~1e9)

int main() {
    // Optimize input and output operations for faster execution
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, q;
    cin >> n >> q; // Read number of nodes and number of queries

    vector<int> t(n + 1); // Transition array (1-based indexing)
    for (int i = 1; i <= n; i++)  {
        cin >> t[i]; // Read the node each node points to
    }

    // Initialize the binary lifting table
    // up[j][i] = node reached after 2^j transitions from node i
    // Initialize with size LOG x (n+1) for 1-based indexing
    vector<vector<int>> up(LOG, vector<int>(n + 1));

    // Base case: 2^0 = 1 transition
    for (int i = 1; i <= n; i++) {
        up[0][i] = t[i];
    }

    // Build the sparse table
    for (int j = 1; j < LOG; j++) {
        for (int i = 1; i <= n; i++) {
            // If up[j-1][i] is 0, it means no transition; set up[j][i] accordingly
            // Assuming that node indices start from 1 and t[i] >=1, else handle separately
            up[j][i] = up[j-1][ up[j-1][i] ];
        }
    }

    // Process each query
    while (q--) {
        int x;  // Starting node
        ll k;   // Number of transitions
        cin >> x >> k;

        int cur = x; // Current node after transitions

        // Iterate through each bit of k
        for (int j = 0; j < LOG; j++) {
            // If the j-th bit of k is set, perform the corresponding jump
            if (k & (1LL << j)) {
                cur = up[j][cur];
            }
        }

        // Output the result after k transitions
        cout << cur << "\n";
    }

    return 0;
}
```

