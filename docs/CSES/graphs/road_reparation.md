# Road Reparation

**Solution:** Kruskal’s Algorithm

**Time Complexity:** $O(m \log m)$ Sort + $O(m \alpha(n))$ DSU

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

struct DSU {
  vector<int> p, size;

  DSU() {};
  DSU(int n) {
    init(n);
  }

  void init(int n) {
    p.resize(n+1);
    iota(p.begin(), p.end(), 0);
    size.assign(n+1, 1);
  }

  int find(int x) {
    if (x == p[x]) return x;
    return p[x] = find(p[x]);
  }

  bool same(int x, int y) {
    return find(x) == find(y);
  }

  bool merge(int a, int b) {
    a = find(a);
    b = find(b);
    if (a != b) {
      if (size[a] < size[b])
        swap(a, b);
      p[b] = a;
      size[a] += size[b];
      return true;
    }
    return false;
  }
};

struct Edge {
  int a, b;
  ll c;
};

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m;
  vector<Edge> edges(m);
  for (int i = 0; i < m; i++) {
    cin >> edges[i].a >> edges[i].b >> edges[i].c;
  }

  sort(edges.begin(), edges.end(), [](auto& a, auto& b) {
    return a.c < b.c;
  });

  DSU dsu(n);
  ll total_cost = 0;
  int cnt = 0;

  for (auto& [a, b, c] : edges) {
    if (dsu.merge(a, b)) {
      total_cost += c;
      cnt++;
      if (cnt == n-1) break;
    }
  }

  if (cnt < n-1) {
    cout << "IMPOSSIBLE";
  }
  else {
    cout << total_cost;
  }

  return 0;
}
```

## Code with Comment

```c++
#include <bits/stdc++.h>
using namespace std;

// Define a type alias for long long to simplify usage
typedef long long ll;

// Structure to represent a Disjoint Set Union (Union-Find) data structure
struct DSU {
    vector<int> p;    // Parent array
    vector<int> size; // Size array to keep track of the size of each set

    // Default constructor
    DSU() {};

    // Constructor that initializes the DSU with 'n' elements
    DSU(int n) {
        init(n);
    }

    // Initialize the DSU with 'n' elements
    void init(int n) {
        p.resize(n + 1);          // Resize parent array to hold n+1 elements (1-based indexing)
        iota(p.begin(), p.end(), 0); // Initialize parent of each element to itself
        size.assign(n + 1, 1);    // Initialize size of each set to 1
    }

    // Find the representative (root) of the set that element 'x' belongs to
    int find(int x) {
        if (x == p[x]) return x;      // If 'x' is its own parent, it's the root
        return p[x] = find(p[x]);     // Path compression: update parent of 'x' to its root
    }

    // Check if elements 'x' and 'y' are in the same set
    bool same(int x, int y) {
        return find(x) == find(y);
    }

    // Merge the sets containing elements 'a' and 'b'
    // Returns true if a merge happened, false if they were already in the same set
    bool merge(int a, int b) {
        a = find(a); // Find root of 'a'
        b = find(b); // Find root of 'b'
        if (a != b) { // If they are in different sets
            if (size[a] < size[b]) // Union by size: attach smaller set to larger set
                swap(a, b);
            p[b] = a;              // Make 'a' the parent of 'b'
            size[a] += size[b];    // Update the size of the set containing 'a'
            return true;           // Indicate that a merge occurred
        }
        return false; // No merge needed as they are already in the same set
    }
};

// Structure to represent an edge in the graph
struct Edge {
    int a, b;    // Endpoints of the edge
    ll c;        // Cost or weight of the edge
};

int main() {
    ios::sync_with_stdio(false); // Optimize IO operations
    cin.tie(nullptr);            // Untie cin from cout for faster input

    int n, m; // 'n' is the number of nodes, 'm' is the number of edges
    cin >> n >> m;
    vector<Edge> edges(m); // Vector to store all edges

    // Read all edges
    for (int i = 0; i < m; i++) {
        cin >> edges[i].a >> edges[i].b >> edges[i].c;
    }

    // Sort the edges in non-decreasing order of their cost using a lambda comparator
    sort(edges.begin(), edges.end(), [](auto& a, auto& b) {
        return a.c < b.c;
    });

    DSU dsu(n);       // Initialize DSU with 'n' nodes
    ll total_cost = 0; // Variable to accumulate the total cost of the MST
    int cnt = 0;        // Counter to keep track of the number of edges added to the MST

    // Iterate through the sorted edges
    for (auto& [a, b, c] : edges) {
        // Attempt to merge the sets containing nodes 'a' and 'b'
        if (dsu.merge(a, b)) {
            total_cost += c; // If merge is successful, add the edge's cost to total_cost
            cnt++;           // Increment the edge counter
            if (cnt == n - 1) break; // If we've added (n-1) edges, the MST is complete
        }
    }

    // After processing all edges, check if a valid MST was formed
    if (cnt < n - 1) {
        cout << "IMPOSSIBLE"; // If not enough edges to connect all nodes, output "IMPOSSIBLE"
    }
    else {
        cout << total_cost;   // Otherwise, output the total cost of the MST
    }

    return 0; // End of the program
}
```
