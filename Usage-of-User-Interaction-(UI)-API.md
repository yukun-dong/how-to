## Introduction
`UserInteraction` defines a unified user interaction interface for Both CLI toolkit and VS Code extension toolkit. 
It is a utility interface that helps to show UI elements to collect input from human. It also provide some utility functions like open a link in external browser or show progress bar for some time consuming task.  
Here we describes some basic usage sample for each of them and how it acts in both CLI and VS Code extension.

```

/**
 * Definition of user interaction, which is platform independent
 */
export interface UserInteraction {
  /**
   * Shows a single selection list
   * @param config single selection config
   * @returns A promise that resolves to the single select result wrapper or FxError
   * @throws FxError
   */
  selectOption: (config: SingleSelectConfig) => Promise<Result<SingleSelectResult, FxError>>;
  /**
   * Shows a multiple selection list
   * @param config multiple selection config
   * @returns A promise that resolves to the multiple select result wrapper or FxError
   * @throws FxError
   */
  selectOptions: (config: MultiSelectConfig) => Promise<Result<MultiSelectResult, FxError>>;
  /**
   * Opens an input box to ask the user for input.
   * @param config text input config
   * @returns A promise that resolves to the text input result wrapper or FxError
   * @throws FxError
   */
  inputText: (config: InputTextConfig) => Promise<Result<InputTextResult, FxError>>;
  /**
   * Shows a file open dialog to the user which allows to select a single file
   * @param config file selector config
   * @returns A promise that resolves to the file selector result wrapper or FxError
   * @throws FxError
   */
  selectFile: (config: SelectFileConfig) => Promise<Result<SelectFileResult, FxError>>;
  /**
   * Shows a file open dialog to the user which allows to select multiple files
   * @param config multiple files selector config
   * @returns A promise that resolves to the multiple files selector result wrapper or FxError
   * @throws FxError
   */
  selectFiles: (config: SelectFilesConfig) => Promise<Result<SelectFilesResult, FxError>>;
  /**
   * Shows a file open dialog to the user which allows to select a folder
   * @param config folder selector config
   * @returns A promise that resolves to the folder selector result wrapper or FxError
   * @throws FxError
   */
  selectFolder: (config: SelectFolderConfig) => Promise<Result<SelectFolderResult, FxError>>;

  /**
   * Opens a link externally in the browser. 
   * @param link The uri that should be opened.
   * @returns A promise indicating if open was successful.
   */
  openUrl(link: string): Promise<Result<boolean, FxError>>;
  /**
   * Show an information/warnning/error message to users. Optionally provide an array of items which will be presented as clickable buttons.
   * @param level message level
   * @param message The message to show.
   * @param items A set of items that will be rendered as actions in the message.
   * @returns A promise that resolves to the selected item or `undefined` when being dismissed.
   */
  showMessage(
    level: "info" | "warn" | "error",
    message: string,
    modal: boolean,
    ...items: string[]
  ): Promise<Result<string | undefined, FxError>>;

  /**
   * Show an information/warnning/error message with different colors to users, which only works for CLI.  
   * @param level message level
   * @param message The message with color to show. The color only works for CLI.
   * @param items A set of items that will be rendered as actions in the message.
   * @returns A promise that resolves to the selected item or `undefined` when being dismissed.
   */
  showMessage(
    level: "info" | "warn" | "error",
    message: Array<{ content: string, color: Colors }>,
    modal: boolean,
    ...items: string[]
  ): Promise<Result<string | undefined, FxError>>;

  /**
   * A function to run a task with progress bar. (CLI and VS Code has different UI experience for progress bar)
   * @param task a runnable task with progress definition
   * @param config task running confiuration
   * @param args args for task run() API
   * @returns A promise that resolves the wrapper of task running result or FxError
   */
  runWithProgress<T>(task: RunnableTask<T>, config: TaskConfig, ...args: any): Promise<Result<T, FxError>>;
}
```

## Common UI Config

We have many different UI elements such as text input, selection, file selector. Most of them have some commonly defined config fields to specify.

The common UI config is defined by `UIConfig` interface:

```
/**
 * A base structure of user interaction (UI) configuration
 */
export interface UIConfig<T> {
  /**
   * name is the identifier of the UI
   */
  name: string;
  /**
   * human readable meaningful display name of the UI
   */
  title: string;
  /**
   * placeholder in the UI
   */
  placeholder?: string;
  /**
   * prompt text providing some ask or explanation to the user
   */
  prompt?: string;
  /**
   * `step` and `totalSteps` are used to describe the progress in question flow
   * `step` is the sequence number of current question
   */
  step?: number;
  /**
   * `totalStep` is the number of questions totally
   */
  totalSteps?: number;
  /**
   * default input value
   */
  default?: T;

  /**
   * A function that will be called to validate input and to give a hint to the user.
   *
   * @param input The current value of the input to be validated.
   * @return A human-readable string which is presented as diagnostic message.
   * Return `undefined` when 'value' is valid.
   */
  validation?: (input: T) => string | undefined | Promise<string | undefined>;
}
```

`name`, `title` are basic required fields for a UI config. `name` is the unique ID of the question, title is the display name of the question. 

`prompt`, `placeholder`, `default` provide user richer experience, which are supported in VS Code extension. 

`step` and `totalSteps` are used to describe the progress in question flow. Each UI element can have a `go-back` and `continue` button to control the progress of the question flow:

![image](https://user-images.githubusercontent.com/1658418/123196983-69a1bb80-d4dd-11eb-9af2-199f110cb918.png)

The left arrow ![image](https://user-images.githubusercontent.com/1658418/123197025-7de5b880-d4dd-11eb-9705-e3f2b3c0cf0e.png) is `go back` button if the UI element has `step` greater than one. 

The tick ![image](https://user-images.githubusercontent.com/1658418/123197245-d3ba6080-d4dd-11eb-9f36-af6288abcd94.png) is an `ok` button to continue to next question in the question flow.

`validation` are validation definition for user input. Both VS Code extension and CLI will provide validating mechanism to check whether input is valid or not and show warning message if validation fails:

In VS Code extension, validation warning look like this:

![image](https://user-images.githubusercontent.com/1658418/123197622-5c390100-d4de-11eb-8cad-cde2d45e58f9.png)

In CLI, it looks like this:

![image](https://user-images.githubusercontent.com/1658418/123197667-6fe46780-d4de-11eb-8a20-c5063e055f90.png)

## Text Input
Text inputs are common ways to collect user input, which is an input text box for user to input a text string. 
User can call the following API to show a text box and collect input from human:
```
const res:Result<InputTextResult, FxError> = await ui.inputText({
  name: "questionId",
  title: "title",
  prompt: 'prompt',
  placeholder: 'My Placeholder'
});
```
For VS Code extension, you can define richer experience with `prompt` and `placeholder`. Text input looks like this:

![image](https://user-images.githubusercontent.com/1658418/123196195-12e7b200-d4dc-11eb-802f-f4b598392528.png)

For CLI, text input is just a command-line query like this:

![image](https://user-images.githubusercontent.com/1658418/123195677-2a726b00-d4db-11eb-8966-94370123e2d0.png)

## Selection


