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
