# High Score

**Solution:** Bellman-Ford algorithm

**Time Complexity:** $O(V \times E)$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb push_back


int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m;

  struct Edge {
    int a, b;
    ll w;
  };

  vector<Edge> ed(m);
  vector<vector<int>> adj(n+1, vector<int>());
  vector<vector<int>> rv_adj(n+1, vector<int>());

  // adj list
  for (int i = 0; i < m; i++) {
    cin >> ed[i].a >> ed[i].b >> ed[i].w;
    adj[ed[i].a].pb(ed[i].b);
    rv_adj[ed[i].b].pb(ed[i].a);
  }
  
  // bfs
  queue<int> q;
  
  vector<bool> reach_from_start(n+1, false);
  q.push(1);
  reach_from_start[1] = true;
  while (!q.empty()) {
    int v = q.front();
    q.pop();

    for (auto& to : adj[v]) {
      if (!reach_from_start[to]) {
        reach_from_start[to] = true;
        q.push(to);
      }
    }
  }

  vector<bool> reach_to_end(n+1, false);
  q.push(n);
  reach_to_end[n] = true;
  while (!q.empty()) {
    int v = q.front();
    q.pop();

    for (auto& to : rv_adj[v]) {
      if (!reach_to_end[to]) {
        reach_to_end[to] = true;
        q.push(to);
      }
    }
  }

  vector<int> relevant(n+1, false);
  for (int v = 1; v <= n; v++) {
    if (reach_from_start[v] && reach_to_end[v])
      relevant[v] = true;
  }

  // dp
  const ll NEG_INF = -1e18;
  vector<ll> dp(n+1, NEG_INF);
  dp[1] = 0;
  for (int i = 1; i <= n-1; i++) {
    bool updated = false;

    for (auto& e : ed) {
      if (relevant[e.a] && relevant[e.b]) {
        if (dp[e.a] != NEG_INF && dp[e.b] < dp[e.a] + e.w) {
          dp[e.b] = dp[e.a] + e.w;
          updated = true;
        }
      }
    }

    if (!updated)
      break;
  }

  bool has_cycle = false;
  for (auto& e : ed) {
    if (relevant[e.a] && relevant[e.b]) {
      if (dp[e.a] != NEG_INF && dp[e.b] < dp[e.a] + e.w) {
        has_cycle = true;
        break;
      }
    }
  }

  if (has_cycle)
    cout << "-1\n";
  else
    cout << dp[n] << "\n";


  return 0;
}
```

## Code with  Comment

```c++
#include <bits/stdc++.h>
using namespace std;

// Type definitions for convenience
#define ll long long
#define pb push_back

int main() {
    // Optimize input and output operations for faster execution
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m; // Read number of nodes and edges

    // Define the Edge structure to store edge information
    struct Edge {
        int a;   // Starting node of the edge
        int b;   // Ending node of the edge
        ll w;    // Weight of the edge
    };

    vector<Edge> ed(m); // Edge list to store all edges
    vector<vector<int>> adj(n + 1, vector<int>());      // Adjacency list for the original graph
    vector<vector<int>> rv_adj(n + 1, vector<int>());   // Adjacency list for the reverse graph

    // Read all edges and build the adjacency lists
    for (int i = 0; i < m; i++) {
        cin >> ed[i].a >> ed[i].b >> ed[i].w; // Read edge from a to b with weight w
        adj[ed[i].a].pb(ed[i].b);             // Add b to the adjacency list of a
        rv_adj[ed[i].b].pb(ed[i].a);          // Add a to the reverse adjacency list of b
    }

    // BFS to determine all nodes reachable from the start node (node 1)
    queue<int> q;
    vector<bool> reach_from_start(n + 1, false); // Boolean array to mark reachable nodes from start
    q.push(1);                                   // Start BFS from node 1
    reach_from_start[1] = true;                  // Mark start node as reachable

    while (!q.empty()) {
        int v = q.front(); // Current node
        q.pop();

        // Iterate over all neighbors of the current node
        for (auto& to : adj[v]) {
            if (!reach_from_start[to]) { // If the neighbor hasn't been visited
                reach_from_start[to] = true; // Mark as reachable
                q.push(to);                  // Add to the queue for further exploration
            }
        }
    }

    // BFS to determine all nodes that can reach the end node (node n) using the reverse graph
    vector<bool> reach_to_end(n + 1, false); // Boolean array to mark nodes that can reach end
    q.push(n);                                // Start BFS from node n
    reach_to_end[n] = true;                   // Mark end node as reachable to itself

    while (!q.empty()) {
        int v = q.front(); // Current node
        q.pop();

        // Iterate over all predecessors of the current node in the original graph
        for (auto& to : rv_adj[v]) {
            if (!reach_to_end[to]) { // If the predecessor hasn't been visited
                reach_to_end[to] = true; // Mark as able to reach end
                q.push(to);               // Add to the queue for further exploration
            }
        }
    }

    // Determine relevant nodes that are both reachable from start and can reach end
    vector<int> relevant(n + 1, false); // 1-based indexing; 0 is unused
    for (int v = 1; v <= n; v++) {
        if (reach_from_start[v] && reach_to_end[v])
            relevant[v] = true; // Mark as relevant if both conditions are met
    }

    // Dynamic Programming (DP) array to store maximum path sums
    const ll NEG_INF = -1e18;                   // Define negative infinity
    vector<ll> dp(n + 1, NEG_INF);             // Initialize all distances to negative infinity
    dp[1] = 0;                                 // Distance to start node is 0

    // Perform edge relaxation for (n-1) iterations to compute maximum path sums
    for (int i = 1; i <= n - 1; i++) {
        bool updated = false; // Flag to check if any update occurs in this iteration

        // Iterate over all edges to perform relaxation
        for (auto& e : ed) {
            // Only consider edges between relevant nodes
            if (relevant[e.a] && relevant[e.b]) {
                // If the starting node has been reached and updating gives a better path
                if (dp[e.a] != NEG_INF && dp[e.b] < dp[e.a] + e.w) {
                    dp[e.b] = dp[e.a] + e.w; // Update the maximum path sum to node b
                    updated = true;           // Mark that an update has occurred
                }
            }
        }

        // If no updates occurred in this iteration, no further improvements are possible
        if (!updated)
            break;
    }

    // After (n-1) iterations, check for the presence of positive weight cycles
    bool has_cycle = false;
    for (auto& e : ed) {
        if (relevant[e.a] && relevant[e.b]) {
            // If further relaxation is possible, a positive cycle exists
            if (dp[e.a] != NEG_INF && dp[e.b] < dp[e.a] + e.w) {
                has_cycle = true; // Positive cycle detected
                break;
            }
        }
    }

    // Output the result based on the presence of a positive cycle
    if (has_cycle)
        cout << "-1\n";          // Output -1 if a positive cycle exists
    else
        cout << dp[n] << "\n";   // Output the maximum path sum to node n

    return 0;
}
```

