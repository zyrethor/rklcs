# Mobius Function


## Code
```c
int mu[n], minD[n];
vector<int> divs[n];

void precalcDivs(int n) {
  for (int d = 1; d < n; d++) {
    for (int v = d; v < n; v+=d) {
      divs[v].pb(d);
    }
  }

  mu[1] = 1;

  for (int d = 2; d < n; d++) {
    if (minD[d] == 0)
      minD[d] = d;

    if (minD[d] != minD[d / minD[d]])
      mu[d] = -mu[d / minD[d]];

    for (int v = 2 * d; v < n; v+=d) {
      if (minD[v] == 0)
        minD[v] = d;
    }
  }
}
```

## Code with Comment
```c
#include <bits/stdc++.h>
using namespace std;

#define pb push_back

const int n = 10;
// mu = Mobius function
// minD = smallest prime factor of a number
// divs = list of divisors for each number
int mu[n], minD[n];
vector<int> divs[n];

void precalcDivs(int n) {
  // Store each number's divisors in divs array
  for (int d = 1; d < n; d++) {
    for (int v = d; v < n; v+=d) {
      divs[v].pb(d);
    }
  }

  // mo(1) = 1
  mu[1] = 1;

  for (int d = 2; d < n; d++) {
    // If minD[d] is still 0, it means d is a prime number
    if (minD[d] == 0)
      minD[d] = d;

    // Determine the value of mu[d]:
    // If d is divisible by its smallest prime factor more than once, mu[d] is 0 (as it's not square-free)
    // Otherwise, mu[d] is -1 or 1 depending on the number of prime factors (calculated recursively)
    if (minD[d] != minD[d / minD[d]])
      mu[d] = -mu[d / minD[d]];

    // Set the smallest prime factor for all multiples of d that haven't been assigned yet
    for (int v = 2 * d; v < n; v+=d) {
      if (minD[v] == 0)
        minD[v] = d;
    }

  }
}

int main() {
  precalcDivs(n);

  for (int i = 0; i<n;i++) {
    cout << mu[i] << " ";
  }
  cout << endl;

  return 0;
}
```
