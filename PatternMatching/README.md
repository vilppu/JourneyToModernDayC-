# Pattern Matching of Discriminated Unions with C++

Modern C++ supports pattern matching of discriminated unions (almost) out-of-box.

[`variant<>`](https://en.cppreference.com/w/cpp/utility/variant) is the standard C++ class template for discriminated union types and [`visit`](https://en.cppreference.com/w/cpp/utility/variant/visit) can be used to match `variant<>`  types.

The only thing missing from standard library is the generic visitable type that is feed to [`visit`](https://en.cppreference.com/w/cpp/utility/variant/visit) function but that is quite easy to implement like:

```
template<class... Ts> struct match : Ts... { using Ts::operator()...; };
template<class... Ts> match(Ts...)->match<Ts...>;
```

Following is simple example of matching geometrical shapes. The same example can be found in [PatternMatching.cpp](PatternMatching.cpp).

```
#include <iostream>
#include <variant>

using namespace std;

constexpr double pi = 3.14159265358979323846;
template<class... Ts> struct match : Ts... { using Ts::operator()...; };
template<class... Ts> match(Ts...)->match<Ts...>;

struct Rectangle
{
	Rectangle(double width, double height) :
		Width(width),
		Heigh(height)
	{
	}

	const double Width;
	const double Heigh;
};

struct Circle
{
	Circle(double radius) :
		Radius(radius)
	{
	}

	const double Radius;
};

typedef variant<Rectangle, Circle> Shape;

int main()
{
	auto rectangle = Shape(Rectangle(10, 20));
	auto circle = Shape(Circle(30));

	auto area = [](auto shape) {
		return visit(
			match{
				[](Rectangle rectangle) {
					return rectangle.Width * rectangle.Heigh;
				},
				[](Circle circle) {
					return pi * (circle.Radius * circle.Radius);
				},
			}, shape);
	};

	auto areaOfRectangle = area(rectangle);
	auto areaOfCircle = area(circle);

	cout
		<< "Area of the rectangle: " << areaOfRectangle << endl
		<< "Area of the circle: " << areaOfCircle << endl
		<< endl;

	return 0;
}
```
