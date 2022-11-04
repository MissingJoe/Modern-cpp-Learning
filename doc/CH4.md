### Types and struct

`std::pair` An STL build-in struct with two fields of any type.

~~~c++
struct Pair {
	type1 first;
	type2 secont;
};

// define and use
std::pair<int, string> numSuffix = {1,"st"};
cout << numSuffix.first << numSuffix.second;

// use std::pair to return a multi-result
std::pair<bool, Student> lookupStudent(string name) {
	Student blank;
	if (found(name)) return std::make_pair(false, blank);
 	Student result = getStudentWithName(name);
	return std::make_pair(true, result);
}
~~~



`auto` Keyword used to declaring a var without telling the type of var. The compiler will deduce the type automatically.

Here is a example for `auto` . Use it to reduce long type names

~~~c++
std::pair<bool, std::pair<double, double>> quadratic(double a, double b, double c){
	double radical = pow(b, 2) - (4*a*c);
	if(radical < 0){
		return {false, {-1, -1}};
	} else {
		double root1 = ( -1*b + sqrt(radical) ) / (2*a);
		double root2 = ( -1*b - sqrt(radical) ) / (2*a);
		return {true, {root1, root2}};
	}
}

int main() {
     double a, b, c;
     std::cin >> a >> b >> c;
     // use auto can simplify the code with complex typewriting
     std::pair<bool, std::pair<double, double>> result = quadratic(a, b, c);
     //auto result = quadratic(a, b, c); 
     bool found = result.first;
     if (found) {
         std::pair<double, double> solutions = result.second;
         //auto solutions = result.second;
         std::cout << solutions.first << solutions.second << endl;
     } else {
     	std::cout << “No solutions found!” << endl;
     }
}
~~~



Strutured binding lets you initialize directly from the contents of a struct

~~~c++
// before
auto p = std::make_pair(“s”, 5);
string a = s.first;
int b = s.second;

// after
auto p = std::make_pair(“s”, 5);
auto [a, b] = p;
~~~



With strutured binding, we can simplify the code before like that:

~~~c++
int main() {
     double a, b, c;
     std::cin >> a >> b >> c;
     auto [found, solutions] = quadratic(a, b, c);
     if (found) {
     	auto [x1, x2] = solutions;
     	std::cout << x1 << “ ” << x2 << endl;
     } else {
     	std::cout << “No solutions found!” << endl;
     }
}
~~~



typical error

~~~c++
// add one to all the elem. Use reference not copy
void shift(vector<std::pair<int, int>>& nums) {
    for (auto& [num1, num2]: nums) {
    	num1++;
    	num2++;
    }
}
shift({{1, 1}}); 
// {{1, 1}} is an rvalue, it can’t be referenced
~~~



### Multi-File Programs, Abstraction, and the Preprocessor

compiler read the code with three step:

1. Preprocessing, `#define` instead in this step.

   `#define` is just string replace. Example

   ~~~c++
   #define LEFT_MARGIN 100
   #define RIGHT_MARGIN 100
   #define SCALE .5
   #define TOTAL_MARGIN LEFT_MARGIN * SCALE + RIGHT_MARGIN * SCALE
   
   int x = 2 * TOTAL_MARGIN;
   
   // int x = 2 * LEFT_MARGIN * SCALE + RIGHT_MARGIN * SCALE;
   // int x = (2 * LEFT_MARGIN * SCALE) + RIGHT_MARGIN * SCALE;
   // not int x = 2 * (LEFT_MARGIN * SCALE + RIGHT_MARGIN * SCALE);
   ~~~

   **conditional compilation**

   what's the meaning of this code?

   File: myStruct.h

   ~~~c++
   #ifndef MyStruct_Included
   #define MyStruct_Included
   struct MyStruct {
       int x;
       double y;
       char z;
   };
   #endif
   ~~~

   `#ifndef MyStruct_Included` means if not `#define` then execute the code below.

   Then in another `.cpp` we include the code in `.h` 

   ~~~c++
   #include "mystruct.h"
   #include "mystruct.h" // #include the same file twice
   int main() {
   	return 0;
   }
   ~~~

   Without conditional compilation, the include code will call the error will mult-include. Then what if we write `#ifndef` in the header file.

   The compiler will translate the code in this

   ~~~c++
   #ifndef MyStruct_Included
   #define MyStruct_Included
   struct MyStruct {
       int x;
       double y;
       char z;
   };
   #endif
   
   #ifndef MyStruct_Included
   #define MyStruct_Included
   struct MyStruct {
       int x;
       double y;
       char z;
   };
   #endif
   int main() {
   	return 0;
   }
   
   ~~~

   We can see that in the first section, there is not `#define` of `MyStruct_Included`, so execute the code of 

   ~~~c++
   #define MyStruct_Included
   struct MyStruct {
       int x;
       double y;
       char z;
   };
   ~~~

   Then in the second section, with `#define` of `MyStruct_Included` , the conditional compilation will not execute. So that there are no errors in compilation.

   **With this example, we get the `#ifndef` can solve the mult-include problem in a very big project.** In big project, include will be complex, so mult-include usually cause many problems. `#ifndef fileName #define fileName #endif` in every header file is very usefull way to solve problems above.

2. compilation, without implementation will not error in this step.

   ~~~c++
   #include <iostream>
   using namespace std;
   int Factorial(int n); // Prototype for a function to compute n!
   int main() {
       cout << Factorial(10) << endl;
       return 0;
   }
   ~~~

3. Linking, `.h` file with the statement of all the functions and `.cpp` file with the implementation. 

### The `# define` derective

