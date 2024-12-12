# Investigation

**Solution:** Dijkstra's algorithm

**Time Complexity:** $O(E \log V)$

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

  int n, m;
  cin >> n >> m;
  int a, b, w;

  vector<vector<pair<int, int>>> adj(n+1);
  for (int i = 0; i < m; i++) {
    cin >> a >> b >> w;
    adj[a].pb(b, w);
  }

  vector<ll> dist(n+1, INF);
  vector<ll> ways(n+1, 0);
  vector<int> min_flights(n+1, 1e9);
  vector<int> max_flights(n+1, 0);

  dist[1] = 0;
  ways[1] = 1;
  min_flights[1] = 0;
  max_flights[1] = 0;

  priority_queue<pii, vector<pii>, greater<pii>> pq;
  pq.emplace(0, 1);

  while (!pq.empty()) {
    auto [cur_dist, u] = pq.top();
    pq.pop();

    if (cur_dist > dist[u]) continue;

    for (auto& [to, w] : adj[u]) {
      if (dist[to] > dist[u] + w) {
        dist[to] = dist[u] + w;
        ways[to] = ways[u];
        min_flights[to] = min_flights[u] + 1;
        max_flights[to] = max_flights[u] + 1;
        pq.emplace(dist[to], to);
      }
      else if (dist[to] == dist[u] + w) {
        ways[to] = (ways[to] + ways[u]) % MOD;
        min_flights[to] = min(min_flights[to], min_flights[u] + 1);
        max_flights[to] = max(max_flights[to], max_flights[u] + 1);
      }
    }
  }

  cout << dist[n] << " " << ways[n] << " " << min_flights[n] << " " << max_flights[n];

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
const int MOD = 1e9 + 7; // Modulo value for large numbers
const ll INF = 1e18;      // Representation of infinity

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

    // Initialize distance, ways, min_flights, and max_flights arrays
    vector<ll> dist(n + 1, INF);          // Shortest distance from node 1 to each node
    vector<ll> ways(n + 1, 0);            // Number of shortest paths to each node
    vector<int> min_flights(n + 1, 1e9);  // Minimum number of flights to each node
    vector<int> max_flights(n + 1, 0);    // Maximum number of flights to each node

    // Starting node initialization
    dist[1] = 0;           // Distance to itself is zero
    ways[1] = 1;           // One way to reach itself
    min_flights[1] = 0;   // Zero flights to reach itself
    max_flights[1] = 0;   // Zero flights to reach itself

    // Priority queue (min-heap) to store pairs (cost, node)
    // The priority queue orders elements based on the smallest cost first
    priority_queue<pii, vector<pii>, greater<pii>> pq;
    pq.emplace(0, 1); // Start with node 1 and cost 0

    // Main loop to process nodes from the priority queue
    while (!pq.empty()) {
        auto [cur_dist, u] = pq.top(); // Extract node with smallest cost
        pq.pop();

        // If the current distance is greater than the recorded distance, skip processing
        if (cur_dist > dist[u])
            continue;

        // Iterate over all outgoing edges from node u
        for (auto& [to, w] : adj[u]) {
            // Check if a shorter path to 'to' is found
            if (dist[to] > dist[u] + w) {
                // Update the shortest distance to 'to'
                dist[to] = dist[u] + w;

                // Update the number of ways to reach 'to' to be the same as 'u'
                ways[to] = ways[u];

                // Update the minimum number of flights to reach 'to'
                min_flights[to] = min_flights[u] + 1;

                // Update the maximum number of flights to reach 'to'
                max_flights[to] = max_flights[u] + 1;

                // Push the updated node into the priority queue
                pq.emplace(dist[to], to);
            }
            // If another shortest path to 'to' is found with the same distance
            else if (dist[to] == dist[u] + w) {
                // Increment the number of ways to reach 'to' modulo MOD
                ways[to] = (ways[to] + ways[u]) % MOD;

                // Update the minimum number of flights if the new path uses fewer flights
                min_flights[to] = min(min_flights[to], min_flights[u] + 1);

                // Update the maximum number of flights if the new path uses more flights
                max_flights[to] = max(max_flights[to], max_flights[u] + 1);
            }
        }
    }

    // After processing, check if node 'n' is reachable
    if (dist[n] == INF) {
        // If 'n' is unreachable, output -1 for all values
        cout << "-1 " << "0 " << "-1 " << "-1\n";
    }
    else {
        // Output the shortest distance, number of ways, minimum flights, and maximum flights
        cout << dist[n] << " " << ways[n] << " " << min_flights[n] << " " << max_flights[n];
    }

    return 0;
}
```

