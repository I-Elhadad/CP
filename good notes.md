Given a number n, find sum of all [GCD](https://www.geeksforgeeks.org/greatest-common-divisor-gcd/)s that can be formed by selecting all the pairs from 1 to n.


![[Pasted image 20250518184934.png]]

```
//    #pragma GCC optimize ("O3")  
//    #pragma GCC optimize ("unroll-loops")  
//    #pragma comment(linker, "/STACK:2000000")  
#include<bits/stdc++.h>  
  
using namespace std;  
#define int long long  
  
#define ll long long  
//#define int long long  
#define ld long double  
#define endl "\n"  
#define FOCUS ios_base::sync_with_stdio(0);cin.tie(0);cout.tie(0);  
  
void Go() {  
    ios_base::sync_with_stdio(false), cin.tie(nullptr), cout.tie(nullptr);  
#ifndef ONLINE_JUDGE  
    freopen("input.txt", "r", stdin);  
    freopen("output.txt", "w", stdout);  
#endif  
}  
vector<int> Phi(int n) {  
    vector<int> phi(n + 1);  
    for (int i = 0; i <= n; i++)phi[i] = i;  
    for (int i = 2; i <= n; i++) {  
        if (phi[i] == i) {  
            for (int j = i; j <= n; j += i) {  
                phi[j] -= phi[j] / i;  
            }  
        }  
    }  
    return phi;  
}  
  
signed main() {  
//    Go();  
FOCUS  
    int N=1000001;  
    auto phi = Phi(N);  
    vector<ll>ans(N+1);  
    for(int i=1;i<=N;i++)  
    {  
        for(int j=i+i;j<=N;j+=i)  
        {  
            ans [j]+=i*phi[j/i];  
        }  
    }  
    for(int i=1;i<=N;i++)ans[i]+=ans[i-1];  
    int n;  
    while (cin>>n&&n)  
    {  
        cout<<ans[n]<<endl;  
    }  
  
    return 0;  
}
```


Given n, calculate the sum LCM(1, n) + LCM(2, n) + .. + LCM(n, n), where LCM(i, n) denotes the Least Common Multiple of the integers i and n.


![[Screenshot from 2025-05-18 19-30-01.png]]

```
//    #pragma GCC optimize ("O3")  
//    #pragma GCC optimize ("unroll-loops")  
//    #pragma comment(linker, "/STACK:2000000")  
#include<bits/stdc++.h>  
  
using namespace std;  
#define int long long  
  
#define ll long long  
//#define int long long  
#define ld long double  
#define endl "\n"  
#define FOCUS ios_base::sync_with_stdio(0);cin.tie(0);cout.tie(0);  
  
void Go() {  
    ios_base::sync_with_stdio(false), cin.tie(nullptr), cout.tie(nullptr);  
#ifndef ONLINE_JUDGE  
    freopen("input.txt", "r", stdin);  
    freopen("output.txt", "w", stdout);  
#endif  
}  
  
vector<int> Phi(int n) {  
    vector<int> phi(n + 1);  
    for (int i = 0; i <= n; i++)phi[i] = i;  
    for (int i = 2; i <= n; i++) {  
        if (phi[i] == i) {  
            for (int j = i; j <= n; j += i) {  
                phi[j] -= phi[j] / i;  
            }  
        }  
    }  
    return phi;  
}  
  
signed main() {  
    Go();  
    FOCUS  
    int N = 1000001;  
    auto phi = Phi(N);  
    vector<ll> ans(N + 1);  
    for (int i = 1; i <= N; i++) {  
        for (int j = i; j <= N; j += i) {  
            ans[j] += i * phi[i];  
        }  
    }  
    for(int i=1;i<=N;i++)  
    {  
        ans[i]++;  
        ans[i] /= 2;  
        ans[i] *= i;  
    }  
  
    return 0;  
}
```


### trick LCM Extreme
Find the result of the following code:

```
unsigned long long allPairLcm( int n ) {
    unsigned long long res = 0;
    for( int i = 1; i <= n; i++ )
        for( int j = i + 1; j <= n; j++ )
            res += lcm(i, j); // lcm means least common multiple
    return res;
}
```

```
vector<ull> LCM_Extreme(vector<int> phi, int N) {  
    vector<ull> ans(N + 1);  
    for (int i = 1; i <= N; i++) {  
        for (int j = i; j <= N; j += i) {  
            ans[j] += i * phi[i];  
        }  
    }  
    for (int i = 0; i <= N; i++) {  
        ans[i]++;  
        ans[i] /= 2;  
        ans[i] *= i;  
        ans[i] -= i;  
    }  
    for (int i = 1; i <= N; i++)ans[i] += ans[i - 1];  
    return ans;  
}
```


![[Pasted image 20250519042408.png]]