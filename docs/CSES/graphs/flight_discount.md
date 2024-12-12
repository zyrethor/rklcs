# Flight Discount

**Solution:** Dijkstra's algorithm

**Time Complexity:** $O((V+E) \log (V))$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb push_back
#define pii pair<int, int>
#define tp tuple<ll, int, int>

const ll INF = 1e18;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m;
  int a, b, c;

  vector<vector<pii>> adj(n+1);
  for (int i = 0; i < m; i++) {
    cin >> a >> b >> c;
    adj[a].emplace_back(b, c);
  }

  vector<ll> d0(n+1, INF);
  vector<ll> d1(n+1, INF);
  d0[1] = 0;

  // pq<cost, node, state>
  priority_queue<tp, vector<tp>, greater<tp>> pq;
  pq.emplace(0, 1, 0);
  while (!pq.empty()) {
    auto [cost, v, state] = pq.top();
    pq.pop();

    if (state == 0 && cost > d0[v]) continue;
    if (state == 1 && cost > d1[v]) continue;

    for (auto& [to, weight] : adj[v]) {
      ll w = (ll)weight;
      if (state == 0) {
        if (d0[to] > d0[v] + w) {
          d0[to] = d0[v] + w;
          pq.emplace(d0[to], to, 0);
        }

        ll new_cost = d0[v] + w / 2;
        if (d1[to] > new_cost) {
          d1[to] = new_cost;
          pq.emplace(d1[to], to, 1);
        }
      }
      else {
        if (d1[to] > d1[v] + w) {
          d1[to] = d1[v] + w;
          pq.emplace(d1[to], to, 1);
        }

      }
    }
  }
  
  cout << min(d0[n], d1[n]);

  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

// Type definitions for convenience
#define ll long long
#define pb push_back
#define pii pair<int, int>
#define tp tuple<ll, int, int>

const ll INF = 1e18;

int main() {
    // Optimize input and output operations for faster execution
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m; // Read number of nodes and edges

    int a, b, c;

    // Adjacency list where adj[u] contains pairs (v, w)
    // representing an edge from u to v with weight w
    vector<vector<pii>> adj(n + 1, vector<pii>());
    for (int i = 0; i < m; i++) {
        cin >> a >> b >> c;       // Read edge from a to b with weight c
        adj[a].emplace_back(b, c); // Add edge to adjacency list
    }

    // Initialize distance arrays
    // d0[i]: minimum cost to reach node i without using the discount
    // d1[i]: minimum cost to reach node i after using the discount
    vector<ll> d0(n + 1, INF);
    vector<ll> d1(n + 1, INF);
    d0[1] = 0; // Starting node has a distance of 0 without discount

    // Priority queue to select the next node with the smallest cost
    // Each entry is a tuple (cost, node, state)
    // state: 0 -> discount not used, 1 -> discount used
    priority_queue<tp, vector<tp>, greater<tp>> pq;
    pq.emplace(0, 1, 0); // Start with node 1, cost 0, discount not used

    while (!pq.empty()) {
        auto [cost, v, state] = pq.top(); // Current state
        pq.pop();

        // Skip processing if we have already found a better path
        if (state == 0 && cost > d0[v]) continue;
        if (state == 1 && cost > d1[v]) continue;

        // Iterate over all outgoing edges from node v
        for (auto& [to, weight] : adj[v]) {
            ll w = static_cast<ll>(weight); // Convert weight to long long

            if (state == 0) { // Discount not used yet
                // Case 1: Do not use the discount on this edge
                if (d0[to] > d0[v] + w) {
                    d0[to] = d0[v] + w;       // Update distance without discount
                    pq.emplace(d0[to], to, 0); // Push to queue with state 0
                }

                // Case 2: Use the discount on this edge
                // The discount reduces the weight by half
                ll new_cost = d0[v] + w / 2;
                if (d1[to] > new_cost) {
                    d1[to] = new_cost;          // Update distance with discount used
                    pq.emplace(d1[to], to, 1);  // Push to queue with state 1
                }
            }
            else { // state == 1, discount has already been used
                // Only option is to traverse the edge without discount
                if (d1[to] > d1[v] + w) {
                    d1[to] = d1[v] + w;       // Update distance after discount
                    pq.emplace(d1[to], to, 1); // Push to queue with state 1
                }
            }
        }
    }

    // The minimum cost to reach node n is the minimum of d0[n] and d1[n]
    cout << min(d0[n], d1[n]);

    return 0;
}
```

