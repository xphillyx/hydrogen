# Hydrogen Language Plugin API

## Registering Your Provider

When your package is activated, a function called "myProviderFunctionName" in your package's `main` file [1] will be run, and it should return a Language Provider object as specified below.

[1] Your `main` file is specified by the `"main"` field of your `package.json`.

```json
"providedServices": {
  "hydrogen.language": {
    "versions": {
      "0.1.0": "myProviderFunctionName"
    }
  }
}
```

## The Language Provider Object

Your provider function should return an object of the following form:

```javascript
provider = {
    // (required): This provider will be used for any Python kernels.
    // More specifically, any kernel whose kernel.json has the 'language' field
    // set to 'python'.
    language: 'python',

    // (optional): this regex will be used to select a portion of the current
    // line to send to the kernel in a complete_request.
    // The coffeescript `line.match(regex)?[0] or ''` will be used to get the
    // completion prefix.
    // If this is not specified, a default will be used.
    completionPrefixRegex: /([^\d\W]|[\u00A0-\uFFFF])[\w.\u00A0-\uFFFF]*$/,

    // (optional): Return a string containing the code to execute
    // editor: TextEditor
    // bufferPosition: a Point: {row: int, column: int}
    // triggerType: one of 'selection', 'point', 'file'
    // selection: if triggerType is 'selection', this contains the code selected
    //      by the user to be executed
    // default: the code that Hydrogen would run if this function didn't exist
    getCode: ({editor, bufferPosition, triggerType, selection, default}) => {
        return editor.getLastCursor().getCurrentBufferLine();
    }

    // (optional): called after getCode, if present
    // called for side effects only
    preExecute: ({editor, code}) => {
        // mutate the editor or whatever
    }

    // (optional): called when your provider needs to be cleaned up. Unsubscribe
    // from things, kill any processes, etc.
    dispose: () => {}
}
```
