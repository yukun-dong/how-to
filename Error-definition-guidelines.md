There are some suggested and unsuggested ways to define errors.

## Not suggest using functions

We don't suggest to define error using a function.

Here is a bad sample:
```
export function InvalidEnvNameError(): UserError {
  return new UserError(
    CoreSource,
    "InvalidEnvNameError",
    `Environment name can only contain letters, digits, _ and -.`
  );
}
```

The reason is the error stack will contains the function `InvalidEnvNameError`, which is not expected.

![image](https://user-images.githubusercontent.com/1658418/132474405-fdcd163d-350e-4b98-a534-1b4c60ecbaee.png)

You can write your own error class that extends `UserError` or `SystemError`
```
export class NotImplementedError extends SystemError {
  constructor(method: string) {
    super(new.target.name, `Method not implemented:${method}`, CoreSource);
  }
}
```