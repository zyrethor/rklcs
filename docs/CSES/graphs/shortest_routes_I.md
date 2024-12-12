# Shortest Routes I

**Solution:** Dijkstra's algorithm

**Time Complexity:** $O(E \log V)$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pii pair<ll, int>

int main() {
  ios::sync_with_stdio(false);
  cin.tie(NULL);
  int n, m;
  cin >> n >> m;
  vector<vector<pair<int, ll>>> adj(n + 1);
  for (int i = 0; i < m; i++) {
    int a, b;
    ll c;
    cin >> a >> b >> c;
    adj[a].emplace_back(b, c);
  }

  vector<ll> dist(n+1, 1e18);
  dist[1] = 0;
  priority_queue<pii, vector<pii>, greater<pii>> pq;
  pq.emplace(0, 1);

  while (!pq.empty()) {
    auto [cur_dist, u] = pq.top();
    pq.pop();

    if (cur_dist > dist[u])
      continue;

    for (auto& [v, w] : adj[u]) {
      if (dist[v] > dist[u] + w) {
        dist[v] = dist[u] + w;
        pq.emplace(dist[v], v);
      }
    }
  }

  for (int i = 1; i <= n; i++) {
    cout << dist[i] << (i == n ? "\n" : " ");
  }
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pii pair<ll, int>

int main() {
    // Optimize input and output operations
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    
    int n, m;
    cin >> n >> m; // Read number of nodes and edges
    
    // Adjacency list where adj[u] contains pairs of (v, w)
    // representing an edge from u to v with weight w
    vector<vector<pair<int, ll>>> adj(n + 1);
    
    for (int i = 0; i < m; i++) {
        int a, b;
        ll c;
        cin >> a >> b >> c; // Read edge from a to b with weight c
        adj[a].emplace_back(b, c); // Add edge to adjacency list
    }

    // Initialize distance array with "infinity"
    const ll INF = 1e18;
    vector<ll> dist(n + 1, INF);
    dist[1] = 0; // Distance to source node is 0

    // Priority queue to select the node with the smallest tentative distance
    // pii is a pair of (distance, node)
    priority_queue<pii, vector<pii>, greater<pii>> pq;
    pq.emplace(0, 1); // Start with the source node

    while (!pq.empty()) {
        // Extract node with the smallest distance
        auto [cur_dist, u] = pq.top();
        pq.pop();

        // If the current distance is greater than the recorded distance, skip
        if (cur_dist > dist[u])
            continue;

        // Iterate over all adjacent edges of node u
        for (auto& [v, w] : adj[u]) {
            // If a shorter path to v is found through u
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w; // Update distance
                pq.emplace(dist[v], v); // Add to priority queue
            }
        }
    }

    // Output the shortest distances from node 1 to all other nodes
    for (int i = 1; i <= n; i++) {
        // If a node is unreachable, you might want to handle it differently
        // For example, print -1 or "INF" instead of 1e18
        cout << dist[i] << (i == n ? "\n" : " ");
    }

    return 0;
}
```

