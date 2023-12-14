# Linear Regression<br>

**개요**<br>
* Linear Least Square Regression<br>
  * [linearFit]()<br> 
* Curve fitting with a high order polynomial<br>
  * [polyFit]()<br>
* Curve fitting with non-linear functions<br>
  * [expFit]()<br>

## linearFit( )<br>

```c
Matrix linearFit(Matrix _x, Matrix _y);
```
>Parameter<br>
**_x** : x data set  <br>
**_y** : y data set <br>

<br>
<hr>

1. 주어진 data set으로부터 linear equation을 예측하는 함수
2. myMatrix.h 안에 선언되어 있다.<br>
3. 다음과 같은 원리를 기반으로 작성되었다.<br>

* Curve-fit line:
  $$f(x)=a_i(x)+a_0$$
* Residual:

$$ r_k=y_k-f(x_k)=y_k-(a_1x_k+a_0)$$

* Total error:
  $$E=\sum_{i=1}^N{r_i}^2=\sum_{i=1}^N(y_i-f(x_i))^2=\sum_{i=1}^N(y_i-(a_1x_i+a_0))^2$$
  <br>
* **Minimize** 
  * The total residual error is at its minimum with parameters a1, a0 for the condition of
$${\delta E\over \delta a_1}=-2\sum_{i=1}^nx_i(y_i-(a_1x_i+a_0))=0$$
$${\delta E\over \delta a_0}=-2\sum_{i=1}^nx_i(y_i-(a_1x_i+a_0))=0$$
<br>


$$(\sum_{i=1}^nx_i^2)a_1+(\sum_{i=1}^nx_i)a_0=\sum_{i=1}^nx_iy_i$$
$$(\sum_{i=1}^nx_i)a_1+(\sum_{i=1}^n1)a_0=\sum_{i=1}^ny_i$$
<br>

$$a_1={nS_{xy}-S_xS_y \over nS_{xx}-S_x^2}$$
$$a_0={S_{xx}S_y-S_{xy}S_x \over nS_{xx}-S_x^2}$$

* **Normal equation**<br>
  * Total error can be expressed as

$$r=y-Az$$

$$
\begin{equation}
   \begin{bmatrix}
   r_1\\
   \vdots\\
   r_n\\
   \end{bmatrix}=
   \begin{bmatrix}
   y_1\\
   \vdots\\
   y_n\\
   \end{bmatrix}-
   \begin{bmatrix}
   x_1 & 1\\
   \vdots & \vdots\\
   x_n & 1\\
   \end{bmatrix}
   \begin{bmatrix}
   a_1\\
   a_0\\
   \end{bmatrix}
\end{equation}$$


<br>

  $$E=\sum_{i=1}^nr_i^2=r^Tr=(y-Az)^T(y-AZ)$$
$${\delta E\over \delta z}=0={\delta (y-Az)^T(y-AZ)\over \delta z}$$
<br>


$$\hat{z}=(A^TA)^{-1}A^Ty$$
$$\hat{y}=A\hat{z}=A(A^TA)^{-1}A^Ty$$

## Example <br>
```c++
int main()
{
    double T_array[] = { 30, 40, 50, 60, 70, 80 };
    double P_array[] = { 1.05, 1.07, 1.09, 1.14, 1.17, 1.21 };

    // calculat # of data point
    int M = sizeof(T_array) / sizeof(T_array[0]);  

    Matrix T = arr2Mat(T_array, M, 1);
    Matrix P = arr2Mat(P_array, M, 1);

    Matrix z = linearFit(T, P);



    printMat(T, "T");
    printMat(P, "P");
    printMat(z, "z");

    double predicted_T = z.at[1][0] * 100 + z.at[0][0];
    printf("Predicted value at T=100C : %f\n", predicted_T);
}
```

## Output <br>
```c
T =
      30.000000
      40.000000
      50.000000
      60.000000
      70.000000
      80.000000

P =
       1.050000
       1.070000
       1.090000
       1.140000
       1.170000
       1.210000

z =
       0.940952
       0.003286

```
## Warning
>_x.rows = _y.rows<br>
-x.cols=-y.cols


## Error Handling
```c
if (_x.rows != _y.rows )
{
	printf("Error: The sizes of matrix _x and _y are not same\n");

	return zeros(_x.rows, _x.cols);
}
```
<br>

## polyFit( )<br>

```c
Matrix	polyFit(Matrix _X, Matrix _Y, int n);
```
>Parameter<br>
**_x** : x data set  <br>
**_y** : y data set <br>
**n**  : n_th order polynomial

<br>
<hr>

1. 앞서 설명한 least square regression method를 고차 다항식에 적용하는 함수
2. myMatrix.h 안에 선언되어 있다.<br>
3. 다음과 같은 원리를 기반으로 작성되었다.<br>

$$f(x_k)=a_nx_k^n+\cdots +a_2x_k^2+a_1x_k^1+a0\\k=1\sim m$$
* n차 다항식의 행렬 A와 벡터 z는 다음과 같이 표현된다.

$$A=
\begin{equation}
   \begin{bmatrix}
   x_1^n & \cdots & x_1^2 & x_1 & 1\\
   \vdots & \ddots & \vdots & \vdots & \vdots\\
   x_m^n & \cdots & x_m^2 & x_m & 1
   \end{bmatrix}, z=
   \begin{bmatrix}
   a_n\\
   \vdots\\
   a_0
   \end{bmatrix}, y=
   \begin{bmatrix}
   y_1\\
   \vdots\\
   y_m
   \end{bmatrix}
\end{equation}
$$

* Vector

$$A^Ty=
\begin{equation}
   \begin{bmatrix}
   \sum_{k=1}^my_kx_k^n\\
   \sum_{k=1}^my_kx_k^{n-1}\\
   \vdots\\
   \sum_{k=1}^my_kx_k^1\\
   \sum_{k=1}^my_kx
   \end{bmatrix}
\end{equation}
$$

* Matrix

$$A^TA=
\begin{equation}
   \begin{bmatrix}
   x_1^n & x_2^n & \cdots & x_{m-1}^n & x_m^n\\
   \vdots & \vdots & \ddots & \vdots & \vdots\\
   x_1 & x_2 & \cdots & x_{m-1} & x_m \\
   1 & 1 & \cdots & 1 & 1
   \end{bmatrix}
   \begin{bmatrix}
   x_1^n & \cdots & x_1^2 & x_1 & 1\\
   \vdots & \ddots & \vdots & \vdots & \vdots\\
   x_m^n & \cdots & x_m^2 & x_m & 1
   \end{bmatrix}=
   \begin{bmatrix}
   \sum_{k=1}^mx_k^{2n} & \sum_{k=1}^mx_k^{2n-1} & \cdots & \sum_{k=1}^mx_k^{n+1} & \sum_{k=1}^mx_k^n\\
   \sum_{k=1}^mx_k^{2n-1} & \sum_{k=1}^mx_k^{2n-2} & \cdots & \sum_{k=1}^mx_k^{n} & \sum_{k=1}^mx_k^{n-1}\\
   \vdots & \vdots & \ddots & \vdots & \vdots\\
   \sum_{k=1}^mx_k^{n+1} & \sum_{k=1}^mx_k^{n} & \cdots & \sum_{k=1}^mx_k^{2} & \sum_{k=1}^mx_k^{1}\\
   \sum_{k=1}^mx_k^{n} & \sum_{k=1}^mx_k^{n-1} & \cdots & \sum_{k=1}^mx_k^{n} & m\\
   \end{bmatrix}
\end{equation}
$$

<br>

* Result
  

$$\hat{z}=(A^TA)^{-1}A^Ty$$
$$\hat{y}=A\hat{z}$$


## Example <br>
```c++
int main()
{
    double T_array2[] = { 0,0.4,0.8,1.2,1.6,2.0,2.4,2.8,3.2,3.6,4.0,4.4,4.8,5.2,5.6,6.0 };
    double P_array2[] = { 0, 3, 4.5, 5.8, 5.9, 5.8, 6.2, 7.4, 9.6, 15.6, 20.7, 26.7, 31.1, 35.6, 39.3, 41.5 };

    int n = sizeof(T_array2) / sizeof(T_array2[0]);  // calculat # of data point

    T = arr2Mat(T_array2, n, 1);
    P = arr2Mat(P_array2, n, 1);


    printf("3rd polynomial\n");
    M = 3;     // n_th order polynomial
    z = polyFit(T, P, M);

    printMat(z, "z");


    printf("4th polynomial\n");
    M = 4;     // n_th order polynomial
    z = polyFit(T, P, M);

    printMat(z, "z");
}
```

## Output <br>
```c
3rd polynomial
z =
       2.892982
      -1.988223
       1.803028
      -0.054117

4th polynomial
z =
      -0.274607
      12.877980
     -10.192668
       3.118549
      -0.264389
```
## Warning
>_X.rows = _Y.rows<br>
_X.cols=_Y.cols


## Error Handling
```c
if (_X.rows != _Y.rows )
{
	printf("Error: The sizes of matrix _X and _Y are not same\n");

	return zeros(_X.rows, _X.cols);
}
```
<br>

## expFit( )<br>

```c
Matrix	expFit(Matrix _X, Matrix _Y)
```
>Parameter<br>
**_x** : x data set  <br>
**_y** : y data set <br>


<hr>

1. 주어진 data set으로부터 linear equation을 예측하는 함수
2. myMatrix.h 안에 선언되어 있다.<br>
3. 다음과 같은 원리를 기반으로 작성되었다.<br>

* Nonlinear equation

$$y=be^{mx}$$

* Linear Form

$$ln(y)=mx+ln(b)$$

* Relationship

$$Y=ln(y), X=x$$

$$a_1=m, a_0=ln(b)$$

## Example <br>
```c++
int main()
{
   	double T_array3[] = { 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30 };
	double P_array3[] = { 9.7, 8.1, 6.6, 5.1, 4.4, 3.7, 2.8, 2.4, 2.0, 1.6, 1.4, 1.1, 0.85, 0.69, 0.6, };
	M = sizeof(T_array3)/sizeof(T_array3[0]);  // calculat # of data point

	T = arr2Mat(T_array3, M, 1);
	P = arr2Mat(P_array3, M, 1);

	z = expFit(T, P);

	printMat(z, "z");

  return 0;
}
```

## Output <br>
```c
z =
      -0.100161
      11.913118 
```
## Warning
>_X.rows = _Y.rows<br>
_Y.cols=_Y.cols


## Error Handling
```c
if (_X.rows != _Y.rows )
{
	printf("Error: The sizes of matrix _X and _Y are not same\n");

	return zeros(_X.rows, _Y.cols);
}
```
<br>
