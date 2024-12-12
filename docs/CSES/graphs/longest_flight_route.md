# Longest Flight Route

## Dijkstra Algorithm O((V+E) log V) TLE

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb emplace_back
#define pii pair<int, int>
const ll INF = 1e18;


int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m;
  int a, b;

  vector<vector<pii>> adj(n+1);
  for (int i = 0; i < m; i++) {
    cin >> a >> b;
    adj[a].pb(b, 1);
  }

  priority_queue<pii, vector<pii>> pq; 
  pq.emplace(0, 1);
  const int NEG_INF = -1e9;
  vector<int> dist(n+1, NEG_INF);
  vector<int> pred(n+1, -1);
  dist[1] = 0;

  while (!pq.empty()) {
    auto [cur_dist, u] = pq.top();
    pq.pop();

    if (cur_dist < dist[u])
      continue;

    for (auto& [to, w] : adj[u]) {
      if (dist[to] < dist[u] + w) {
        dist[to] = dist[u] + w;
        pred[to] = u;
        pq.emplace(dist[to], to);
      }
    }
  }

  if (dist[n] == NEG_INF) {
    cout << "IMPOSSIBLE";
    return 0;
  }

  cout << dist[n] + 1 << "\n";

  int start = n;
  vector<int> ans;
  ans.pb(start);
  while (pred[start] != -1) {
    start = pred[start];
    ans.pb(start);
  }
  reverse(ans.begin(), ans.end());
  int len = ans.size();
  for (int i = 0; i < len; i++) {
    cout << ans[i] << (i == len-1 ? "\n" : " ");
  }

  return 0;
}
```

## Topological Sort (V + E)

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb emplace_back
#define pii pair<int, int>
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

  vector<int> topo_order;
  while (!q.empty()) {
    int u = q.front();
    q.pop();

    topo_order.pb(u);
    for (auto & to : adj[u]) {
      in_degree[to]--;
      if (in_degree[to] == 0)
        q.push(to);
    }
  }

  // check if a graph has a cycle
  if (topo_order.size() != n) {
    cout << "IMPOSSIBLE";
    return 0;
  }

  const int NEG_INF = -1e9;
  vector<int> dist(n+1, NEG_INF);
  vector<int> pred(n+1, -1);
  dist[1] = 0;

  for (auto& v : topo_order) {
    if (dist[v] == NEG_INF)
      continue;

    for (auto &to : adj[v]) {
      if (dist[to] < dist[v] + 1) {
        dist[to] = dist[v] + 1;
        pred[to] = v;
      }
    }
  }

  if (dist[n] == NEG_INF) {
    cout << "IMPOSSIBLE";
    return 0;
  }

  vector<int> path;
  int cur = n;
  while (cur != -1) {
    path.pb(cur);
    cur = pred[cur];
  }

  reverse(path.begin(), path.end());

  cout << dist[n] + 1 << "\n";
  int len = path.size();
  for (int i = 0; i < len; i++) {
    cout << path[i] << (i == len-1 ? "\n" : " ");
  }

  return 0;
}
```

## BFS find shortest route in unweighed graph

```c++
#include <bits/stdc++.h>
using namespace std;

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;
    int a, b;
    vector<vector<int>> adj(n + 1);
    for(int i=0; i<m; i++){
        cin >> a >> b;
        adj[a].emplace_back(b);
    }

    int source = 1, destination = n;
    vector<int> dist(n + 1, -1);
    vector<int> pred(n + 1, -1);
    queue<int> q;

    q.push(source);
    dist[source] = 0;

    while(!q.empty()){
        int u = q.front(); q.pop();
        for(auto &v : adj[u]){
            if(dist[v] == -1){
                dist[v] = dist[u] + 1;
                pred[v] = u;
                q.push(v);
            }
        }
    }

    if(dist[destination] == -1){
        cout << "IMPOSSIBLE";
        return 0;
    }

    // Reconstruct the path
    vector<int> path;
    int current = destination;
    while(current != -1){
        path.push_back(current);
        current = pred[current];
    }
    reverse(path.begin(), path.end());

    // Output
    cout << dist[destination] << "\n";
    for(int i=0; i<path.size(); i++){
        cout << path[i] << (i == path.size()-1 ? "\n" : " ");
    }

    return 0;
}
```

