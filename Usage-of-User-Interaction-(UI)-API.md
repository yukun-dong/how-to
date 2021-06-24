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

`validation` is a validation function to validate the user input. Both VS Code extension and CLI will provide validating mechanism and show warning message if validation fails:

In VS Code extension, validation warning look like this:

![image](https://user-images.githubusercontent.com/1658418/123197905-d1a4d180-d4de-11eb-9179-e5ca455be321.png)

In CLI, it looks like this:

![image](https://user-images.githubusercontent.com/1658418/123197667-6fe46780-d4de-11eb-8a20-c5063e055f90.png)

## Text Input
Text inputs are common ways to collect user input, which is an input text box for user to input a text string. 
You can call the following API to show a text box and collect input from human:
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

Selections are frequently used when a list of options will pop up for user to select. There are two types of selections: single selection (`selectOption`) and multiple selection (`selectOptions`).
By calling `` or `` APIs, you need to specify the option list with type `string[]` or `OptionItem[]`. 
```
const res:Result<SingleSelectResult, FxError> = await ui.selectOption({
  name: "questionId",
  title: "title",
  options: ["option1","option2"]
});
```
`string[]` option type is straitforward while `OptionItem[]` provides more sophisticated ways to display the option item.

Here is a sample of UI in VS Code extension:

![image](https://user-images.githubusercontent.com/1658418/123198695-27c64480-d4e0-11eb-9e3f-d21e11fc5c3d.png)

Here is the CLI counterparts:

![image](https://user-images.githubusercontent.com/1658418/123198809-53492f00-d4e0-11eb-9214-72a4e8bb1c7c.png)

## File picker

File pickers are use when the system need user to select file(s) from local file system. The answer to the file picker is a string path or an array of paths.

`selectFile`, `selectFiles` and `selectFolder` are three APIs related to file picker. The difference is `selectFile` is used for single file selection, `selectFiles` is used for multiple files selection, and `selectFolder` is used for folder selection.

File picker shows richer UI in VS Code extension than what in CLI. 
In VS Code, the file picker looks like this: a dialog will pop up for user to select file(s):

![image](https://user-images.githubusercontent.com/1658418/123214834-49ccc080-d4fa-11eb-96c1-78ea852003dc.png)

In CLI, the file picker is simply a text input box:

![image](https://user-images.githubusercontent.com/1658418/123215054-8a2c3e80-d4fa-11eb-932f-868ff9b48d5b.png)

## Message dialog

There are two cases when message dialogs are needed: 
-  The system shows some message and need collect user feedback.
-  The system shows some message and don't need any user confirm.

`showMessage` API is provided to show such message dialog. 

For the first case, you need to set parameter `modal` as true to show a modal dialog which will not disappear until user inputs. 
For modal dialog, you need to wait until user input, so you need to await until the returned promise is resolved:
```
const res:Result<string | undefined, FxError> = await ui.showMessasge("info", "message content", true, "item1", "item2");

```
For the second case, you need to set `modal` as false, which will disappear if it loses focus.

For non-modal dialog, normally, you don't need the user input. So we suggest you don't need to add wait the promise to be resolved:
```
ui.showMessasge("info", "message content", false, "item1", "item2");

```

Here is the UI of showing message using modal dialog in VS Code extension:

![image](https://user-images.githubusercontent.com/1658418/123233959-18a9bb80-d50d-11eb-90a5-28a56192958a.png)

Here is the UI of showing message using non-modal dialog in VS Code extension:

![image](https://user-images.githubusercontent.com/1658418/123234286-67efec00-d50d-11eb-80a1-e4013630f235.png)

