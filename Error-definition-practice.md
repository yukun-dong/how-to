## Three basic method to define error

There are many ways to define customized errors in the project:
1. Define an error as a constant value
1. Define an error as a function that returns a `UserError` or `SystemError`
1. Define an customized error class
1. directly construct a `UserError` or `SystemError` in the place where error happens

**We don't suggest first method**, unless you don't care about the stack at all.
Because the error stack is constant and meaningless in such a case.

**We don't suggest the second method either**, which add one more call stack on top of the stacks where the error really happens.

Here is a bad sample:
```
function MyError() {
  return new UserError("MyError", "my error message", "API");
}
console.log(MyError());
```
The error stack will contains the function `MyError`, which is not expected:

![image](https://user-images.githubusercontent.com/1658418/132477124-3e0904fb-2a06-485e-9e73-d61a5780e26c.png)

**We suggest the third method** to write your own error class that extends `UserError` or `SystemError`.

For example: 
```
class MyError extends UserError {
  constructor () {
    super(new.target.name, "my error message", "API")
  }
}
```
Then the stack will be clean: 

![image](https://user-images.githubusercontent.com/1658418/132477358-dfb459e9-513c-47c1-b33a-8d4696854fd6.png)

Another advantage of the third method is that it support `instanceof` operation on error type checking:
```
console.log(new MyError() instanceof MyError); // output `true`
```
Because in the constructor, we have passed `new.target.name` (which is actually `MyError`) as error name parameter into the `super` constructor.
Alternatively, if we don't want to define an error name different from the class name, we can just pass an empty string as the error name.
The following definition is equivalent to previous one:
```
class MyError extends UserError {
  constructor () {
    super("", "my error message", "API")
  }
}
```
The reason is the in constructor of `UserError`, we have some checking on error name, if the name is empty string, we will use the constructor name as the default error name:

![image](https://user-images.githubusercontent.com/1658418/132478785-319c8d87-0ef4-4736-ad5f-c8627337eeeb.png)

In addition to method three, **We also suggest the fourth method**, because it is the most flexible. 

You will find there are many parameters in `UserError` and `SystemError`, which is somehow not so convenient to create. 

We have tried to simplified the error constructor, but it will cause to refactor a lot of legacy codes. So we add a new set of builder functions for them:
![image](https://user-images.githubusercontent.com/1658418/132480106-adc4e4b5-4f64-4e83-ad91-b6a2c4011412.png)

We support two override constructors. We support a wrapped error builder that take the predefined `Error` as input and create a wrapped error.
Such builder is useful when we meet with some errors that is throwed by third party modules and we can directly wrap the error and convert into `FxError`:

```
const wrap = UserError.build("API", new RangeError("range error"));
console.log(wrap);
console.log(wrap instanceof UserError); //true
console.log(wrap.name); //RangeError
```
The output of the above code is:
![image](https://user-images.githubusercontent.com/1658418/132481211-baa8ce2e-741b-4a92-a8ef-d8c111b81ca8.png)

In such a case the wrap.name is the error name the name of input error: `RangeError`.