# crashReporter

> Envía los informes de fallos a un servidor remoto.

Proceso: [Main](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process)

The following is an example of setting up Electron to automatically submit crash reports to a remote server:

```javascript
const { crashReporter } = require('electron')

crashReporter.start({ submitURL: 'https://your-domain.com/url-to-submit' })
```

Para configurar un servidor que acepte y procese los informes de fallos, se pueden utilizar los siguientes proyectos:

* [socorro](https://github.com/mozilla/socorro)
* [mini-breakpad-server](https://github.com/electron/mini-breakpad-server)

O utilice una solución alojada por terceros:

* [Backtrace](https://backtrace.io/electron/)
* [Sentry](https://docs.sentry.io/clients/electron)
* [BugSplat](https://www.bugsplat.com/docs/platforms/electron)

Crash reports are stored temporarily before being uploaded in a directory underneath the app's user data directory (called 'Crashpad' on Windows and Mac, or 'Crash Reports' on Linux). You can override this directory by calling `app.setPath('crashDumps', '/path/to/crashes')` before starting the crash reporter.

On Windows and macOS, Electron uses [crashpad](https://chromium.googlesource.com/crashpad/crashpad/+/master/README.md) to monitor and report crashes. On Linux, Electron uses [breakpad](https://chromium.googlesource.com/breakpad/breakpad/+/master/). This is an implementation detail driven by Chromium, and it may change in future. In particular, crashpad is newer and will likely eventually replace breakpad on all platforms.

### Note about Node child processes on Linux

If you are using the Node.js `child_process` module and want to report crashes from those processes on Linux, there is an extra step you will need to take to properly initialize the crash reporter in the child process. This is not necessary on Mac or Windows, as those platforms use Crashpad, which automatically monitors child processes.

Since `require('electron')` is not available in Node child processes, the following APIs are available on the `process` object in Node child processes. Note that, on Linux, each Node child process has its own separate instance of the breakpad crash reporter. This is dissimilar to renderer child processes, which have a "stub" breakpad reporter which returns information to the main process for reporting.

#### `process.crashReporter.start(options)`

See [`crashReporter.start()`](#crashreporterstartoptions).

#### `process.crashReporter.getParameters()`

See [`crashReporter.getParameters()`](#crashreportergetparameters).

#### `process.crashReporter.addExtraParameter(key, value)`

See [`crashReporter.addExtraParameter(key, value)`](#crashreporteraddextraparameterkey-value).

#### `process.crashReporter.removeExtraParameter(key)`

See [`crashReporter.removeExtraParameter(key)`](#crashreporterremoveextraparameterkey).

## Métodos

El módulo `crashReporter` tiene los siguientes métodos:

### `crashReporter.start(options)`

* `options` Object
  * `submitURL` String - URL a donde se enviarán los informes de errores como un POST.
  * `productName` String (optional) - Defaults to `app.name`.
  * `companyName` String (optional) _Deprecated_ - Deprecated alias for `{ globalExtra: { _companyName: ... } }`.
  * `uploadToServer` Boolean (optional) - Whether crash reports should be sent to the server. If false, crash reports will be collected and stored in the crashes directory, but not uploaded. Por defecto es `true`.
  * `ignoreSystemCrashHandler` Boolean (optional) - If true, crashes generated in the main process will not be forwarded to the system crash handler. Por defecto es `false`.
  * `rateLimit` Boolean (optional) _macOS_ _Windows_ - If true, limit the number of crashes uploaded to 1/hour. Por defecto es `false`.
  * `compress` Boolean (optional) - If true, crash reports will be compressed and uploaded with `Content-Encoding: gzip`. Por defecto es `true`.
  * `extra` Record<String, String> (optional) - Extra string key/value annotations that will be sent along with crash reports that are generated in the main process. Only string values are supported. Crashes generated in child processes will not contain these extra parameters to crash reports generated from child processes, call [`addExtraParameter`](#crashreporteraddextraparameterkey-value) from the child process.
  * `globalExtra` Record<String, String> (optional) - Extra string key/value annotations that will be sent along with any crash reports generated in any process. These annotations cannot be changed once the crash reporter has been started. If a key is present in both the global extra parameters and the process-specific extra parameters, then the global one will take precedence. By default, `productName` and the app version are included, as well as the Electron version.

This method must be called before using any other `crashReporter` APIs. Once initialized this way, the crashpad handler collects crashes from all subsequently created processes. The crash reporter cannot be disabled once started.

This method should be called as early as possible in app startup, preferably before `app.on('ready')`. If the crash reporter is not initialized at the time a renderer process is created, then that renderer process will not be monitored by the crash reporter.

**Note:** You can test out the crash reporter by generating a crash using `process.crash()`.

**Note:** If you need to send additional/updated `extra` parameters after your first call `start` you can call `addExtraParameter`.

**Note:** Parameters passed in `extra`, `globalExtra` or set with `addExtraParameter` have limits on the length of the keys and values. Key names must be at most 39 bytes long, and values must be no longer than 127 bytes. Keys with names longer than the maximum will be silently ignored. Key values longer than the maximum length will be truncated.

**Nota:** Este método solo está disponible en el proceso principal.

### `crashReporter.getLastCrashReport()`

Returns [`CrashReport`](structures/crash-report.md) - The date and ID of the last crash report. Only crash reports that have been uploaded will be returned; even if a crash report is present on disk it will not be returned until it is uploaded. En caso de que no haya reportes subidos, `null` es retornado.

**Nota:** Este método solo está disponible en el proceso principal.

### `crashReporter.getUploadedReports()`

Devuelve [`CrashReport []`](structures/crash-report.md):

Returns all uploaded crash reports. Each report contains the date and uploaded ID.

**Nota:** Este método solo está disponible en el proceso principal.

### `crashReporter.getUploadToServer()`

Returns `Boolean` - Whether reports should be submitted to the server. Set through the `start` method or `setUploadToServer`.

**Nota:** Este método solo está disponible en el proceso principal.

### `crashReporter.setUploadToServer(uploadToServer)`

* `uploadToServer` Boolean - Whether reports should be submitted to the server.

This would normally be controlled by user preferences. This has no effect if called before `start` is called.

**Nota:** Este método solo está disponible en el proceso principal.

### `crashReporter.addExtraParameter(key, value)`

* `key` String - Parameter key, must be no longer than 39 bytes.
* `value` String - Parameter value, must be no longer than 127 bytes.

Establecer un parámetro adicional que se enviará con el informe de fallos. The values specified here will be sent in addition to any values set via the `extra` option when `start` was called.

Parameters added in this fashion (or via the `extra` parameter to `crashReporter.start`) are specific to the calling process. Adding extra parameters in the main process will not cause those parameters to be sent along with crashes from renderer or other child processes. Similarly, adding extra parameters in a renderer process will not result in those parameters being sent with crashes that occur in other renderer processes or in the main process.

**Note:** Parameters have limits on the length of the keys and values. Key names must be no longer than 39 bytes, and values must be no longer than 20320 bytes. Keys with names longer than the maximum will be silently ignored. Key values longer than the maximum length will be truncated.

**Note:** On linux values that are longer than 127 bytes will be chunked into multiple keys, each 127 bytes in length.  Por ejemplo. `addExtraParameter('foo', 'a'.repeat(130))` will result in two chunked keys `foo__1` and `foo__2`, the first will contain the first 127 bytes and the second will contain the remaining 3 bytes.  On your crash reporting backend you should stitch together keys in this format.

### `crashReporter.removeExtraParameter(key)`

* `key` String - Parameter key, must be no longer than 39 bytes.

Remove an extra parameter from the current set of parameters. Future crashes will not include this parameter.

### `crashReporter.getParameters()`

Returns `Record<String, String>` - The current 'extra' parameters of the crash reporter.

## Carga útil del informe de fallos

El informador de fallos enviará la siguiente información al `submitURL` como un `multipart/form-data` `POST`:

* `ver` String - La versión de Electron.
* `platform` String - por ejemplo, "win32".
* `process_type` String - por ejemplo, "renderer".
* `guid` String - por ejemplo, "5e1286fc-da97-479e-918b-6bfb0c3d1c72".
* `_version` Cadena - La versión en `package.json`.
* `_productName` String - El nombre del producto en el objeto `crashReporter` `options`.
* `prod` String - Name of the underlying product. In this case Electron.
* `_companyName` Cadena - El nombre de la empresa en el objeto `crashReporter` `options`.
* `upload_file_minidump` File - El informe de fallos en el formato de `minidump`.
* Todas las propiedades de nivel uno del objeto `extra` en el objeto `crashReporter` `options`.