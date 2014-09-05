#計算幾何
計算幾何は、Boost Geometry Libraryによって提供される。

Contents
<ol class='goog-toc'><li class='goog-toc'>[<strong>1 </strong>2つの図形が互いに素かを判定](#TOC-2-)</li><li class='goog-toc'>[<strong>2 </strong>2つの図形が交点を持っているかを判定](#TOC-2-1)</li><li class='goog-toc'>[<strong>3 </strong>図形がもう一方の図形の完全な内側にあるかを判定](#TOC--)</li><li class='goog-toc'>[<strong>4 </strong>2つの図形が空間的に等しいかを判定](#TOC-2-2)</li><li class='goog-toc'>[<strong>5 </strong>面積を計算する](#TOC--1)</li><li class='goog-toc'>[<strong>6 </strong>図形の中心座標を計算する](#TOC--2)</li><li class='goog-toc'>[<strong>7 </strong>図形の凸包を計算する](#TOC--3)</li><li class='goog-toc'>[<strong>8 </strong>2つの図形の距離を計算する](#TOC-2-3)</li><li class='goog-toc'>[<strong>9 </strong>2つの図形の差を計算する](#TOC-2-4)</li><li class='goog-toc'>[<strong>10 </strong>2つの図形の和を計算する](#TOC-2-5)</li><li class='goog-toc'>[<strong>11 </strong>2つの図形の共通部分を計算する](#TOC-2-6)</li><li class='goog-toc'>[<strong>12 </strong>図形の包絡線を計算する](#TOC--4)</li><li class='goog-toc'>[<strong>13 </strong>図形の長さを計算する](#TOC--5)</li><li class='goog-toc'>[<strong>14 </strong>図形を逆向きにする](#TOC--6)</li><li class='goog-toc'>[<strong>15 </strong>図形を単純化する](#TOC--7)</li><li class='goog-toc'>[<strong>16 </strong>図形から重複した点を削除する](#TOC--8)</li><li class='goog-toc'>[<strong>17 </strong>図形を平行移動する](#TOC--9)</li><li class='goog-toc'>[<strong>18 </strong>図形を拡大縮小する](#TOC--10)</li><li class='goog-toc'>[<strong>19 </strong>図形を回転する](#TOC--11)</li></ol>




###2つの図形が互いに素かを判定
2つの図形が互いに素かを判定するには、boost::geometry::disjoint()アルゴリズムを使用する。
disjoint()は、2つの図形が重なりあっていなければtrue、重なり合っていたらfalseを返す。

<b>box同士が重なりあっていないかを判定：</b>

```cpp
#include <boost/assert.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/geometry/algorithms/disjoint.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::box<point> box;

int main()
{
    // A. disjoint
    // a
    // +------+
    // |      |
    // |      |
    // +------+  b
    //           +------+
    //           |      |
    //           |      |
    //           +------+
    {
        const box a(point(0, 0), point(3, 3));
        const box b(point(4, 4), point(7, 7));

        const bool result = bg::disjoint(a, b);
        BOOST_ASSERT(result);
    }

    // B. not disjoint
    // a
    // +------+
    // |   b  |
    // |   +--+---+
    // +---+--+   |
    //     |      |
    //     +------+
    {
        const box a(point(0, 0), point(3, 3));
        const box b(point(2, 2), point(5, 5));

        const bool result = bg::disjoint(a, b);
        BOOST_ASSERT(!result);
    }
}
```
* disjoint[color ff0000]
* disjoint[color ff0000]

<b>boxとpoint_xyが重なりあっていないかを判定：</b>

```cpp
#include <boost/assert.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/geometry/algorithms/disjoint.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::box<point> box;

int main()
{
    // disjoint
    // a
    // +------+
    // |      |
    // |      |
    // +------+
    //           b
    {
        const box a(point(0, 0), point(3, 3));
        const point b(4, 4);

        const bool result = bg::disjoint(a, b);
        BOOST_ASSERT(result);
    }
    // not disjoint
    // a
    // +------+
    // |  b   |
    // |      |
    // +------+
    {
        const box a(point(0, 0), point(3, 3));
        const point b(2, 2);

        const bool result = bg::disjoint(a, b);
        BOOST_ASSERT(!result);
    }
}
```
* disjoint[color ff0000]
* disjoint[color ff0000]

###2つの図形が交点を持っているかを判定
2つの図形が交点を持っているかを判定するには、boost::geometry::intersects()アルゴリズムを使用する。

2つの線が交わっているかの判定：
```cpp
#include <boost/assert.hpp>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/linestring.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

int main()
{
    typedef bg::model::d2::point_xy<double> point;

    //  line2
    //    |
    // ---+---- line1
    //    |
    {
        const bg::model::linestring<point> line1 = boost::assign::list_of<point>(0, 2)(2, 2);
        const bg::model::linestring<point> line2 = boost::assign::list_of<point>(1, 0)(1, 4);

        const bool result = bg::intersects(line1, line2);
        BOOST_ASSERT(result); // 交点を持っている
    }
    // -------- line1
    // -------- line2
    {
        const bg::model::linestring<point> line1 = boost::assign::list_of<point>(0, 0)(2, 0);
        const bg::model::linestring<point> line2 = boost::assign::list_of<point>(0, 2)(2, 2);

        const bool result = bg::intersects(line1, line2);
        BOOST_ASSERT(!result); // 交点を持っていない
    }
}
```
* intersects[color ff0000]
* intersects[color ff0000]

###図形がもう一方の図形の完全な内側にあるかを判定
図形がもう一方の図形の内側にあるかを判定するには、boost::geometry::within()アルゴリズムを使用する。
within()は、第1引数の図形に対して、第2引数の図形が完全に内側にあればtrue、そうでなければfalseを返す。

<b>点が四角形内にあるかを判定：</b>

```cpp
#include <iostream>
#include <boost/geometry/geometry.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::box<point> box;

int main()
{
    const point top_left(0, 0);
    const point bottom_right(3, 3);
    const box   box(top_left, bottom_right);

    const point p(1.5, 1.5);

    if (bg::within(p, box)) {
        std::cout << "in" << std::endl;
    }
    else {
        std::cout << "out" << std::endl;
    }
}
```
* within[color ff0000]

出力結果：
```cpp
in
```

###2つの図形が空間的に等しいかを判定
2つの図形が空間的に等しいかを判定するには、boost::geometry::equals()アルゴリズムを使用する。
図形の形が同じでも位置が異なればfalseを返す。

以下は、三角形からなる四角形と、四角形が等しいか判定する処理：

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/algorithms/equals.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/geometry/geometries/adapted/boost_tuple.hpp>
#include <boost/assign/list_of.hpp>

BOOST_GEOMETRY_REGISTER_BOOST_TUPLE_CS(cs::cartesian)

namespace bg = boost::geometry;

// poly
// ae    d
// +-----+
// | +   |
// |   + |
// +-----+
// b     c
//
// box
// (0,0)
// +-----+
// |     |
// |     |
// +-----+
//       (3,3)
int main()
{
    typedef boost::tuple<int, int> point;

    bg::model::polygon<point> poly;
    bg::exterior_ring(poly) = boost::assign::tuple_list_of(0, 0)(0, 3)(3, 3)(3, 0)(0, 0);

    const bg::model::box<point> box(point(0, 0), point(3, 3));

    const bool result = bg::equals(poly, box);
    if (result) {
        std::cout << "equal" << std::endl;
    }
    else {
        std::cout << "not equal" << std::endl;
    }
}
```
* equals[color ff0000]

実行結果：
```cpp
equal
```

###面積を計算する
図形の面積を計算するには、boost::geometry::area()関数を使用する。
以下は、四角形と三角形の面積を計算する例：


```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

int main()
{
    typedef bg::model::d2::point_xy<double> point;
    typedef bg::model::box<point> box;
    typedef bg::model::polygon<point> polygon;

    // box
    {
        const box x(point(0, 0), point(3, 3));

        const double result = bg::area(x);
        std::cout << result << std::endl;
    }
    // polygon
    {
        polygon x;
        bg::exterior_ring(x) = boost::assign::list_of<point>(0, 0)(0, 3)(3, 3)(0, 0);

        const double result = bg::area(x);
        std::cout << result << std::endl;
    }
}



実行結果：```cpp
9
4.5
```
* area[color ff0000]

###図形の中心座標を計算する
図形の中心座標を計算するには、boost::geometry::centroid()か、boost::geometry::return_centroid<Point>()を使用する。

boost::geometry::centroid()は、中心座標の点を第2引数で参照として返し、
boost::geometry::return_centroid()は、中心座標の点を戻り値で返す。
return_centroid()は、テンプレート引数でPoint Conceptの型を指定する必要がある。

<b>三角形の中心座標を求める(centroidを使用)：</b>

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

int main()
{
    typedef bg::model::d2::point_xy<double> point;
    typedef bg::model::polygon<point> polygon;

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (2, 0)
        (4, 3)
        (0, 3)
        ;

    point p;
    bg::centroid(poly, p);

    std::cout << bg::dsv(p) << std::endl;
}
```
* centroid[color ff0000]

実行結果：
```cpp
(1.55556, 1.66667)
```

<b>return_centroidを使った場合：</b>

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

int main()
{
    typedef bg::model::d2::point_xy<double> point;
    typedef bg::model::polygon<point> polygon;

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (2, 0)
        (4, 3)
        (0, 3)
        ;

    const point p = bg::return_centroid<point>(poly);

    std::cout << bg::dsv(p) << std::endl;
}
```
* return_centroid<point>[color ff0000]

実行結果：
```cpp
(1.55556, 1.66667)

```

###図形の凸包を計算する
図形の凸包を計算するには、boost::geometry::convex_hull()を使用する。
第1引数で図形を渡すと、第2引数で参照として凸包の図形が返される。

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/assign/list_of.hpp>

int main()
{
    namespace bg = boost::geometry;

    typedef bg::model::d2::point_xy<double> point;
    typedef bg::model::polygon<point> polygon;

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (2.0, 1.3)
        (2.4, 1.7)
        (3.6, 1.2)
        (4.6, 1.6)
        (4.1, 3.0)
        (5.3, 2.8)
        (5.4, 1.2)
        (4.9, 0.8)
        (3.6, 0.7)
        (2.0, 1.3)
        ;

    polygon hull;
    bg::convex_hull(poly, hull);
       
    std::cout
        << "polygon: " << bg::dsv(poly) << std::endl
        << "hull: "    << bg::dsv(hull) << std::endl;
}
```
* convex_hull[color ff0000]

実行結果：
```cpp
polygon: (((2, 1.3), (2.4, 1.7), (3.6, 1.2), (4.6, 1.6), (4.1, 3), (5.3, 2.8), (5.4, 1.2), (4.9, 0.8), (3.6, 0.7), (2, 1.3)))
hull: (((2, 1.3), (2.4, 1.7), (4.1, 3), (5.3, 2.8), (5.4, 1.2), (4.9, 0.8), (3.6, 0.7), (2, 1.3)))
```

![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110803/20110803003137.png)
緑色部分が入力した図形。
点線部分が計算された凸包図形。


###2つの図形の距離を計算する
2つの図形の距離を計算するには、boost::geometry::distance()を使用する。
distance()は図形間の最短距離を返す。

<b>点と点の距離：</b>
```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;

int main()
{
    const point a(0, 0);
    const point b(3, 3);

    const double d = bg::distance(a, b);
    std::cout << d << std::endl;
}
```
* distance[color ff0000]

実行結果：
```cpp
4.24264
```

<b>点と三角形の距離：</b>
```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;

int main()
{
    const point p(0, 0);

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (3, 3)
        (6, 3)
        (6, 6)
        (3, 3)
        ;

    const double d = bg::distance(p, poly);
    std::cout << d << std::endl;
}
```
* distance[color ff0000]

実行結果：
```cpp
4.24264
```

###2つの図形の差を計算する
2つの図形の差を計算するには、boost::geometry::difference()を使用する。
第1引数と第2引数で渡した図形の差が、第3引数で返される。

```cpp
#include <iostream>
#include <vector>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;
typedef bg::model::box<point> box;

int main()
{
    const box bx(point(2, 0), point(6, 4.5));

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (1, 1)
        (5, 5)
        (5, 1)
        (1, 1)
        ;

    // bx - poly
    std::vector<polygon> out;
    bg::difference(bx, poly, out);
}
```
* difference[color ff0000]

計算された差の図形：
![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110805/20110805180722.png)

点線部分が、difference()で計算された図形。


###2つの図形の和を計算する
2つの図形の和を計算するには、boost::geometry::union_()を使用する。
第1引数と第2引数で渡した図形の和が、第3引数で返される。

```cpp
#include <iostream>
#include <vector>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;
typedef bg::model::box<point> box;

int main()
{
    const box bx(point(2, 0), point(6, 4.5));

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (1, 1)
        (5, 5)
        (5, 1)
        (1, 1)
        ;

    std::vector<polygon> out;
    bg::union_(bx, poly, out);
}
```
* union_[color ff0000]

計算された和の図形：
![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110805/20110805181538.png)

点線部分が、union_()で計算された図形。


###2つの図形の共通部分を計算する
2つの図形の共通部分を計算するには、boost::geometry::intersection()を使用する。
第1引数と第2引数で渡した図形の共通部分が、第3引数で返される。

```cpp
#include <vector>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;
typedef bg::model::box<point> box;

int main()
{
    box bx(point(2, 0), point(6, 4.5));

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (1, 1)
        (5, 5)
        (5, 1)
        (1, 1)
        ;

    std::vector<polygon> out;
    bg::intersection(bx, poly, out);
}
```

計算された共通部分の図形：
![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110805/20110805183210.png)

点線部分が、intersection()で計算された図形。


###図形の包絡線を計算する
図形の包絡線を計算するには、boost::geometry::envelope()を計算する。
第1引数として渡した図形の包絡線が、Box Conceptの型として第2引数で返される。

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;
typedef bg::model::box<point> box;

int main()
{

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (2.0, 1.3)
        (2.4, 1.7)
        (3.6, 1.2)
        (4.6, 1.6)
        (4.1, 3.0)
        (5.3, 2.8)
        (5.4, 1.2)
        (4.9, 0.8)
        (3.6, 0.7)
        (2.0, 1.3)
        ;

    box bx;
    bg::envelope(poly, bx);
      
    std::cout
        << "poly: " << bg::dsv(poly) << std::endl
        << "bx: "   << bg::dsv(bx) << std::endl;
}
```
* envelope[color ff0000]

実行結果：
```cpp
poly: (((2, 1.3), (2.4, 1.7), (3.6, 1.2), (4.6, 1.6), (4.1, 3), (5.3, 2.8), (5.4, 1.2), (4.9, 0.8), (3.6, 0.7), (2, 1.3)))
bx: ((2, 0.7), (5.4, 3))
```

計算された包絡線の図形：
![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110803/20110803003945.png)
点線部分が、envelope()で計算された包絡線。

また、boost::geometry::return_envelope<Box>()を使用すれば、参照ではなく戻り値として包絡線が返される。
```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/geometry/geometries/box.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;
typedef bg::model::box<point> box;

int main()
{

    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (2.0, 1.3)
        (2.4, 1.7)
        (3.6, 1.2)
        (4.6, 1.6)
        (4.1, 3.0)
        (5.3, 2.8)
        (5.4, 1.2)
        (4.9, 0.8)
        (3.6, 0.7)
        (2.0, 1.3)
        ;

    const box bx = bg::return_envelope<box>(poly);

    std::cout
        << "poly: " << bg::dsv(poly) << std::endl
        << "bx: "   << bg::dsv(bx) << std::endl;
}
```
* return_envelope<box>[color ff0000]

実行結果：
```cpp
poly: (((2, 1.3), (2.4, 1.7), (3.6, 1.2), (4.6, 1.6), (4.1, 3), (5.3, 2.8), (5.4, 1.2), (4.9, 0.8), (3.6, 0.7), (2, 1.3)))
bx: ((2, 0.7), (5.4, 3))
```

###図形の長さを計算する
図形の長さを計算するには、線の場合にはboost::geometry::length()を使用し、三角形の場合にはboost::geometry::perimeter()を使用する。

<b>線の長さを計算</b>

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/linestring.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;
typedef bg::model::d2::point_xy<double> point;

int main()
{
    bg::model::linestring<point> line = boost::assign::list_of<point>
        (0, 0)
        (1, 1)
        (4, 8)
        (3, 2)
        ;

    const double len = bg::length(line);
    std::cout << len << std::endl;
}
```
* length[color ff0000]

実行結果：
```cpp
15.1127


<b>三角形の長さを計算</b>


```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;

int main()
{
    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (1, 1)
        (5, 5)
        (5, 1)
        (1, 1)
        ;

    const double len = bg::perimeter(poly);
    std::cout << len << std::endl;
}


実行結果：
```cpp
13.6569
```
* perimeter[color ff0000]

###図形を逆向きにする
図形を逆向きにするには、boost::geometry::reverse()を使用する。

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;

int main()
{
    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (0, 0)
        (3, 3)
        (3, 1)
        (0, 0)
        ;

    bg::reverse(poly);

    std::cout << bg::dsv(poly) << std::endl;
}
```
* reverse[color ff0000]

実行結果：
```cpp
(((0, 0), (3, 1), (3, 3), (0, 0)))
```

###図形を単純化する
図形を単純化するには、boost::geometry::simplify()を使用する。
第1引数に単純化する元となる図形、
第2引数に出力先変数への参照、
第3引数に単純化の距離を指定する。

<b>線を単純化する例：</b>
```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/linestring.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::linestring<point> linestring;

int main()
{
    const linestring line = boost::assign::list_of<point>
        (3, 3)
        (3.8, 4)
        (6, 6)
        (4, 9)
        (5, 8)
        (7, 7)
        ;

    linestring result;
    bg::simplify(line, result, 0.5);

    std::cout << bg::dsv(line) << std::endl;
    std::cout << bg::dsv(result) << std::endl;
}
```
* simplify[color ff0000]

実行結果：
```cpp
((3, 3), (3.8, 4), (6, 6), (4, 9), (5, 8), (7, 7))
((3, 3), (6, 6), (4, 9), (7, 7))
```

![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110810/20110810165142.jpg)

緑が元となった図形。青がsimplify()によって単純化された図形。


###図形から重複した点を削除する
重複した点を削除するには、boost::geometry::unique()関数を使用する。

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/linestring.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::linestring<point> linestring;

int main()
{
    linestring line = boost::assign::list_of<point>
        (0, 0)
        (1, 1)
        (1, 1)
        (3, 3)
        (1, 1)
        ;

    bg::unique(line);

    std::cout << bg::dsv(line) << std::endl;
}
```
* unique[color ff0000]

実行結果：
```cpp
((0, 0), (1, 1), (3, 3), (1, 1))
```

###図形を平行移動する
図形を平行移動するには、boost::geometry::transform()関数で、translate_transformer戦略ポリシーを使用して移動量を指定する。


```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;
namespace trans = bg::strategy::transform;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;

int main()
{
    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (0, 0)
        (3, 3)
        (3, 0)
        (0, 0)
        ;

    // (1.5, 1.5)移動する
    trans::translate_transformer<point, point> translate(1.5, 1.5);

    polygon result;
    bg::transform(poly, result, translate);

    std::cout << bg::dsv(result) << std::endl;
}



実行結果：```cpp
(((1.5, 1.5), (4.5, 4.5), (4.5, 1.5), (1.5, 1.5)))
```
* translate_transformer[color ff0000]
* transform[color ff0000]

###図形を拡大縮小する
図形を拡大縮小するには、boost::geometry::transform()関数に、scale_transformer戦略ポリシーを使用して拡大率を指定する。


```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;
namespace trans = bg::strategy::transform;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;

int main()
{
    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (0, 0)
        (3, 3)
        (3, 0)
        (0, 0)
        ;

    // 3倍に拡大する
    trans::scale_transformer<point, point> translate(3.0);

    polygon result;
    bg::transform(poly, result, translate);

    std::cout << bg::dsv(result) << std::endl;
}


実行結果：
```cpp
(((0, 0), (9, 9), (9, 0), (0, 0)))
```
* scale_transformer[color ff0000]
* transform[color ff0000]

###図形を回転する
図形を回転するには、boost::geometry::transform()関数に、rotate_transformer戦略ポリシーを使用して回転する角度を指定する。
rotate_transformerのテンプレート引数で、角度の単位を選択できる。デグリ：boost::geometry::degree、ラジアン：boost::geometry::radian。

回転は、原点(0, 0)を中心に時計回りに行われる。

```cpp
#include <iostream>
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/point_xy.hpp>
#include <boost/geometry/geometries/polygon.hpp>
#include <boost/assign/list_of.hpp>

namespace bg = boost::geometry;
namespace trans = bg::strategy::transform;

typedef bg::model::d2::point_xy<double> point;
typedef bg::model::polygon<point> polygon;

int main()
{
    polygon poly;
    bg::exterior_ring(poly) = boost::assign::list_of<point>
        (0, 0)
        (3, 3)
        (3, 0)
        (0, 0)
        ;

    trans::rotate_transformer<point, point, bg::degree> translate(90.0);

    polygon result;
    bg::transform(poly, result, translate);

    std::cout << bg::dsv(result) << std::endl;
}
```
* rotate_transformer[color ff0000]
* bg::degree[color ff0000]
* transform[color ff0000]

実行結果：
```cpp
(((0, 0), (3, -3), (1.83691e-016, -3), (0, 0)))
```

![](http://cdn-ak.f.st-hatena.com/images/fotolife/f/faith_and_brave/20110811/20110811150157.png)

緑が回転前、オレンジが回転後の図形。

