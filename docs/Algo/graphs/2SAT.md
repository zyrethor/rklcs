# 2 SAT

## Code

```c++
struct TwoSat : SCC {
  TwoSat(int n_) : SCC(n_) {}

  void orr(int x, int y) {
    if ((x ^ y) == 1) return;
    add_edge(x ^ 1, y);
    add_edge(y ^ 1, x);
  }

  vector<int> solve_2sat() {
    solve_scc();
    vector<int> res(n);
    for (int i = 0; i < n; i+=2) {
      if (idx[i] == idx[i+1])
        return {};
    }

    for (int i = 0; i < n; i++) {
      res[i] = idx[i] < idx[i^1];
    }
    return res;
  }
};
```

## Example

```c++
int n, m;
cin >> n >> m;

TwoSat ts(2*m);
char c1, c2;
int a, b;
for (int i = 0; i < n; i++) {
	cin >> c1 >> a >> c2 >> b;
	a--; b--;
	a = 2 * a + (c1 == '-' ? 1 : 0);
	b = 2 * b + (c2 == '-' ? 1 : 0);
	ts.orr(a, b);
}
```

