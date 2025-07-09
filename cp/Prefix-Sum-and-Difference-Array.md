# Prefix Sum and Difference Array
##  Prefix Sum

### 1D Prefix Sum
* `pref[x]: sum of {A[i] | i<=x}`
    + `pref[i] = pref[i-1]+A[i]`
* `sum of {A[x] | x1<=x<=x2} = pref[x2]-pref[x1-1]`

### 2D Prefix Sum
* `pref[x][y]: sum of {A[i][j] | i<=x and j<=y}`
    + `pref[i][j] = pref[i-1][j]+pref[i][j-1]-pref[i-1][j-1]+A[i][j]`
    + `pref[i][j] = A[i][j] => pref[i][j] += pref[i-1][j] => pref[i][j] += pref[i][j-1]`
* `sum of {A[x][y] | x1<=x<=x2 and y1<=y<=y2} = pref[x2][y2]-pref[x1-1][y2]-pref[x2][y1-1]+pref[x1-1][y1-1]`
``` cpp
vector<vector<int>> pref(n+1, vector<int>(m+1));
for(int i = 1; i <= n; i++) {
    for(int j = 1; j <= m; j++) {
        pref[i][j] = pref[i-1][j]+pref[i][j-1]-pref[i-1][j-1]+a[i][j];
    }
}
auto query = [&](int x1, int y1, int x2, int y2) -> int {
    return pref[x2][y2]-pref[x1-1][y2]-pref[x2][y1-1]+pref[x1-1][y1-1];
};
```

### 3D Prefix Sum
* `pref[x][y][z]: sum of {A[i][j][k] | i<=x and j<=y and k<=z}`
    + `pref[i][j][k] = pref[i-1][j][k]+pref[i][j-1][k]+pref[i][j][k-1]-pref[i-1][j-1][k]-pref[i][j-1][k-1]-pref[i-1][j][k-1]+pref[i-1][j-1][k-1]+A[i][j][k]`
    + `pref[i][j][k] = A[i][j][k] => pref[i][j][k] += pref[i-1][j][k] => pref[i][j][k] += pref[i][j-1][k] => pref[i][j][k] += pref[i][j][k-1]`
* `sum of {A[x][y][z] | x1<=x<=x2 and y1<=y<=y2 and z1<=z<=z2} = pref[x2][y2][z2]-pref[x1-1][y2][z2]-pref[x2][y1-1][z2]-pref[x2][y2][z1-1]+pref[x1-1][y1-1][z2]+pref[x1-1][y2][z1-1]+pref[x2][y1-1][z1-1]-pref[x1-1][y1-1][z1-1]`
``` cpp
int main() {
    vector<vector<vector<int>>> pref(n+1, vector<vector<int>>(n+1, vector<int>(n+1)));
    // for(int i = 1; i <= n; i++) {
    //     for(int j = 1; j <= n; j++) {
    //         for(int k = 1; k <= n; k++) {
    //             pref[i][j][k] = pref[i-1][j][k]+pref[i][j-1][k]+pref[i][j][k-1]-pref[i-1][j-1][k]-pref[i][j-1][k-1]-pref[i-1][j][k-1]+pref[i-1][j-1][k-1]+inp[i][j][k];
    //         }
    //     }
    // }

    // init
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            for(int k = 1; k <= n; k++) {
                pref[i][j][k] = inp[i][j][k];
            }
        }
    }
    // sum up x
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            for(int k = 1; k <= n; k++) {
                pref[i][j][k] += pref[i-1][j][k];
            }
        }
    }
    // sum up y
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            for(int k = 1; k <= n; k++) {
                pref[i][j][k] += pref[i][j-1][k];
            }
        }
    }
    // sum up z
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            for(int k = 1; k <= n; k++) {
                pref[i][j][k] += pref[i][j][k-1];
            }
        }
    }
    
    int q;
    cin >> q;
    while(q--) {
        int x1, x2, y1, y2, z1, z2;
        cin >> x1 >> x2 >> y1 >> y2 >> z1 >> z2;
        cout << pref[x2][y2][z2]-pref[x1-1][y2][z2]-pref[x2][y1-1][z2]-pref[x2][y2][z1-1]+pref[x1-1][y1-1][z2]+pref[x1-1][y2][z1-1]+pref[x2][y1-1][z1-1]-pref[x1-1][y1-1][z1-1] << "\n";
    }
}
```

## Difference Array
* Run the prefix sum to difference array will result to original array

### 1D Difference Array
* Initialize difference array by 0
* Update difference array: `add c to all A[x] such that x1<=x<=x2`
    + `diff[x1] += c`
    + `diff[x2+1] -= c`
* Run prefix sum on difference array and add A to it to get back the original array
    + `diff[x] += diff[x-1] for all x`: This is 1D prefix sum
    + `diff[x] += a[x] for all x`

### 2D Difference Array
* Initialize difference array by 0
* Update difference array: `add c to all A[x][y] such that x1<=x<=x2 and y1<=y<=y2`
    + `diff[x1][y1] += c`
    + `diff[x1][y2+1] -= c`
    + `diff[x2+1][y1] -= c`
    + `diff[x2+1][y2+1] += c`
* Run prefix sum on difference array and add A to it to get back the original array
    + `diff[x][y] += diff[x-1][y]+diff[x][y-1]-diff[x-1][y-1] for all x, y`: This is 2D prefix sum
    + `diff[x][y] += a[x][y] for all x, y`
``` c++
int main() {
    int n, q;
    cin >> n >> q;
    vector<vector<int>> a(n+1, vector<int>(n+1));
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) cin >> a[i][j];
    }

    vector<vector<int>> diff(n+2, vector<int>(n+2));
    while(q--) {
        int x1, y1, x2, y2;
        cin >> x1 >> y1 >> x2 >> y2;
        diff[x1][y1]++;
        diff[x1][y2+1]--;
        diff[x2+1][y1]--;
        diff[x2+1][y2+1]++;
    }
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            diff[i][j] += diff[i-1][j]+diff[i][j-1]-diff[i-1][j-1];
        }
    }
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            diff[i][j] += a[i][j];
            cout << diff[i][j] << " ";
        }
        cout << "\n";
    }
}
```

### Arithmetic Array
* Given array A, we want to add a arithmetic series from index l to index r
    + arithmetic series: start on s, end on e, common difference d
* https://www.bilibili.com/video/BV1Gp4y1E7Jp
* Initialize Ari array by 0
* Update array: ``
    + `Ari[l] += s`
    + `Ari[l+1] += d-s`
    + `Ari[r+1] -= d+e`
    + `Ari[r+2] += e`
* Run prefix sum *2 times* on Ari array and add A to it to get back the original array
    + `Ari[x] += Ari[x-1] for all x`
    + `Ari[x] += Ari[x-1] for all x`
    + `Ari[x] += A[x] for all x`
* https://www.luogu.com.cn/problem/P4231
``` cpp
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    int n, m;
    cin >> n >> m;

    vector<ll> a(n+3);
    while(m--) {
        int l, r;
        ll s, e;
        cin >> l >> r >> s >> e;
        ll d = (e-s)/(r-l);
        a[l] += s;
        a[l+1] += d-s;
        a[r+1] -= d+e;
        a[r+2] += e;
    }
    for(int i = 1; i <= n; i++) a[i] += a[i-1];
    for(int i = 1; i <= n; i++) a[i] += a[i-1];

    ll ans1 = 0, ans2 = 0;
    for(int i = 1; i <= n; i++) {
        ans1 ^= a[i];
        ans2 = max(ans2, a[i]);
    }
    cout << ans1 << " " << ans2 << "\n";
}
```
