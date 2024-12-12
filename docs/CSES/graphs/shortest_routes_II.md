# Shortest Routes II

**Solution:** Floyd-Warshall algorithm

**Time Complexity:** $O(n^3)$

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pii pair<ll, int>
#define INF 1e18

ll dist_mat[505][505];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(NULL);
  int n, m, q;
  cin >> n >> m >> q;
  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= n; j++) {
      dist_mat[i][j] = (i == j ? 0 : INF);
    }
  }

  int a, b;
  ll c;
  for (int i = 0; i < m; i++) {
    cin >> a >> b >> c;
    if (c < dist_mat[a][b]) {
      dist_mat[a][b] = c;
      dist_mat[b][a] = c;
    }
  }

  for (int k = 1; k <= n; k++) {
    for (int i = 1; i <= n; i++) {
      if (dist_mat[i][k] == INF)
        continue;
      for (int j = 1; j <= n; j++) {
        if (dist_mat[k][j] == INF)
          continue;

        if (dist_mat[i][j] > dist_mat[i][k] + dist_mat[k][j])
          dist_mat[i][j] = dist_mat[i][k] + dist_mat[k][j];
      }
    }
  }

  while (q--) {
    cin >> a >> b;
    if (dist_mat[a][b] < INF) {
      cout << dist_mat[a][b] << "\n";
    }
    else {
      cout << "-1\n";
    }
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
#define pii pair<ll, int>
#define INF 1e18

// Define the maximum number of nodes based on the problem constraints
const int MAX_NODES = 505;

// Initialize the distance matrix with INF
ll dist_mat[MAX_NODES][MAX_NODES];

int main() {
    // Optimize input and output operations for faster execution
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    
    int n, m, q;
    cin >> n >> m >> q; // Read number of nodes, edges, and queries

    // Initialize the distance matrix
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (i == j)
                dist_mat[i][j] = 0; // Distance to self is zero
            else
                dist_mat[i][j] = INF; // Initialize other distances to infinity
        }
    }

    // Read and process all edges
    for (int i = 0; i < m; i++) {
        int a, b;
        ll c;
        cin >> a >> b >> c; // Read edge from node a to node b with weight c
        
        // Since the graph is undirected, update both [a][b] and [b][a]
        // Only update if the new edge has a smaller weight
        if (c < dist_mat[a][b]) {
            dist_mat[a][b] = c;
            dist_mat[b][a] = c;
        }
    }

    // Floyd-Warshall Algorithm to compute all-pairs shortest paths
    for (int k = 1; k <= n; k++) { // Iterate through each possible intermediate node
        for (int i = 1; i <= n; i++) { // Iterate through each source node
            // If the distance from i to k is already infinity, skip to avoid overflow
            if (dist_mat[i][k] == INF)
                continue;
            for (int j = 1; j <= n; j++) { // Iterate through each destination node
                // If the distance from k to j is infinity, skip
                if (dist_mat[k][j] == INF)
                    continue;

                // If a shorter path from i to j through k is found, update it
                if (dist_mat[i][j] > dist_mat[i][k] + dist_mat[k][j]) {
                    dist_mat[i][j] = dist_mat[i][k] + dist_mat[k][j];
                }
            }
        }
    }

    // Handle each query by outputting the shortest distance between two nodes
    while (q--) {
        int a, b;
        cin >> a >> b; // Read query nodes a and b
        if (dist_mat[a][b] < INF) {
            cout << dist_mat[a][b] << "\n"; // Output the shortest distance
        }
        else {
            cout << "-1\n"; // Output -1 if no path exists
        }
    }

    return 0;
}
```

