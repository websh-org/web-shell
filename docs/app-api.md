# WebShell App API

The connection between the app (in a sandboxed iframe) and the shell (in the parent window) is established through an async-enabled
messaging channel. The app side of the connection is managed by the `WebShellApp` object from the `@websh/web-shell-app` package.

On connection (immediately after the app is loaded), the app sends  to the shell its manifest, which consists of various meta data about the app and the description of its capabilities according to this API. Use `WebShellApp.manifest` in the app to specify the app's manifest.

After connection, the app receives commands from the shell, which it executes and returns the resuls, or throws an error. The result or the error is then transmitted back to the shell. Use `WebShellApp.command` in the app to register command handlers.

The app can also send events to the shell. Use `WebShellApp.trigger` in the app to send an event.

Commands, expected results, errors and events are defined in various app APIs. For each API that it supports, the app must include an entry in the `.api` object in its manifest, with contents as specified for each app API.

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
    ...
  }
}
````
## App APIs
* [file](app-api-file)