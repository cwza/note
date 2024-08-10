# Prefix Sum

## 1D Prefix Sum
* `pref[i] = pref[i-1]+A[i]`
* `sum of {A[x] | lx<=x<=rx} = pref[x]-pref[x-1]`

## 2D Prefix Sum
* `pref[i][j] = pref[i-1][j]+pref[i][j-1]-pref[i-1][j-1]+A[i][j]`
* `sum of {A[x][y] | lx<=x<=rx and ly<=y<=ry} = pref[rx][ry]-pref[lx-1][ry]-pref[rx][ly-1]+pref[lx-1][ly-1]`

## 3D Prefix Sum
* `pref[i][j][k] = pref[i-1][j][k]+pref[i][j-1][k]+pref[i][j][k-1]-pref[i-1][j-1][k]-pref[i][j-1][k-1]-pref[i-1][j][k-1]+pref[i-1][j-1][k-1]+A[i][j][k]`
* `sum of {A[x][y][z] | lx<=x<=rx and ly<=y<=ry and lz<=z<=rz} = pref[rx][ry][rz]-pref[lx-1][ry][rz]-pref[rx][ly-1][rz]-pref[rx][ry][lz-1]+pref[lx-1][ly-1][rz]+pref[lx-1][ry][lz-1]+pref[rx][ly-1][lz-1]-pref[lx-1][ly-1][lz-1]`
``` cpp
int main() {
    vector<vector<vector<int>>> pref(n+1, vector<vector<int>>(n+1, vector<int>(n+1)));
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            for(int k = 1; k <= n; k++) {
                pref[i][j][k] = pref[i-1][j][k]+pref[i][j-1][k]+pref[i][j][k-1]-pref[i-1][j-1][k]-pref[i][j-1][k-1]-pref[i-1][j][k-1]+pref[i-1][j-1][k-1]+inp[i][j][k];
            }
        }
    }
    
    int q;
    cin >> q;
    while(q--) {
        int lx, rx, ly, ry, lz, rz;
        cin >> lx >> rx >> ly >> ry >> lz >> rz;
        cout << pref[rx][ry][rz]-pref[lx-1][ry][rz]-pref[rx][ly-1][rz]-pref[rx][ry][lz-1]+pref[lx-1][ly-1][rz]+pref[lx-1][ry][lz-1]+pref[rx][ly-1][lz-1]-pref[lx-1][ly-1][lz-1] << "\n";
    }
}
```
