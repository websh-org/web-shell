# WebShell App API

## Manifest
````js
{
  String name,
  String description,
  String version,
  String author,
  String docs, // Documentation URL
  String icon, // Icon url
  Object api: {
    Object file: {
      Object formats: {
        Object "format-id": {
          String description, 
          String extension, 
          String type,
          String encoding,
          Boolean create,
          Boolean open,
          Boolean save
        }
      }
    }
  }
}
````
### File formats details:
* `description` - defaults to `"[format-id] file"`
* `extension` - defaults to `"*"`
  * You can specify several extensions, space-separated, without leading dots. 
  * Use `*` for any extension.
  * The first explicitly specified extension is the default extension used when saving files. If no default is supplied, the default is no extension.
* `type` - defaults to `"*/*"`
  * The expected mime type of files.
  * You can specify several extensions, space-separated. 
  * Use `*` for any mime type, or e.g. `text/*˙ for any text mime type.
  * The first explicitly specified type will be used when saving files. If it isn't supplied, the app will not be able to save files in this format,
* `create`
  * can this app create this file format?
* `open`
  * can this app open this file format?
* `save`
  * can this app save this file format?
* `encoding`
  * The encoding to be used when transferring this format. The valid values are `"text"`, `"base64"`, `"binary"` and `"blob"`.
  * If `binary` or `blob` is used, the ownership of the content will be transferred and e.g. no longer be available in the app after `file-save`.
  
## Commands

### `file-open`

### args
````js
{
  String extension,
  String format,
  content
}
````
* The type of `content` depends on the encoding specified in the manifest.

### expected response

````js
"OK"
````

### `file-save`

### args
````js
{
  String extension,
  String format
}
````
* The type of `content` depends on the encoding specified in the manifest.

### expected response

````js
{
  content
}
````
* The expected type of `content` depends on the encoding specified in the manifest.

## `file-create`

### args
````js
{
  String format
}
````

### expected response

````js
"OK"
````
