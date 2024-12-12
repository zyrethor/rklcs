# Flight Routes

**Solution:** Dijkstra's algorithm

**Time Complexity:** $O(k \times E \times \log V)$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb emplace_back
#define pii pair<ll, int>
const ll INF = 1e18;


int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m, k;
  cin >> n >> m >> k;
  int a, b;
  ll c;

  vector<vector<pair<int, ll>>> adj(n+1);
  for (int i = 0; i < m; i++) {
    cin >> a >> b >> c;
    adj[a].emplace_back(b, c);
  }

  vector<int> count(n+1, 0);
  vector<ll> ans;
  priority_queue<pii, vector<pii>, greater<pii>> pq;
  pq.emplace(0, 1);

  while (!pq.empty()) {
    auto [cost, u] = pq.top();
    pq.pop();

    if (count[u] >= k)
      continue;

    count[u]++;

    if (u == n) {
      ans.pb(cost);
      if ((int)ans.size() == k)
        break;
    }

    for (auto& [to, w] : adj[u]) {
      if (count[to] < k)
        pq.emplace(cost + w, to);
    }

  }

  for (int i = 0; i < k; i++) {
    cout << ans[i] << (i == k-1 ? "\n" : " ");
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

// Define a large constant to represent infinity
const ll INF = 1e18;

int main() {
    // Optimize input and output operations for faster execution
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, k;
    cin >> n >> m >> k; // Read number of nodes, edges, and the value of k

    int a, b;
    ll c;

    // Adjacency list where adj[u] contains pairs (v, w)
    // representing an edge from u to v with weight w
    vector<vector<pair<int, ll>>> adj(n + 1);
    for (int i = 0; i < m; i++) {
        cin >> a >> b >> c;           // Read edge from a to b with weight c
        adj[a].emplace_back(b, c);    // Add edge to adjacency list
    }

    // Array to keep track of how many times each node has been reached
    vector<int> count(n + 1, 0);

    // Vector to store the costs of the first k shortest paths to node n
    vector<ll> ans;

    // Priority queue (min-heap) to store pairs of (cost, node)
    // The priority queue orders elements based on the smallest cost first
    priority_queue<pii, vector<pii>, greater<pii>> pq;

    // Initialize the priority queue with the starting node (1) and cost 0
    pq.emplace(0, 1);

    // Main loop to process nodes from the priority queue
    while (!pq.empty()) {
        auto [cost, u] = pq.top(); // Extract the node with the smallest cost
        pq.pop();

        // If the node has already been reached k times, skip it
        if (count[u] >= k)
            continue;

        // Increment the count for this node
        count[u]++;

        // If the destination node is reached, record the path cost
        if (u == n) {
            ans.pb(cost);             // Add the current path cost to the answer list
            if ((int)ans.size() == k) // If k paths have been found, terminate the loop
                break;
        }

        // Explore all adjacent edges from the current node
        for (auto& [to, w] : adj[u]) {
            // If the adjacent node hasn't been reached k times yet, enqueue it
            if (count[to] < k)
                pq.emplace(cost + w, to); // Add the new path with updated cost to the queue
        }
    }

    // After finding k paths, output their costs
    for (int i = 0; i < k; i++) {
        cout << ans[i] << (i == k - 1 ? "\n" : " ");
    }

    return 0;
}
```

