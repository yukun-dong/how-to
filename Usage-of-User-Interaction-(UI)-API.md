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