# Cycle Finding

**Solution:** Bellman-Ford algorithm

**Time Complexity:** $O(V \times E)$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb push_back
const ll INF = 1e18;


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

  for (int i = 0; i < m; i++) {
    cin >> ed[i].a >> ed[i].b >> ed[i].w;
  }

  
  vector<ll> dist(n+1, 0);
  vector<int> pred(n+1, -1);
  dist[0] = 0;
  int x = -1;

  for (int i= 1; i <= n; i++) {
    x = -1;
    for (auto& e : ed) {
      if (dist[e.b] > dist[e.a] + e.w) {
        dist[e.b] = dist[e.a] + e.w;
        pred[e.b] = e.a;
        x = e.b;
      }
    }
  }
 
  if (x == -1) {
    cout << "NO\n";
    return 0;
  }

  for (int i = 0; i < n; i++) {
    x = pred[x];
  }

  int start = x;
  vector<int> cycle;
  cycle.pb(start);
  x = pred[start];
  while (x != start) {
    cycle.pb(x);
    x = pred[x];
  }

  cycle.pb(start);
  reverse(cycle.begin(), cycle.end());
  cout << "YES\n";
  int len = cycle.size();
  for (int i = 0; i < len; i++) {
    cout << cycle[i] << (i == len - 1 ? "\n" : " ");
  }


  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb push_back
const ll INF = 1e18;

int main() {
    // Optimize input and output operations for faster execution
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m; // Read number of nodes and edges

    // Define the Edge structure to store edge information
    struct Edge {
        int a;    // Starting node of the edge
        int b;    // Ending node of the edge
        ll w;     // Weight of the edge
    };

    vector<Edge> ed(m); // Edge list to store all edges

    // Read all edges and store them in the edge list
    for (int i = 0; i < m; i++) {
        cin >> ed[i].a >> ed[i].b >> ed[i].w; // Read edge from a to b with weight w
    }

    // Initialize distance and predecessor arrays
    // dist[i]: minimum distance to node i
    // pred[i]: predecessor of node i in the shortest path
    vector<ll> dist(n + 1, INF);
    vector<int> pred(n + 1, -1);
    dist[0] = 0; // Node numbering starts from 1, so dist[0] is unused
    int x = -1;   // Variable to store the node updated in the last iteration

    // Perform Bellman-Ford algorithm for n iterations
    for (int i = 1; i <= n; i++) {
        x = -1; // Reset x at the start of each iteration
        for (auto& e : ed) {
            // Relax the edge if a shorter path is found
            if (dist[e.a] != INF && dist[e.b] > dist[e.a] + e.w) {
                dist[e.b] = dist[e.a] + e.w; // Update distance
                pred[e.b] = e.a;             // Update predecessor
                x = e.b;                      // Mark node b as updated
            }
        }
    }

    // After n iterations, check if any distance was updated
    if (x == -1) {
        // No updates in the nth iteration implies no negative cycle
        cout << "NO\n";
        return 0;
    }

    // To find the actual negative cycle, we need to backtrack
    // Start from the last updated node and follow predecessors
    for (int i = 0; i < n; i++) {
        x = pred[x]; // Move back n steps to ensure we are within the cycle
    }

    int start = x; // Starting node of the cycle
    vector<int> cycle; // Vector to store the cycle nodes
    cycle.pb(start);   // Add the starting node to the cycle
    x = pred[start];   // Move to the predecessor of the start node

    // Traverse the cycle until we return to the start node
    while (x != start) {
        cycle.pb(x);   // Add node to the cycle
        x = pred[x];   // Move to the predecessor
    }

    cycle.pb(start); // Complete the cycle by adding the start node again
    reverse(cycle.begin(), cycle.end()); // Reverse to get the correct order

    // Output the cycle
    cout << "YES\n";
    int len = cycle.size();
    for (int i = 0; i < len; i++) {
        cout << cycle[i] << (i == len - 1 ? "\n" : " ");
    }

    return 0;
}
```

