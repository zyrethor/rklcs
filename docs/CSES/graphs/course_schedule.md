# Course Schedule

**Solution:** Topological order

**Time Complexity:** $O(V+E)$

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

  int n, m;
  cin >> n >> m;
  int a, b;

  vector<vector<int>> adj(n+1);
  vector<int> in_degree(n+1);
  for (int i = 0; i < m; i++) {
    cin >> a >> b;
    adj[a].pb(b);
    in_degree[b]++;
  }

  queue<int> q;
  for (int i = 1; i <= n; i++) {
    if (in_degree[i] == 0)
      q.push(i);
  }

  vector<int> ans;
  while (!q.empty()) {
    int u = q.front();
    q.pop();

    ans.pb(u);
    for (auto & to : adj[u]) {
      in_degree[to]--;
      if (in_degree[to] == 0)
        q.push(to);
    }
  }

  int len = ans.size();
  if (len == n) {
    for (int i = 0; i < len; i++) {
      cout << ans[i] << (i == len-1 ? "\n" : " ");
    }
  }
  else {
    cout << "IMPOSSIBLE";
  }

  return 0;
}
```

## Code with comment

```c++
#include <bits/stdc++.h>
using namespace std;

// Define macros for convenience
#define ll long long            // Shorthand for 'long long'
#define pb emplace_back        // Shorthand for 'emplace_back' method of vectors
#define pii pair<ll, int>       // Shorthand for a pair consisting of 'long long' and 'int'
const ll INF = 1e18;             // A large constant value, often used as infinity

int main() {
    // Optimize I/O operations
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m; // 'n' = number of vertices, 'm' = number of edges
    cin >> n >> m;

    int a, b; // Variables to store edge information

    // Initialize adjacency list and in-degree array
    // Using (n + 1) to make the vertex indexing 1-based
    vector<vector<int>> adj(n + 1, vector<int>()); // Adjacency list
    vector<int> in_degree(n + 1, 0);              // In-degree of each vertex

    // Read all edges and build the adjacency list and in-degree array
    for (int i = 0; i < m; i++) {
        cin >> a >> b;    // Read edge from vertex 'a' to vertex 'b'
        adj[a].pb(b);    // Add 'b' to the adjacency list of 'a'
        in_degree[b]++;   // Increment the in-degree of vertex 'b'
    }

    queue<int> q; // Queue to store vertices with zero in-degree

    // Enqueue all vertices with zero in-degree
    for (int i = 1; i <= n; i++) {
        if (in_degree[i] == 0) {
            q.push(i);
        }
    }

    vector<int> ans; // Vector to store the topological order

    // Perform Kahn's Algorithm for Topological Sorting
    while (!q.empty()) {
        int u = q.front(); // Get the vertex at the front of the queue
        q.pop();           // Remove it from the queue

        ans.pb(u);         // Add the vertex to the topological order

        // Iterate through all adjacent vertices of 'u'
        for (auto &to : adj[u]) {
            in_degree[to]--; // Decrement the in-degree as 'u' is processed

            // If in-degree becomes zero, enqueue the vertex
            if (in_degree[to] == 0) {
                q.push(to);
            }
        }
    }

    // Check if topological sorting was possible (i.e., no cycles)
    if (ans.size() == n) {
        // If possible, print the topological order
        for (int i = 0; i < ans.size(); i++) {
            cout << ans[i];
            if (i != ans.size() - 1)
                cout << " "; // Add space between numbers except after the last one
            else
                cout << "\n"; // Add a newline after the last number
        }
    }
    else {
        // If not possible (due to a cycle), print "IMPOSSIBLE"
        cout << "IMPOSSIBLE";
    }

    return 0; // Indicate successful termination
}
```

