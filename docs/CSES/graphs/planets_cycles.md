# Planets Cycles

**Solution:** DFS

**Time Complexity:** $O(n)$

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

  int n;
  cin >> n;
  vector<int> t(n+1);
  for (int i = 1; i <= n; i++)  {
    cin >> t[i];
  }

  // 0=unvisited, 1=visiting, 2=done
  vector<int> state(n+1, 0);
  vector<int> ans(n+1, 0);
  vector<bool> inCycle(n+1, false);

  function<void(int)> dfs = [&](int u) {
    state[u] = 1;
    int to = t[u];

    if (state[to] == 0) {
      dfs(to);
    } else if (state[to] == 1) {
      int cur = to;
      int cycle_len = 0;

      do {
        cur = t[cur];
        cycle_len++;
      } while (cur != to);

      cur = to;
      do {
        inCycle[cur] = true;
        ans[cur] = cycle_len;
        cur = t[cur];
      } while (cur != to);
    }

    state[u] = 2;
  };

  for (int i = 1; i <= n; i++) {
    if (state[i] == 0) {
      dfs(i);
    }
  }

  function<int(int)> calcAns = [&](int u) -> int {
    if (ans[u] != 0) return ans[u];

    ans[u] = 1 + calcAns(t[u]);
    return ans[u];
  };

  for (int i = 1; i <= n; i++) {
    if (!inCycle[i] && ans[i] == 0)
      calcAns(i);
  }

  for (int i = 1; i <= n; i++) {
    cout << ans[i] << (i == n ? "\n" : " ");
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
const int MOD = 1e9+7; // Not used in this code but commonly used for modular arithmetic
const ll INF = 1e18;    // Representation of infinity

int main() {
    // Optimize input and output operations for faster execution
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n; // Read number of nodes

    vector<int> t(n + 1); // Transition array (1-based indexing)
    for (int i = 1; i <= n; i++)  {
        cin >> t[i]; // Read the node each node points to
    }

    // State array to keep track of node states during DFS
    // 0 = unvisited, 1 = visiting, 2 = done
    vector<int> state(n + 1, 0);

    // ans[i] will store the cycle length if node i is in a cycle
    // or the number of steps to reach a cycle plus the cycle length if not in a cycle
    vector<int> ans(n + 1, 0);

    // inCycle[i] indicates whether node i is part of a cycle
    vector<bool> inCycle(n + 1, false);

    // Lambda function for DFS to detect cycles and assign cycle lengths
    function<void(int)> dfs = [&](int u) {
        state[u] = 1; // Mark node as visiting
        int to = t[u]; // Next node

        if (state[to] == 0) {
            // If the next node is unvisited, continue DFS
            dfs(to);
        } else if (state[to] == 1) {
            // A cycle is detected
            int cur = to;
            int cycle_len = 0;

            // Calculate the length of the cycle
            do {
                cur = t[cur];
                cycle_len++;
            } while (cur != to);

            // Mark all nodes in the cycle and assign the cycle length
            cur = to;
            do {
                inCycle[cur] = true; // Mark node as part of a cycle
                ans[cur] = cycle_len; // Assign cycle length
                cur = t[cur];
            } while (cur != to);
        }

        state[u] = 2; // Mark node as done
    };

    // Perform DFS for each node to detect cycles
    for (int i = 1; i <= n; i++) {
        if (state[i] == 0) {
            dfs(i);
        }
    }

    // Lambda function to calculate ans[i] for nodes not in any cycle
    function<int(int)> calcAns = [&](int u) -> int {
        if (ans[u] != 0) return ans[u]; // If already calculated, return the value

        ans[u] = 1 + calcAns(t[u]); // Recursively calculate the steps to reach a cycle plus the cycle length
        return ans[u];
    };

    // Calculate ans[i] for all nodes not part of any cycle
    for (int i = 1; i <= n; i++) {
        if (!inCycle[i] && ans[i] == 0)
            calcAns(i);
    }

    // Output the ans[i] for each node
    for (int i = 1; i <= n; i++) {
        cout << ans[i] << (i == n ? "\n" : " ");
    }

    return 0;
}
```

