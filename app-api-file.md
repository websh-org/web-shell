# App API: File
> **Implemented in WebShell apps, by app developers, using the `WebShellApp` object .**

Supports file operations in WebShell apps.
## API Manifest
````js
{
  ...
  Object api: {
    ...
    Object file: {
      Object formats: {
        Object "format-id": {
          String name, 
          String extension = "*", 
          String type = "*/*",
          String encoding = "text",
          Boolean new,
          Boolean open,
          Boolean save
        }
      }
    }
  }
}
````
### File formats details:
* `name` - defaults to `"[format-id] file"`
* `extension` - defaults to `"*"`
  * You can specify several extensions, space-separated, without leading dots. 
  * Use `*` for any extension.
  * The first explicitly specified extension is the default extension used when saving files. If no default is supplied, the default is no extension.
* `type` - defaults to `"*/*"`
  * The expected mime type of files.
  * You can specify several extensions, space-separated. 
  * Use `*` for any mime type, or e.g. `text/*˙ for any text mime type.
  * The first explicitly specified type will be used when saving files. If it isn't supplied, the app will not be able to save files in this format,
* `new`
  * can this app create this file format?
* `open`
  * can this app open this file format?
* `save`
  * can this app save this file format?
* `encoding`
  * The encoding to be used when transferring this format. The valid values are:
    * `"text"` - The content is text and will be transfered as a string.
    * `"json"` - The content is JSON text and will be transfered as any JSONable value.
    * `"base64"` - The content is binary and will be transfered as a base64-encoded string.
    * `"binary"` - The content is binary and will be transferred as a `UInt8Array` buffer.
    * `"blob"` - The content is binary and will be transferred as a blob object.
    
  
  * If `binary` or `blob` is used, the ownership of the content will be transferred and e.g. no longer be available in the app after `file-save`.
  
## Commands

### file-open
The app receives the content of the file to be opened, as well as its extension and the ID of its format (this will be one of the formats specified in the app's manifest). The type of `content` depends on the encoding specified in the manifest.
````js
Command "file-open" { String extension, String format, content } => "OK"

Error "file-bad-file" { String reason } 
Error "file-bad-format" { String format }
````

### file-save
The app receives the format and the extension of the file that is to be saved, and returns the content of the file that will be saved. The expected type of `content` depends on the encoding specified in the manifest. The default mime type specified in the format in the manifest will be used for the saved file.

````js
Command "file-save" { String extension,  String format } => { content }

Error "file-bad-format" { String format }
````


### file-new
The app receives the format of the file that is to be created. 

````js
Command "file-new" { String format } => "OK"

Error "file-bad-format" { String format }
````

