# kulit

> Kelola file dan URL menggunakan aplikasi bawaan mereka.

Process: [Main](../glossary.md#main-process), [Renderer](../glossary.md#renderer-process) (non-sandboxed only)

The `shell` modul menyediakan fungsi yang berkaitan dengan integrasi desktop.

Contoh membuka URL di browser default pengguna:

```javascript
const { shell } = require('electron') shell.openExternal ('https://github.com')
```

**Note:** While the `shell` module can be used in the renderer process, it will not function in a sandboxed renderer.

## Methods

The `shell` modul memiliki metode berikut:

### `shell.showItemInFolder (fullPath)`

* `fullPath` String

Show the given file in a file manager. If possible, select the file.

### `shell.openPath(path)`

* `path` String

Returns `Promise<String>` - Resolves with a string containing the error message corresponding to the failure if a failure occurred, otherwise "".

Buka file yang diberikan dengan cara default desktop.

### `shell.openExternal(url[, options])`

* `url` String - Max 2081 characters on windows.
* `options` Object (optional)
  * `activate` Boolean (optional) _macOS_ - `true` to bring the opened application to the foreground. The default is `true`.
  * `workingDirectory` String (optional) _Windows_ - The working directory.

Returns `Promise<void>`

Open the given external protocol URL in the desktop's default manner. (For example, mailto: URLs in the user's default mail agent).

### `shell.moveItemToTrash(fullPath[, deleteOnFail])` _Deprecated_

* `fullPath` String
* `deleteOnFail` Boolean (optional) - Whether or not to unilaterally remove the item if the Trash is disabled or unsupported on the volume. _macOS_

Returns `Boolean` - Whether the item was successfully moved to the trash or otherwise deleted.

> NOTE: This method is deprecated. Use `shell.trashItem` instead.

Pindahkan file yang diberikan ke sampah dan mengembalikan status boolean untuk pengoperasiannya.

### `shell.trashItem(path)`

* `path` String - path to the item to be moved to the trash.

Returns `Promise<void>` - Resolves when the operation has been completed. Rejects if there was an error while deleting the requested item.

This moves a path to the OS-specific trash location (Trash on macOS, Recycle Bin on Windows, and a desktop-environment-specific location on Linux).

### `Shell.beep()`

Bermain suara bip.

### `shell.writeShortcutLink (shortcutPath [, operasi], pilihan)` _Windows_

* `shortcutPath` String
* `operation` String (optional) - Default is `create`, can be one of following:
  * `buat` - membuat shortcut baru, Timpa jika diperlukan.
  * `update` - update ditentukan properti hanya pada tombol cepat yang ada.
  * `menggantikan` - menimpa tombol cepat yang ada, gagal jika tidak ada jalan pintas.
* `pilihan` [ShortcutDetails](structures/shortcut-details.md)

Kembali `Boolean` - Apakah cara pintas telah dibuat berhasil.

Membuat atau memperbarui tautan pintasan di `shortcutPath`.

### `shell.readShortcutLink(shortcutPath)` _Windows_

* `shortcutPath` String

Kembali [`ShortcutDetails`](structures/shortcut-details.md)

Menyelesaikan link pintasan di `shortcutPath`.

Pengecualian akan dilemparkan ketika terjadi kesalahan.