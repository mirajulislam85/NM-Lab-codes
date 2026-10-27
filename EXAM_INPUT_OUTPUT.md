# Numerical Methods Lab Mid: exam-style code and actual I/O

**No imports. Bankers rounding uses no round(). Related inputs can be entered on one line.**


## 01_Significant_Count
```python
# Theory: Leading zeros are not significant; decimal trailing zeros are.
s = input("Enter number: ").lower().lstrip("+-")
s = s.split("e")[0]              # Exponent is not counted
if "." in s:
    digits = s.replace(".", "").lstrip("0")
    count = len(digits) if digits else len(s.split(".")[1]) or 1
else:
    count = len(s.lstrip("0").rstrip("0")) or 1
print("Significant digits =", count)
```


### Easy sample input lines:
```text
0.00450
```
Actual output:
```text
Enter number: Significant digits = 3
```


### Medium sample input lines:
```text
1020.0300
```
Actual output:
```text
Enter number: Significant digits = 8
```


### Complex sample input lines:
```text
-1.20500e-3
```
Actual output:
```text
Enter number: Significant digits = 6
```


## 01b_Significant_Round
```python
# Theory: Keep n significant digits; discarded digit >=5 means round up.
s, n = input("Number and significant digits: ").split()
n = int(n)
sign = "-" if s.startswith("-") else ""
s = s.lower().lstrip("+-")
part = s.split("e")
power = int(part[1]) if len(part) == 2 else 0
point = part[0].split(".")
left, right = point[0], point[1] if len(point) == 2 else ""
digits = (left + right).lstrip("0")
if n < 1:
    print("Invalid digits")
elif not digits:
    print("Result = 0")
else:
    power += len(digits) - len(right) - 1
    kept = int(digits[:n].ljust(n, "0"))
    if len(digits) > n and digits[n] >= "5":
        kept += 1
    place = power - n + 1
    if kept >= 10**n:
        kept //= 10
        place += 1
    if place >= 0:
        answer = str(kept * 10**place)
    else:
        t = str(kept).zfill(1-place)
        answer = t[:place] + "." + t[place:]
    print("Result =", sign + answer)
```


### Easy sample input lines:
```text
123.456 4
```
Actual output:
```text
Number and significant digits: Result = 123.5
```


### Medium sample input lines:
```text
0.0045678 3
```
Actual output:
```text
Number and significant digits: Result = 0.00457
```


### Complex sample input lines:
```text
-98765.5 3
```
Actual output:
```text
Number and significant digits: Result = -98800
```


## 02_Bankers_Rounding
```python
# Theory: Exactly halfway -> last retained digit must be even.
# No import and NO round() here.
n, p = map(int, input("Count and decimal places: ").split())
values = input("Enter numbers: ").split()
if len(values) != n or n < 1 or p < 0:
    print("Invalid input")
else:
    print(f"{'Number':<16}Banker's Rounding")
    for raw in values:
        sign = "-" if raw.startswith("-") else ""
        s = raw.lstrip("+-").split(".")
        whole = s[0] or "0"
        frac = s[1] if len(s) > 1 else ""
        frac = frac.ljust(p+1, "0")
        scale = 10**p
        kept = int(whole)*scale + int(frac[:p] or "0")
        drop = frac[p:]
        up = drop[0] > "5" or (drop[0] == "5" and
              (drop[1:].strip("0") != "" or kept % 2 == 1))
        if up:
            kept += 1
        out = str(kept//scale)
        if p:
            out += "." + str(kept%scale).zfill(p)
        print(f"{raw:<16}{sign if kept else ''}{out}")
```


### Easy sample input lines:
```text
4 0
2.5 3.5 -4.5 5.5
```
Actual output:
```text
Count and decimal places: Enter numbers: Number          Banker's Rounding
2.5             2
3.5             4
-4.5            -4
5.5             6
```


### Medium sample input lines:
```text
4 3
2.345600 2.344500 2.345500 7.126500
```
Actual output:
```text
Count and decimal places: Enter numbers: Number          Banker's Rounding
2.345600        2.346
2.344500        2.344
2.345500        2.346
7.126500        7.126
```


### Complex sample input lines:
```text
4 2
-2.345000 99.995000 5.125000 5.135000
```
Actual output:
```text
Count and decimal places: Enter numbers: Number          Banker's Rounding
-2.345000       -2.34
99.995000       100.00
5.125000        5.12
5.135000        5.14
```


## 03_Horner
```python
# Theory: P(new) = P(old)*x + next coefficient.
n = int(input("Enter degree: "))
a = list(map(float, input("Enter coefficients (highest power first): ").split()))
x = float(input("Enter value of x: "))
if len(a) != n+1 or n < 0:
    print("Invalid coefficients")
else:
    p = 0
    print("Power   a(k)   P(k)")
    for i in range(n+1):
        p = p*x + a[i]
        print(f"{n-i:<8}{a[i]:<7g}{p:g}")
    print(f"f({x:g}) = {p:g}")
```


### Easy sample input lines:
```text
2
1 -3 2
2
```
Actual output:
```text
Enter degree: Enter coefficients (highest power first): Enter value of x: Power   a(k)   P(k)
2       1      1
1       -3     -1
0       2      0
f(2) = 0
```


### Medium sample input lines:
```text
4
2 -3 0 5 -7
2
```
Actual output:
```text
Enter degree: Enter coefficients (highest power first): Enter value of x: Power   a(k)   P(k)
4       2      2
3       -3     1
2       0      2
1       5      9
0       -7     11
f(2) = 11
```


### Complex sample input lines:
```text
5
1 -4 -7 0 11 -13
3
```
Actual output:
```text
Enter degree: Enter coefficients (highest power first): Enter value of x: Power   a(k)   P(k)
5       1      1
4       -4     -1
3       -7     -10
2       0      -30
1       11     -79
0       -13    -250
f(3) = -250
```


## 04_Bisection
```python
# Theory: c=(a+b)/2, root lies where f(a)*f(b)<0.
def f(x): return x**3-x-2
a,b,e=map(float,input("Enter a b error: ").split())
if a>=b or e<=0 or f(a)*f(b)>0: print("Invalid interval")
elif f(a)==0 or f(b)==0:
    print(f"Root = {(a if f(a)==0 else b):.6f}")
else:
    print("i     a         b         c         f(c)")
    for i in range(1,101):
        c=(a+b)/2
        print(f"{i:<6}{a:<10.5f}{b:<10.5f}{c:<10.5f}{f(c):.5f}")
        if f(c)==0 or (b-a)/2<e: break
        if f(a)*f(c)<0: b=c
        else: a=c
    print(f"Root = {c:.6f}")
```


### Easy sample input lines:
```text
1 2 0.01
```
Actual output:
```text
Enter a b error: i     a         b         c         f(c)
1     1.00000   2.00000   1.50000   -0.12500
2     1.50000   2.00000   1.75000   1.60938
3     1.50000   1.75000   1.62500   0.66602
4     1.50000   1.62500   1.56250   0.25220
5     1.50000   1.56250   1.53125   0.05911
6     1.50000   1.53125   1.51562   -0.03405
7     1.51562   1.53125   1.52344   0.01225
Root = 1.523438
```


### Medium sample input lines:
```text
1 1.8 0.0001
```
Actual output:
```text
Enter a b error: i     a         b         c         f(c)
1     1.00000   1.80000   1.40000   -0.65600
2     1.40000   1.80000   1.60000   0.49600
3     1.40000   1.60000   1.50000   -0.12500
4     1.50000   1.60000   1.55000   0.17388
5     1.50000   1.55000   1.52500   0.02158
6     1.50000   1.52500   1.51250   -0.05242
7     1.51250   1.52500   1.51875   -0.01560
8     1.51875   1.52500   1.52187   0.00295
9     1.51875   1.52187   1.52031   -0.00634
10    1.52031   1.52187   1.52109   -0.00170
11    1.52109   1.52187   1.52148   0.00062
12    1.52109   1.52148   1.52129   -0.00054
13    1.52129   1.52148   1.52139   0.00004
Root = 1.521387
```


### Complex sample input lines:
```text
-5 3 0.00000001
```
Actual output:
```text
Enter a b error: i     a         b         c         f(c)
1     -5.00000  3.00000   -1.00000  -2.00000
2     -1.00000  3.00000   1.00000   -2.00000
3     1.00000   3.00000   2.00000   4.00000
4     1.00000   2.00000   1.50000   -0.12500
5     1.50000   2.00000   1.75000   1.60938
6     1.50000   1.75000   1.62500   0.66602
7     1.50000   1.62500   1.56250   0.25220
8     1.50000   1.56250   1.53125   0.05911
9     1.50000   1.53125   1.51562   -0.03405
10    1.51562   1.53125   1.52344   0.01225
11    1.51562   1.52344   1.51953   -0.01097
12    1.51953   1.52344   1.52148   0.00062
13    1.51953   1.52148   1.52051   -0.00518
14    1.52051   1.52148   1.52100   -0.00228
15    1.52100   1.52148   1.52124   -0.00083
16    1.52124   1.52148   1.52136   -0.00010
17    1.52136   1.52148   1.52142   0.00026
18    1.52136   1.52142   1.52139   0.00008
19    1.52136   1.52139   1.52138   -0.00001
20    1.52138   1.52139   1.52139   0.00003
21    1.52138   1.52139   1.52138   0.00001
22    1.52138   1.52138   1.52138   -0.00000
23    1.52138   1.52138   1.52138   0.00000
24    1.52138   1.52138   1.52138   0.00000
25    1.52138   1.52138   1.52138   0.00000
26    1.52138   1.52138   1.52138   -0.00000
27    1.52138   1.52138   1.52138   -0.00000
28    1.52138   1.52138   1.52138   -0.00000
29    1.52138   1.52138   1.52138   -0.00000
30    1.52138   1.52138   1.52138   -0.00000
Root = 1.521380
```


## 05_False_Position
```python
# Theory: c=(a*f(b)-b*f(a))/(f(b)-f(a)); f(x)=x^2-x-2.
def f(x): return x*x-x-2
a,b,e=map(float,input("Enter a b error: ").split())
if a>=b or e<=0 or f(a)*f(b)>0: print("Invalid interval")
elif f(a)==0 or f(b)==0:
    print(f"Root = {(a if f(a)==0 else b):.6f}")
else:
    print("i     a         b         c         f(c)")
    for i in range(1,101):
        fa,fb=f(a),f(b)
        c=(a*fb-b*fa)/(fb-fa)
        print(f"{i:<6}{a:<10.5f}{b:<10.5f}{c:<10.5f}{f(c):.5f}")
        if abs(f(c))<e: break
        if fa*f(c)<0: b=c
        else: a=c
    else: print("No convergence"); c=None
    if c is not None: print(f"Root = {c:.6f}")
```


### Easy sample input lines:
```text
1 3 0.01
```
Actual output:
```text
Enter a b error: i     a         b         c         f(c)
1     1.00000   3.00000   1.66667   -0.88889
2     1.66667   3.00000   1.90909   -0.26446
3     1.90909   3.00000   1.97674   -0.06923
4     1.97674   3.00000   1.99415   -0.01751
5     1.99415   3.00000   1.99854   -0.00439
Root = 1.998536
```


### Medium sample input lines:
```text
0 3 0.0001
```
Actual output:
```text
Enter a b error: i     a         b         c         f(c)
1     0.00000   3.00000   1.00000   -2.00000
2     1.00000   3.00000   1.66667   -0.88889
3     1.66667   3.00000   1.90909   -0.26446
4     1.90909   3.00000   1.97674   -0.06923
5     1.97674   3.00000   1.99415   -0.01751
6     1.99415   3.00000   1.99854   -0.00439
7     1.99854   3.00000   1.99963   -0.00110
8     1.99963   3.00000   1.99991   -0.00027
9     1.99991   3.00000   1.99998   -0.00007
Root = 1.999977
```


### Complex sample input lines:
```text
-1.5 0 0.00000001
```
Actual output:
```text
Enter a b error: i     a         b         c         f(c)
1     -1.50000  0.00000   -0.80000  -0.56000
2     -1.50000  -0.80000  -0.96970  -0.08999
3     -1.50000  -0.96970  -0.99563  -0.01308
4     -1.50000  -0.99563  -0.99938  -0.00187
5     -1.50000  -0.99938  -0.99991  -0.00027
6     -1.50000  -0.99991  -0.99999  -0.00004
7     -1.50000  -0.99999  -1.00000  -0.00001
8     -1.50000  -1.00000  -1.00000  -0.00000
9     -1.50000  -1.00000  -1.00000  -0.00000
10    -1.50000  -1.00000  -1.00000  -0.00000
11    -1.50000  -1.00000  -1.00000  -0.00000
Root = -1.000000
```


## 06_Fixed_Point
```python
# Theory: x(new)=(x+2)^(1/3); stopping |x(new)-x|<error.
def g(x): return (x+2)**(1/3)
x,e=map(float,input("Enter initial guess and error: ").split())
if x < -2 or e<=0: print("Invalid input")
else:
    print("i     x(old)      x(new)")
    for i in range(1,101):
        x1=g(x)
        print(f"{i:<6}{x:<12.6f}{x1:.6f}")
        if abs(x1-x)<e: break
        x=x1
    else: print("No convergence"); x1=None
    if x1 is not None: print(f"Root = {x1:.6f}")
```


### Easy sample input lines:
```text
1 0.01
```
Actual output:
```text
Enter initial guess and error: i     x(old)      x(new)
1     1.000000    1.442250
2     1.442250    1.509897
3     1.509897    1.519724
Root = 1.519724
```


### Medium sample input lines:
```text
1.5 0.0001
```
Actual output:
```text
Enter initial guess and error: i     x(old)      x(new)
1     1.500000    1.518294
2     1.518294    1.520935
3     1.520935    1.521316
4     1.521316    1.521370
Root = 1.521370
```


### Complex sample input lines:
```text
-1 0.00000001
```
Actual output:
```text
Enter initial guess and error: i     x(old)      x(new)
1     -1.000000   1.000000
2     1.000000    1.442250
3     1.442250    1.509897
4     1.509897    1.519724
5     1.519724    1.521141
6     1.521141    1.521345
7     1.521345    1.521375
8     1.521375    1.521379
9     1.521379    1.521380
10    1.521380    1.521380
11    1.521380    1.521380
12    1.521380    1.521380
Root = 1.521380
```


## 07_Newton_Raphson
```python
# Theory: x(new)=x-f(x)/f'(x), f(x)=x^2-3x+2.
def f(x): return x*x-3*x+2
def df(x): return 2*x-3
x,e=map(float,input("Enter initial guess and error: ").split())
if e<=0: print("Invalid error")
else:
    print("i     x(old)      f(x)        x(new)")
    for i in range(1,101):
        if df(x)==0:
            print("Derivative is zero"); break
        x1=x-f(x)/df(x)
        print(f"{i:<6}{x:<12.6f}{f(x):<12.6f}{x1:.6f}")
        if abs(x1-x)<e:
            print(f"Root = {x1:.6f}"); break
        x=x1
    else: print("No convergence")
```


### Easy sample input lines:
```text
0 0.01
```
Actual output:
```text
Enter initial guess and error: i     x(old)      f(x)        x(new)
1     0.000000    2.000000    0.666667
2     0.666667    0.444444    0.933333
3     0.933333    0.071111    0.996078
4     0.996078    0.003937    0.999985
Root = 0.999985
```


### Medium sample input lines:
```text
3 0.0001
```
Actual output:
```text
Enter initial guess and error: i     x(old)      f(x)        x(new)
1     3.000000    2.000000    2.333333
2     2.333333    0.444444    2.066667
3     2.066667    0.071111    2.003922
4     2.003922    0.003937    2.000015
5     2.000015    0.000015    2.000000
Root = 2.000000
```


### Complex sample input lines:
```text
-10 0.0000000001
```
Actual output:
```text
Enter initial guess and error: i     x(old)      f(x)        x(new)
1     -10.000000  132.000000  -4.260870
2     -4.260870   32.937618   -1.402133
3     -1.402133   8.172375    0.005862
4     0.005862    1.982449    0.669271
5     0.669271    0.440111    0.934165
6     0.934165    0.070169    0.996170
7     0.996170    0.003845    0.999985
8     0.999985    0.000015    1.000000
9     1.000000    0.000000    1.000000
10    1.000000    0.000000    1.000000
Root = 1.000000
```


## 08_Secant
```python
# Theory: x2=x1-f(x1)*(x1-x0)/(f(x1)-f(x0)).
def f(x): return x**3-x-2
x0,x1,e=map(float,input("Enter x0 x1 error: ").split())
if e<=0: print("Invalid error")
else:
    print("i     x0          x1          x2")
    for i in range(1,101):
        f0,f1=f(x0),f(x1)
        if f1==f0:
            print("Division by zero"); break
        x2=x1-f1*(x1-x0)/(f1-f0)
        print(f"{i:<6}{x0:<12.6f}{x1:<12.6f}{x2:.6f}")
        if abs(x2-x1)<e:
            print(f"Root = {x2:.6f}"); break
        x0,x1=x1,x2
    else: print("No convergence")
```


### Easy sample input lines:
```text
1 2 0.01
```
Actual output:
```text
Enter x0 x1 error: i     x0          x1          x2
1     1.000000    2.000000    1.333333
2     2.000000    1.333333    1.462687
3     1.333333    1.462687    1.531169
4     1.462687    1.531169    1.520926
5     1.531169    1.520926    1.521376
Root = 1.521376
```


### Medium sample input lines:
```text
1.3 1.7 0.0001
```
Actual output:
```text
Enter x0 x1 error: i     x0          x1          x2
1     1.300000    1.700000    1.490501
2     1.700000    1.490501    1.517469
3     1.490501    1.517469    1.521474
4     1.517469    1.521474    1.521379
Root = 1.521379
```


### Complex sample input lines:
```text
1 3 0.0000000001
```
Actual output:
```text
Enter x0 x1 error: i     x0          x1          x2
1     1.000000    3.000000    1.166667
2     3.000000    1.166667    1.289417
3     1.166667    1.289417    1.614142
4     1.289417    1.614142    1.503581
5     1.614142    1.503581    1.520163
6     1.503581    1.520163    1.521397
7     1.520163    1.521397    1.521380
8     1.521397    1.521380    1.521380
9     1.521380    1.521380    1.521380
Root = 1.521380
```


## 08b_Secant_Exam_Table
```python
# Theory: Secant x2=x1-f(x1)*(x1-x0)/(f(x1)-f(x0)).
# Input polynomial evaluated with Horner; show each iteration.
n = int(input("Enter degree: "))
a = list(map(float, input("Enter coefficients: ").split()))
x0, x1 = map(float, input("Enter x0 x1: ").split())
p = int(input("Decimal places: "))
def f(x):
    y = 0
    for c in a: y = y*x+c
    return y
if len(a) != n+1 or p < 0:
    print("Invalid input")
elif f(x0) == 0 or f(x1) == 0:
    print(f"Root = {(x0 if f(x0)==0 else x1):.{p}f}")
elif f(x0)*f(x1) > 0:
    print("Invalid initial values")
else:
    print(f"{'i':<3}{'x(i)':<12}{'x(i-1)':<12}{'f(x(i))':<12}{'f(x(i-1))':<12}x(i+1)")
    for i in range(1, 101):
        y0, y1 = f(x0), f(x1)
        if y1 == y0:
            print("Division by zero")
            break
        x2 = x1-y1*(x1-x0)/(y1-y0)
        print(f"{i:<3}{x1:<12.5f}{x0:<12.5f}{y1:<12.5f}{y0:<12.5f}{x2:.5f}")
        if round(x2,p) == round(x1,p):
            print(f"Root = {x2:.{p}f}")
            break
        x0, x1 = x1, x2
    else:
        print("No convergence")
```


### Easy sample input lines:
```text
2
1 0 -2
1 2
2
```
Actual output:
```text
Enter degree: Enter coefficients: Enter x0 x1: Decimal places: i  x(i)        x(i-1)      f(x(i))     f(x(i-1))   x(i+1)
1  2.00000     1.00000     2.00000     -1.00000    1.33333
2  1.33333     2.00000     -0.22222    2.00000     1.40000
3  1.40000     1.33333     -0.04000    -0.22222    1.41463
4  1.41463     1.40000     0.00119     -0.04000    1.41421
Root = 1.41
```


### Medium sample input lines:
```text
3
2 0 -2 -5
1 2
2
```
Actual output:
```text
Enter degree: Enter coefficients: Enter x0 x1: Decimal places: i  x(i)        x(i-1)      f(x(i))     f(x(i-1))   x(i+1)
1  2.00000     1.00000     7.00000     -5.00000    1.41667
2  1.41667     2.00000     -2.14699    7.00000     1.55359
3  1.55359     1.41667     -0.60759    -2.14699    1.60763
4  1.60763     1.55359     0.09449     -0.60759    1.60036
5  1.60036     1.60763     -0.00324    0.09449     1.60060
Root = 1.60
```


### Complex sample input lines:
```text
2
1 0 -2
1 2
4
```
Actual output:
```text
Enter degree: Enter coefficients: Enter x0 x1: Decimal places: i  x(i)        x(i-1)      f(x(i))     f(x(i-1))   x(i+1)
1  2.00000     1.00000     2.00000     -1.00000    1.33333
2  1.33333     2.00000     -0.22222    2.00000     1.40000
3  1.40000     1.33333     -0.04000    -0.22222    1.41463
4  1.41463     1.40000     0.00119     -0.04000    1.41421
5  1.41421     1.41463     -0.00001    0.00119     1.41421
Root = 1.4142
```


## 09_Newton_Forward_Backward
```python
# Theory: Equal spacing. Forward u=(t-x0)/h; backward u=(t-x_last)/h.
n=int(input("Number of points: "))
x=list(map(float,input("Enter x values: ").split()))
y=list(map(float,input("Enter y values: ").split()))
t=float(input("Find f(x) at x: "))
if n<2 or len(x)!=n or len(y)!=n: print("Invalid points")
else:
    h=x[1]-x[0]
    valid=h!=0
    for i in range(2,n):
        if abs(x[i]-x[i-1]-h)>1e-9: valid=False
    if not valid: print("x values must be equally spaced")
    else:
        d=[[0]*n for i in range(n)]
        for i in range(n): d[i][0]=y[i]
        for j in range(1,n):
            for i in range(j,n): d[i][j]=d[i][j-1]-d[i-1][j-1]
        print("x       y       D1      D2      D3      D4")
        for i in range(n):
            print(f"{x[i]:<8g}{y[i]:<8g}",end="")
            for j in range(1,i+1): print(f"{d[i][j]:<8g}",end="")
            print()
        u=(t-x[0])/h; forward=y[0]; term=1
        for j in range(1,n):
            term=term*(u-j+1)/j
            forward+=term*d[j][j]
        u=(t-x[-1])/h; backward=y[-1]; term=1
        for j in range(1,n):
            term=term*(u+j-1)/j
            backward+=term*d[-1][j]
        print(f"Forward = {forward:.4f}")
        print(f"Backward = {backward:.4f}")
```


### Easy sample input lines:
```text
3
0 1 2
1 2 5
0.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x       y       D1      D2      D3      D4
0       1       
1       2       1       
2       5       3       2       
Forward = 1.2500
Backward = 1.2500
```


### Medium sample input lines:
```text
4
0 1 2 3
1 2 5 10
1.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x       y       D1      D2      D3      D4
0       1       
1       2       1       
2       5       3       2       
3       10      5       2       0       
Forward = 3.2500
Backward = 3.2500
```


### Complex sample input lines:
```text
5
0 1 2 3 4
1 3 11 31 69
3.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x       y       D1      D2      D3      D4
0       1       
1       3       2       
2       11      8       6       
3       31      20      12      6       
4       69      38      18      6       0       
Forward = 47.3750
Backward = 47.3750
```


## 09b_Newton_Backward_Exam_Table
```python
# Theory: backward D(y_i)=y_i-y_(i-1), u=(t-x_last)/h.
n = int(input("Number of points: "))
x = list(map(float, input("Enter x values: ").split()))
y = list(map(float, input("Enter y values: ").split()))
t = float(input("Find f(x) at x: "))
if n < 2 or len(x) != n or len(y) != n:
    print("Invalid points")
else:
    h = x[1]-x[0]
    valid = h != 0
    for i in range(2,n):
        if abs(x[i]-x[i-1]-h) > 1e-9: valid = False
    if not valid:
        print("x values must be equally spaced")
    else:
        d = [[0]*n for i in range(n)]
        for i in range(n): d[i][0] = y[i]
        for j in range(1,n):
            for i in range(j,n):
                d[i][j] = d[i][j-1]-d[i-1][j-1]
        print("x      y      1st     2nd     3rd     4th")
        for i in range(n):
            print(f"{x[i]:<7g}{y[i]:<7g}",end="")
            for j in range(1,i+1): print(f"{d[i][j]:<8g}",end="")
            print()
        u = (t-x[-1])/h
        ans, term = y[-1], 1
        for j in range(1,n):
            term = term*(u+j-1)/j
            ans += term*d[-1][j]
        print(f"f({t:g}) = {ans:.4f}")
```


### Easy sample input lines:
```text
3
0 1 2
1 2 5
0.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x      y      1st     2nd     3rd     4th
0      1      
1      2      1       
2      5      3       2       
f(0.5) = 1.2500
```


### Medium sample input lines:
```text
5
0 1 2 3 4
1 3 11 31 69
3.2
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x      y      1st     2nd     3rd     4th
0      1      
1      3      2       
2      11     8       6       
3      31     20      12      6       
4      69     38      18      6       0       
f(3.2) = 36.9680
```


### Complex sample input lines:
```text
5
0 1 2 3 4
1 3 11 31 69
3.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x      y      1st     2nd     3rd     4th
0      1      
1      3      2       
2      11     8       6       
3      31     20      12      6       
4      69     38      18      6       0       
f(3.5) = 47.3750
```


## 10_Newton_Divided_Difference
```python
# Theory: Divided difference=(next-previous)/(right x - left x).
n=int(input("Number of points: "))
x=list(map(float,input("Enter x values: ").split()))
y=list(map(float,input("Enter y values: ").split()))
t=float(input("Find f(x) at x: "))
if n<2 or len(x)!=n or len(y)!=n or len(set(x))!=n:
    print("Invalid points")
else:
    d=[[0]*n for i in range(n)]
    for i in range(n): d[i][0]=y[i]
    for j in range(1,n):
        for i in range(n-j):
            d[i][j]=(d[i+1][j-1]-d[i][j-1])/(x[i+j]-x[i])
    print("x       y       DD1     DD2     DD3     DD4")
    for i in range(n):
        print(f"{x[i]:<8g}{y[i]:<8g}",end="")
        for j in range(1,n-i): print(f"{d[i][j]:<8g}",end="")
        print()
    ans=d[0][n-1]
    for j in range(n-2,-1,-1): ans=ans*(t-x[j])+d[0][j]
    print(f"Result = {ans:.4f}")
```


### Easy sample input lines:
```text
3
0 1 2
1 2 5
0.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x       y       DD1     DD2     DD3     DD4
0       1       1       1       
1       2       3       
2       5       
Result = 1.2500
```


### Medium sample input lines:
```text
4
0 2 3 5
1 5 10 26
4
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x       y       DD1     DD2     DD3     DD4
0       1       2       1       0       
2       5       5       1       
3       10      8       
5       26      
Result = 17.0000
```


### Complex sample input lines:
```text
5
-2 0 1 3 5
-1 3 2 24 118
2.5
```
Actual output:
```text
Number of points: Enter x values: Enter y values: Find f(x) at x: x       y       DD1     DD2     DD3     DD4
-2      -1      2       -1      1       0       
0       3       -1      4       1       
1       2       11      9       
3       24      47      
5       118     
Result = 13.6250
```
