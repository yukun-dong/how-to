There are many ways to define customized errors in the project:
1. Define an error as a constant value
1. Define an error as a function that returns a `UserError` or `SystemError`
1. Define an customized error class

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

**We strongly suggest the third method** to write your own error class that extends `UserError` or `SystemError`.

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