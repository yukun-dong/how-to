## Introduction
`UserInteraction` defines a unified user interaction interface for Both CLI toolkit and VS Code extension toolkit.

```
interface UserInteraction {
  selectOption: (config: SingleSelectConfig) => Promise<Result<SingleSelectResult,FxError>>;
  selectOptions: (config: MultiSelectConfig) => Promise<Result<MultiSelectResult,FxError>>;
  inputText: (config: InputTextConfig) => Promise<Result<InputTextResult,FxError>>;
  selectFile: (config: SelectFileConfig) => Promise<Result<SelectFileResult,FxError>>;
  selectFiles: (config: SelectFilesConfig) => Promise<Result<SelectFilesResult,FxError>>;
  selectFolder: (config: SelectFolderConfig) => Promise<Result<SelectFolderResult,FxError>>;
  
  openUrl(link: string): Promise<Result<boolean,FxError>>;
  showMessage(
    level: "info" | "warn" | "error",
    message: string,
    modal: boolean,
    ...items: string[]
  ): Promise<Result<string|undefined,FxError>>;
  /**
   * 
   * @param message Only works for CLI info level, color for other log levels and VSCode window will be ignored
   */
  showMessage(
    level: "info" | "warn" | "error",
    message: Array<{content: string, color: Colors}>,
    modal: boolean,
    ...items: string[]
  ): Promise<Result<string|undefined,FxError>>;
  runWithProgress<T>(task: RunnableTask<T>, config: TaskConfig, ...args:any): Promise<Result<T,FxError>>;
}
```