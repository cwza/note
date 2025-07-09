# Coordinate Compression
* https://www.bilibili.com/video/BV1Wz4y1K74C
* https://leetcode.cn/problems/xepqZ5/
``` cpp
using ll = long long;
const ll MAX = 1e18, MIN = -1e18;

class Solution {
public:
    int fieldOfGreatestBlessing(vector<vector<int>>& forceField) {
        vector<ll> xs = {MIN, MAX}, ys = {MIN, MAX};
        vector<tuple<ll, ll, ll, ll>> points;
        for(auto field : forceField) {
            // *2 to let 0.5 disappear
            ll x = field[0]*2;
            ll y = field[1]*2;
            ll side = field[2]*2;
            ll x1 = x-side/2;
            ll y1 = y-side/2;
            ll x2 = x+side/2;
            ll y2 = y+side/2;
            xs.push_back(x1);
            xs.push_back(x2);
            ys.push_back(y1);
            ys.push_back(y2);
            points.push_back({x1, y1, x2, y2});
        }
        // coordinate compression
        sort(xs.begin(), xs.end());
        xs.resize(unique(xs.begin(), xs.end())-xs.begin());
        sort(ys.begin(), ys.end());
        ys.resize(unique(ys.begin(), ys.end())-ys.begin());
        int xn = xs.size();
        int yn = ys.size();
        map<ll, int> mp_x, mp_y;
        for(int i = 0; i < xn; i++) mp_x[xs[i]] = i;
        for(int i = 0; i < yn; i++) mp_y[ys[i]] = i;

        // 2D difference array on compressed coordinates
        vector<vector<int>> diff(xn, vector<int>(yn));
        auto add = [&](int x1, int y1, int x2, int y2) -> void {
            diff[x1][y1]++;
            diff[x1][y2+1]--;
            diff[x2+1][y1]--;
            diff[x2+1][y2+1]++;
        };
        for(auto [x1, y1, x2, y2] : points) {
            add(mp_x[x1], mp_y[y1], mp_x[x2], mp_y[y2]);
        }
        for(int i = 1; i < xn; i++) {
            for(int j = 1; j < yn; j++) {
                diff[i][j] += diff[i-1][j]+diff[i][j-1]-diff[i-1][j-1];
            }
        }
        int ans = 0;
        for(int i = 1; i < xn; i++) {
            for(int j = 1; j < yn; j++) {
                ans = max(ans, diff[i][j]);
            }
        }
        return ans;
    }
};
```
