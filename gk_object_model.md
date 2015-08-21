# 線形代数

幾何学ではその内部で線形代数を用いられている。

# 曲線（Curve）

曲線を扱う際に機能として提供すべきもの

0. 曲線上の位置
0. 導関数
0. 接線
0. 曲率 
0. アフィン変換
0. 分割（subdivision）
0. 結合
0. 単純化 （simplifization）

| 機能 | 実装場所 | 必要性 |
| :-: | :-: | :-: |
| 曲線上の位置 | メンバ関数 | ◎ |
| 導関数 | 一般関数  | ◎ |
| 接線 | 一般関数 | ◎ |
| 曲率 | 一般関数 | ◎ |
| アフィン変換 | 一般関数 | ○ |
| 分割 | 一般関数 | △ |
| 結合 | 一般関数 | △ |
| 単純化 | 一般関数 | △ |

曲線を数式で表現すると以下のようになる。

\begin{eqnarray}
\mathbf{r} = \mathbf{r}(t)
\label{eq:curve_function}
\end{eqnarray}

一般的に3次元空間として表現すると、式\eqref{eq:curve_function}の$\mathbf{r}(t)$は

\begin{eqnarray*}
\mathbf{r}(t) = [ \begin{array}{ccc} x(t) & y(t) & z(t) \end{array} ]
\end{eqnarray*}

となる。

```cpp
template<typename Parameter, typename Dependence>
struct Curve {
	typedef Parameter parameter;
	typedef Dependence dependence;
	
	dependence operator()(const parameter& t) const;
};

template<typename Curve>
struct curve_traits {
	typedef typename Curve::parameter parameter;
	typedef typename Curve::dependence dependence;
};
```



### メンバメソッドによる機能提供

#### パラメータに対する位置

```cpp
struct curve {
	dependence operator()(const parameter& t) const;
};
```

### 解析関数

#### 近接点

任意の位置$\mathbf{p}$に対して曲線$\mathbf{r}(t)$への距離$d$は

\begin{eqnarray}
d(t) = | \mathbf{p} - \mathbf{r}(t) |
\end{eqnarray}

となる。距離$d$は$t$の関数となるため、$d(t)$としている。

\begin{eqnarray}
a & = & \mbox{argmin} \: d(t)
\end{eqnarray}

```cpp
template<typename Curve>
typename curve_traits<Curve>::parameter parameter_on(const Curve& curve, const typename curve_traits<Curve>::dependence& p);
```

#### 導関数

\begin{eqnarray}
\frac{d}{dt}\mathbf{r}(t) = \dot{\mathbf{r}}(t)
\end{eqnarray}

**導関数型のメンバ関数として提供**
```cpp
template<typename Curve>
struct derivative {
	typedef Curve source;
	typedef curve_derivative value_type;

	static value_type operator()(const source& r); // operatorのstatic属性設定はできないので、コンパイルエラー
};
```

**一般関数として提供**
```cpp
template<typename Curve>
struct derivative {
	typedef Curve source;
	typedef curve_derivative value_type;
};

template<typename Curve>
typename derivative<Curve>::value_type derivative_of(const Curve& curve);
```

\begin{eqnarray*}
\frac{d}{dt}\mathbf{r}(t) = \lim_{\Delta t \rightarrow 0} \frac{\mathbf{r}(t + \Delta t) - \mathbf{r}(t)}{\Delta t}
\end{eqnarray*}

#### 接線

\begin{eqnarray}
t & = & a \nonumber \\
\frac{d}{dt}\mathbf{r}(a) & = & \dot{\mathbf{r}}(a)
\end{eqnarray}

```cpp
template<typename Curve>
typename derivative<typename derivative<Curve>::value_type>::value_type tangent(const Curve& curve, const curve_traits<Curve>::parameter& a) {
	return derivative<Curve>::value_type(a);
}
```

#### 曲率

\begin{eqnarray}
\kappa(t) = \frac{ \sqrt{|\dot{\mathbf{r}}|^2 |\ddot{\mathbf{r}}|^2 - (\dot{\mathbf{r}} \cdot \ddot{\mathbf{r}})^2 } } { |\dot{\mathbf{r}}|^3 }
\end{eqnarray}

#### 分割

```cpp
template<typename Curve>
struct subdivision {
	typedef Curve source;
	typedef XXX value_type;
};

template<typename Curve>
typename subdivision<Curve>::value_type subdivide(const Curve& curve, const typename curve_traits<Curve>::parameter& t);
```

## 直線（Line）

\begin{eqnarray}
\mathbf{r}(a) & = & \mathbf{p}  \\
\mathbf{r}(b) & = & \mathbf{q} \\ 
\mathbf{r}(t) & = & \mathbf{p}+ (t - a) \times \frac{\mathbf{q} - \mathbf{p}}{b-a} \\
\end{eqnarray}

- 直線
- 半直線
- 線分

\begin{eqnarray}
\mathbf{d} = \frac{\mathbf{v}}{|\mathbf{v}|} 
\end{eqnarray}

```cpp
template<typename Vector>
direction d = normalize(const Vector& v);
```

```cpp
template<typename Scalar>
struct line{
	typedef XXX vector;
	vector operator()(const Scalar& t) const;
}

template<typename AL>
struct algebra_linear_traits{
	typedef AL::vector vector;
	typedef AL::direction direction;
	typedef AL::matrix matrix;

	static size_t dimension(const vector&);
	static std::pair<size_t, size_t> dimension(const matrix&);
};

template<typename AL, typename Parameter> // Algebra Linear
struct line{
	typedef algebra_linear_traits<AL> traits;
	typedef traits::vector vector;

	line();
	line(const line& other);
	line(const vector& first, const vector& second);
	vector operator()(const Parameter& t) const;
};

template<typename Scalar>
struct ray{
};

template<typename Scalar>
struct segment{
};

template<typename Line>
struct line_straits{
	typedef Line::vector vector;
};
```

```cpp
template<typename Line>
direction direct(const Line& l);
```

# 曲面（Surface）

\begin{eqnarray}
\mathbf{r} = \mathbf{r}(u, v)
\end{eqnarray}

## 平面（Plane）

\begin{eqnarray}
\end{eqnarray}

> Written with [StackEdit](https://stackedit.io/).