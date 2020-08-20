## Klasse: Tray

> Fügen Sie Symbole und Kontextmenüs in den Benachrichtigungsbereich des Systems hinzu.

Prozess: [Haupt](../glossary.md#main-process)

`Tray` ist ein [EventEmitter][event-emitter].

```javascript
const { app, Menu, Tray } = require('electron')

let tray = null
app.on('ready', () => {
  tray = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    { label: 'Item1', type: 'radio' },
    { label: 'Item2', type: 'radio' },
    { label: 'Item3', type: 'radio', checked: true },
    { label: 'Item4', type: 'radio' }
  ])
  tray.setToolTip('This is my application.')
  tray.setContextMenu(contextMenu)
})
```

__Plattform-Einschränkungen:__

* Unter Linux wird der App-Indikator verwendet, wenn er unterstützt wird, ansonsten wird `GtkStatusIcon` verwendet.
* On Linux distributions that only have app indicator support, you have to install `libappindicator1` to make the tray icon work.
* App-Indikator wird nur angezeigt, wenn es ein Kontextmenü hat.
* Wenn die App-Anzeige auf Linux verwendet wird, wird das `click` Ereignis ignoriert.
* On Linux in order for changes made to individual `MenuItem`s to take effect, you have to call `setContextMenu` again. Ein Beispiel:

```javascript
const { app, Menu, Tray } = require('electron')

let appIcon = null
app.on('ready', () => {
  appIcon = new Tray('/path/to/my/icon')
  const contextMenu = Menu.buildFromTemplate([
    { label: 'Item1', type: 'radio' },
    { label: 'Item2', type: 'radio' }
  ])

  // Make a change to the context menu
  contextMenu.items[1].checked = false

  // Call this again for Linux because we modified the context menu
  appIcon.setContextMenu(contextMenu)
})
```
* Unter Windows wird empfohlen, `ICO` Icons zu verwenden, um beste visuelle Effekte zu erhalten.

If you want to keep exact same behaviors on all platforms, you should not rely on the `click` event and always attach a context menu to the tray icon.


### `neue Tray(Bild)`

* `image` ([NativeImage](native-image.md) | String)

Creates a new tray icon associated with the `image`.

### Instanz-Ereignisse

Das `Tray` Modul sendet folgende Ereignisse aus:

#### Ereignis: 'click'

Rückgabewert:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `bounds` [Rechteck](structures/rectangle.md) - Die Grenzen des Tray-Symbols.
* `position` [Punkt](structures/point.md) - Die Position des Events.

Wird beim Klicken des Tray Icons gesendet.

#### Ereignis: 'right-click' _macOS_ _Windows_

Rückgabewert:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `bounds` [Rechteck](structures/rectangle.md) - Die Grenzen des Tray-Symbols.

Wird durch einen Rechts Klick auf das Tray Icon gesendet.

#### Ereignis: 'double-click' _macOS_ _Windows_

Rückgabewert:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `bounds` [Rechteck](structures/rectangle.md) - Die Grenzen des Tray-Symbols.

Emitted when the tray icon is double clicked.

#### Event: 'balloon-show' _Windows_

Emitted when the tray balloon shows.

#### Event: 'balloon-click' _Windows_

Emitted when the tray balloon is clicked.

#### Event: 'balloon-closed' _Windows_

Emitted when the tray balloon is closed because of timeout or user manually closes it.

#### Event: 'drop' _macOS_

Emitted when any dragged items are dropped on the tray icon.

#### Event: 'drop-files' _macOS_

Rückgabewert:

* ` Ereignis </ 0>  Ereignis</li>
<li><code>files` String[] - The paths of the dropped files.

Emitted when dragged files are dropped in the tray icon.

#### Event: 'drop-text' _macOS_

Rückgabewert:

* ` Ereignis </ 0>  Ereignis</li>
<li><code>text` String - the dropped text string.

Emitted when dragged text is dropped in the tray icon.

#### Event: 'drag-enter' _macOS_

Emitted when a drag operation enters the tray icon.

#### Event: 'drag-leave' _macOS_

Emitted when a drag operation exits the tray icon.

#### Event: 'drag-end' _macOS_

Emitted when a drag operation ends on the tray or ends at another location.

#### Event: 'mouse-enter' _macOS_

Rückgabewert:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Punkt](structures/point.md) - Die Position des Events.

Emitted when the mouse enters the tray icon.

#### Event: 'mouse-leave' _macOS_

Rückgabewert:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Punkt](structures/point.md) - Die Position des Events.

Emitted when the mouse exits the tray icon.

#### Event: 'mouse-move' _macOS_ _Windows_

Rückgabewert:

* `event` [KeyboardEvent](structures/keyboard-event.md)
* `position` [Punkt](structures/point.md) - Die Position des Events.

Emitted when the mouse moves in the tray icon.

### Instanz Methoden

The `Tray` class has the following methods:

#### `tray.destroy()`

Destroys the tray icon immediately.

#### `tray.setImage(image)`

* `image` ([NativeImage](native-image.md) | String)

Sets the `image` associated with this tray icon.

#### `tray.setPressedImage(image)` _macOS_

* `image` ([NativeImage](native-image.md) | String)

Sets the `image` associated with this tray icon when pressed on macOS.

#### `tray.setToolTip(toolTip)`

* `toolTip` String

Legt den Hover-Text für dieses Tray-Symbol fest.

#### `tray.setTitle(title)` _macOS_

* `title` String

Legt den Titel fest, der neben dem Tray Icon in der Statusleiste angezeigt wird (Unterstütz ANSI-Farben).

#### `tray.getTitle()` _macOS_

Returns `String` - the title displayed next to the tray icon in the status bar

#### `tray.setIgnoreDoubleClickEvents(ignore)` _macOS_

* `ignore` Boolean

Sets the option to ignore double click events. Ignoring these events allows you to detect every individual click of the tray icon.

This value is set to false by default.

#### `tray.getIgnoreDoubleClickEvents()` _macOS_

Returns `Boolean` - Whether double click events will be ignored.

#### `tray.displayBalloon(options)` _Windows_

* `options` Object
  * `icon` ([NativeImage](native-image.md) | String) (optional) -
  * `title` String
  * `content` String

Displays a tray balloon.

#### `tray.popUpContextMenu([menu, position])` _macOS_ _Windows_

* `menu` Menü (optional)
* `position` [Punkt](structures/point.md) (optional) - Die Pop-up-Position.

Pops up the context menu of the tray icon. When `menu` is passed, the `menu` will be shown instead of the tray icon's context menu.

Die `position` ist nur unter Windows verfügbar und ist standardmäßig (0, 0).

#### `tray.setContextMenu(menu)`

* `menu` Menu | null

Legt das Kontextmenü für dieses Symbol fest.

#### `tray.getBounds()` _macOS_ _Windows_

Returns [`Rectangle`](structures/rectangle.md)

The `bounds` of this tray icon as `Object`.

#### `tray.isDestroyed()`

Returns `Boolean` - Whether the tray icon is destroyed.

[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter