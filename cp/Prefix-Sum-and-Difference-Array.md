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
* Initialize difference array: `diff[x] = A[x]-A[x-1] for each x`
* Update difference array: `add c to all A[x] such that x1<=x<=x2`
    + `diff[x1]+c`
    + `diff[x2+1]-c`
* Run prefix sum on difference array to get back the original array
    + `Run diff[x] += diff[x-1] for all x, then the diff will becomes the original array`

## 2D Difference Array
* Initialize difference array by 0
* Update difference array: `add c to all A[x][y] such that x1<=x<=x2 and y1<=y<=y2`
    + `diff[x1][y1]+c`
    + `diff[x1][y2+1]-c`
    + `diff[x2+1][y1]-c`
    + `diff[x2+1][y2+1]+c`
* Run prefix sum on difference array and add A to it to get back the original array
    + `diff[x][y] += diff[x-1][y]+diff[x][y-1]-diff[x-1][y-1] for all x, y`
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
            diff[i][j] += a[i][j];
        }
    }
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            cout << diff[i][j] << " ";
        }
        cout << "\n";
    }
}
```
