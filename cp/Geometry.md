# Geometry

## Find the intersect of 2 rectangle (Assume sides parallel to the coordinate axes)
Given the bottom-left and top-right coordinates of 2 rectangles, Find the bottom-left and top-right coordinates of the intersect rectangle
```cpp
/*
Rectangle A: (A.x1, A.y1), (A.x2, A.y2)
Rectangle B: (B.x1, B.y1), (B.x2, B.y2)

Intersect rectangle C : 
(C.x1, C.y1) = ( max(A.x1, B.x1), max(A.y1, B.y1) )
(C.x2, C.y2) = ( min(A.x2, B.x2), min(A.y2, B.y2) )

if C.x1 > c.x2 or C.y1 > C.y2 then C does not exist
*/
```

## Degree/Radians
- PI = acos(-1)
- radian = degree*PI/180
- atan2(y, x): returns the angle by (x, y) and positive x-axis, the value will be in the range [-PI, PI]
```cpp
// Find the angle by (x, y) and positive x-axis
const double PI = acos(-1);
cout << atan2(10,10)*180/PI; // 45
cout << atan2(10,-10)*180/PI; // 135
cout << atan2(-10,-10)*180/PI; // -135
cout << atan2(-10,10)*180/PI; // -45
```

- Rotate the (a, b) vector counter-clockwise by d degree
```cpp
pair<double, double> rotate(int a, int b, int d) {
    double r = sqrt(a*a+b*b);
    double theta = atan2(b, a);
    double theta2 = theta+d*PI/180;
    return {r*cos(theta2), r*sin(theta2)} 
}
```
