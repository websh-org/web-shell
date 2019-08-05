# Shell API
The shell object is the central hub of WebShell. It contains commands for all operations that are available to UI developers. WebShell and all its internal components can be controlled only through the shell API. 

> What about state? UIs need access to the state of various components to correctly display the EU. Both event-driven and reactivity-driven state reporting should be supported.

## Apps

### app-open
Open an app from a url in an iframe.

````js
Command "app-open" { String url, HTMLIframeElement iframe } => { String appid }

Error "app-bad-url" { String url }
Error "app-http-error" { Number status, String reason }
````
> - [ ] Create an `app-controller` for the iframe.
> - [ ] Call `app-load` on the controller to load the app.
> - [ ] Return the app id.

### app-close
Ask the app to close nicely. 
````js
Command "app-close" { String appid } => "OK"

Error "app-bad-appid" { Strint appid } 
````
> - [ ] Call `app-close` on the app controller.
> - [ ] If successfull, call `app-unload` on the controller.

### app-unload
Force unload an app.
````js
Command "app-unload" { String appid } => "OK"

Error "app-bad-appid" { Strint appid }
````
> - [ ] Call `app-unload` on the app controller.
> - [ ] Possibly force unloading by setting the src attribute of the iframe to null

### app-api-call
> This probably shouldn't exist. Shell itself should probably be aware of all valid app API calls and have a separate command for each of them. Which means that we would potentially have to add many more commands to the shell.

Pass an app API call to an app.
````js
Command "app-api-call" { String api, String cmd, Object args } => [see docs for app APIs]
````

## FS Volumes
### fs-volume-mount
Open a FS adapter in an iframe and connect it to the file system.
````js
Command "fs-volume-add" { String url, HTMLIframeElement iframe } => { String vid }

Error "fs-volume-already-mounted" { vid }
````
> - [ ] Create a FS adapter controller.
> - [ ] Call `fs-adapter-load` on the FS adapter controller.
> - [ ] Wait for login
> - [ ] Create a volume
> - [ ] Call `fs-volume-mount` on the FS.
> - [ ] The URL must be whitelisted in shell conf
> - [ ] Return the volume id.

### fs-volume-umount
Ask the FS volume to disconnect nicely. 
````js
Command "fs-volume-disconnect" { String vid } => "OK"

Error "fs-volume-not-mounted" { vid }
````
> - [ ] Call `fs-volume-umount` on the FS.
> - [ ] If successfull, call `fs-adapter-close` on the FS adapter controller.
> - [ ] If successfull, call `fs-adapter-unload` on the FS adapter controller.


### fs-volume-umount-force
Force unload an app.
````js
Command "app-unload" { String appid } => "OK"

Error "app-bad-appid" { String appid }
````
> - [ ] Call `fs-volume-umount-force` on the FS.
> - [ ] Call `fs-adapter-unload` on the FS adapter controller.

## FS Operations
FS operations are passed to the FS. See Fyle System API for details.
> FS operations should probably support multiple encodings, to make it easier for UI developers.
> Recoding should be done in one place, either here or in the FS.

### fs-get 
Get information for a path in the FS, possibly including the content.
````js
Command "fs-get" { 
  String path, 
  Object include: { 
    Boolean parent, 
    Boolean parents, 
    Boolean children, 
    Boolean content 
  } => WebShellFile

Error "fs-not-found" { String path }
Error "fs-access-denied" { String path }
````

### fs-create
Create a named file at a path in the FS.
````js
Command "fs-create" { 
  String parentpath, 
  String name,
  String type,
  ArrayBuffer content,
  Object include: { 
    Boolean parent, 
    Boolean parents, 
    Boolean children, 
    Boolean content 
  } => WebShellFile

Error "fs-not-found" { String path }
Error "fs-access-denied" { String path }
````


### fs-update
Update the content of a file at a path in the FS.
````js
Command "fs-update" { 
  String path, 
  String type,
  ArrayBuffer content,
  Object include: { 
    Boolean parent, 
    Boolean parents, 
    Boolean children, 
    Boolean content 
  } => WebShellFile

Error "fs-not-found" { String path }
Error "fs-access-denied" { String path }
````
