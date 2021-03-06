# EasyUnitTestFramework
An easy to use unit testing framework for C++11.
It's a very flexible framework, easy to set up and shouldn't increase compile times by too much.
# Setup
This framework uses CMake as its build system. You only have to include ```${EUTF_INCLUDE_DIR}``` in your search path. Make sure to set ```EUTF_BUILD_TESTS``` off if you don't want to build the examples.
If you don't use cmake it still shouldn't be too complicated, the framework only consists of one .hpp file.
# How to use
Tests should be created in .cpp files:
```c++
// ... whatever

// default place to print test results is the console
#include "EUTF.hpp"

EUTF_NEW_TEST(u8"name of the test is a quoted string")
{
	// test something
	EUTF_EXPECT(2 == 3); // will give an error
	EUTF_CHECK(2 == 3); // will give a warning
	EUTF_MESSAGE("this will be printed alongside the output that contains the results of the tests");
	EUTF_ASSERT(2 == 3); // this will give a fatal error, anything after ASSERT that fails won't get executed, it will exit from the test

	// everything else is skipped because EUTF_ASSERT failed
}

```
To run the test:
```c++
// you can call RUN_ALL_TESTS in the same file you've created the test, however I strongly recommend you call it in your main.cpp(or however it's called) file since you may have tests across multiple .cpp files 
// and there should be only ONE RUN_ALL_TESTS call in your entire project

// When you want to run the tests before you #include "EUTF.hpp" you must write #define EUTF_MAIN 
#define EUTF_MAIN
#include "EUTF.hpp"

// You could also call it in the global namespace:
/* EUTF_RUN_ALL_TESTS(); */

int main()
{
	EUTF_RUN_ALL_TESTS();
	return 0;
}
```
The output of that should be something like this:
```c++
path/to/your/file.cpp(line at which a fatal error/error/warning/message occured) name of the test is a quoted string [FAILURE]: 2 == 3
path/to/your/file.cpp(line at which a fatal error/error/warning/message occured) name of the test is a quoted string [WARNING]: 2 == 3
path/to/your/file.cpp(line at which a fatal error/error/warning/message occured) name of the test is a quoted string [MESSAGE]: this will be printed alongside the output that contains the results of the tests
path/to/your/file.cpp(line at which a fatal error/error/warning/message occured) name of the test is a quoted string [FATAL]: 2 == 3

Warnings: 1
Failures: 1
Fatal errors: 1
```
You can also have test suites which are a way to organize tests:
```c++
#include "EUTF.hpp"

EUTF_TEST_SUITE(u8"name")
{
	EUTF_NEW_TEST(u8"test")
	{
		EUTF_EXPECT(2 == 3);
	}

	// and as many tests as you want(even nested test suites)
}
EUTF_END_TEST_SUITE();

```
And the output should be:
```c++
path/to/your/file.cpp(line) name\test [FAILURE]: 2 == 3
```
You are not limited to just a name for a test, you can also have as many tags as you want besides the name. Tags are simply a way to differentiate multiple tests with the same name. In fact the name itself is considered a tag. Test suites however only have a name. 
```c++
EUTF_NEW_TEST(u8"the first tag is considered the name of the test", u8"another tag", u8"and another one")
{
	// ...
}
```
And in the output any notification will come up like this:
```
file(line) Test suite name in which the test is put\the first tag... ...
```
You can also define a section which is a test in a test. You can have as many nested sections as you want. To define it:
```c++
EUTF_TEST(u8"whatever")
{
	EUTF_SECTION(u8"name", u8"tags")
	{
		// EUTF_REQUIRE should only be used inside a section
		// Just like EUTF_EXPECT if it fails it's considered an error
		EUTF_REQUIRE(2 == 3);
		// The remaining section won't run, as well as the sub-sections
		EUTF_SECTION(u8"another section")
		{
			// Nothing will run
		}
	}
	// But the remaining test will still run
}
```
The difference between ```EUTF_REQUIRE``` and ```EUTF_EXPECT``` is that the former will exit from the current section, while the latter won't. If you want to exit from the test completely when something fails inside a section use ```EUTF_ASSERT```. Of course, you can still call EUTF_EXPECT/CHECK/MESSAGE inside a section.
Check examples/ for more.
 
