### random
```
mt19937 random_seed(time(0));  
  
long long rnd(long long l, long long r) {  
    if (l > r) throw invalid_argument("parameters is invalid");  
    uniform_int_distribution<long long> dist(l, r);  
    return dist(random_seed);  
}
```