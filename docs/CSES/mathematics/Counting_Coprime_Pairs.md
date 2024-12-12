# Counting Coprime Pairs

notice long long

```c
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pb push_back

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);

  int n;
  cin >> n;
  vector<int> v(n);
  int mx = 0;
  for (auto& num : v) {
    cin >> num;
    mx = max(mx, num);
  }

  vector<int> freq(mx + 1, 0);
  for (auto num : v) {
    freq[num]++;
  }

  vector<int> mu(mx + 1, 1);
  vector<bool> isPrime(mx + 1, true);
  for (int i = 2; i <= mx; i++) {
    if (isPrime[i]) {
      for (int j = i; j <= mx; j+=i) {
        isPrime[j] = false;
        mu[j] *= -1;
      }

      ll sq = (ll)i * i;
      for (ll j = sq; j <= mx; j+=sq) {
        mu[j] = 0;
      }
    }
  }

  vector<int> multiple(mx + 1, 0);
  for (int i = 1; i <= mx; i++) {
    for (int j = i; j <= mx; j+=i) {
      multiple[i] += freq[j];
    }
  }

  ll ans = 0;
  for (int i = 1; i <= mx; i++) {
    if (mu[i] == 0)
      continue;

    ll cnt = (ll)multiple[i];
    if (cnt < 2)
      continue;

    ll pairs = (cnt * (cnt - 1)) / 2;
    ans += (mu[i] * pairs);
  }


  cout << ans;

  return 0;
}
```
