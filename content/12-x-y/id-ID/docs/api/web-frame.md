# webBingkai

> Sesuaikan render halaman web saat ini.

Proses: [Renderer](../glossary.md#renderer-process)

`webFrame` export of the Electron module is an instance of the `WebFrame` class representing the top frame of the current `BrowserWindow`. Sub-frames can be retrieved by certain properties and methods (e.g. `webFrame.firstChild`).

Contoh dari halaman saat ini pembesaran 200%.

```javascript
const { webFrame } = require('electron')

webFrame.setZoomFactor(2)
```

## Methods

The `WebFrame` class has the following instance methods:

### `webBingkai.tetapkanFaktorZoom(faktor)`

* `factor` Double - Zoom factor; default is 1.0.

Changes the zoom factor to the specified factor. Zoom factor is zoom percent divided by 100, so 300% = 3.0.

The factor must be greater than 0.0.

### `webBingkai.tetapkanFaktorZoom()`

Kembali `nomor` - faktor zoom saat ini.

### `webBingkai.tetapkanFaktorZoom(level)`

* `level` Angka - level zoom.

Mengubah tingkat zoom ke tingkat tertentu. Ukuran aslinya adalah 0 dan masing-masing Peningkatan atas atau di bawah mewakili zoom 20% lebih besar atau lebih kecil ke default batas 300% dan 50% dari ukuran aslinya, berurutan.

> **NOTE**: The zoom policy at the Chromium level is same-origin, meaning that the zoom level for a specific domain propagates across all instances of windows with the same domain. Differentiating the window URLs will make zoom work per-window.

### `webBingkai.dapatkanLevelZoom()`

Kembali `nomor` - tingkat zoom saat ini.

### `webBingkai.setBatasLevelVisualZoom(minimalLevel, maksimalLevel)`

* `minimalLevel` Nomor
* `maksimalLevel` Nomor

Menetapkan maksimum dan minimum tingkat mencubit-to-zoom.

> **NOTE**: Visual zoom is disabled by default in Electron. To re-enable it, call:
> 
> ```js
webFrame.setVisualZoomLevelLimits(1, 3)
```

### `webFrame.setSpellCheckProvider(language, provider)`

* `bahasa` String
* `provider` Object
  * `spellCheck` Function
    * `words` String[]
    * `callback ` Fungsi
      * `misspeltWords` String[]

Menetapkan penyedia pemeriksaan ejaan di bidang masukan dan area teks.

If you want to use this method you must disable the builtin spellchecker when you construct the window.

```js
const mainWindow = new BrowserWindow({
  webPreferences: {
    spellcheck: false
  }
})
```

The `provider` must be an object that has a `spellCheck` method that accepts an array of individual words for spellchecking. The `spellCheck` function runs asynchronously and calls the `callback` function with an array of misspelt words when complete.

Contoh menggunakan [node-cekEjaan][spellchecker] sebagai penyedia:

```javascript
const { webFrame } = require('electron')
const spellChecker = require('spellchecker')
webFrame.setSpellCheckProvider('en-US', {
  spellCheck (words, callback) {
    setTimeout(() => {
      const spellchecker = require('spellchecker')
      const misspelled = words.filter(x => spellchecker.isMisspelled(x))
      callback(misspelled)
    }, 0)
  }
})
```

### `webFrame.insertCSS(css)`

* `css` String - CSS source code.

Returns `String` - A key for the inserted CSS that can later be used to remove the CSS via `webFrame.removeInsertedCSS(key)`.

Injects CSS into the current web page and returns a unique key for the inserted stylesheet.

### `webFrame.removeInsertedCSS(key)`

* `kunci` senar

Removes the inserted CSS from the current web page. The stylesheet is identified by its key, which is returned from `webFrame.insertCSS(css)`.

### `webFrame.insertText(text)`

* `teks` String

Sisipan `teks` ke elemen yang terfokus.

### `webFrame.executeJavaScript(code[, userGesture, callback])`

* `code` String
* `userGesture` Boolean (opsional) - Default adalah `false`.
* `callback` Function (optional) - Called after script has been executed. Unless the frame is suspended (e.g. showing a modal alert), execution will be synchronous and the callback will be invoked before the method returns. For compatibility with an older version of this method, the error parameter is second.
  * `hasil` Ada
  * ` error </ 0> Kesalahan</li>
</ul></li>
</ul>

<p spaces-before="0">Returns <code>Promise<any>` - A promise that resolves with the result of the executed code or is rejected if execution throws or results in a rejected promise.</p>

Evaluasi `kode` di halaman.

Di jendela browser beberapa API HTML seperti `requestFullScreen` hanya bisa dipanggil oleh isyarat dari pengguna. Setting `userGesture` ke `true` akan dihapus keterbatasan ini.

### `webFrame.executeJavaScriptInIsolatedWorld(worldId, scripts[, userGesture, callback])`

* `worldId` Integer - The ID of the world to run the javascript in, `0` is the default main world (where content runs), `999` is the world used by Electron's `contextIsolation` feature. Accepts values in the range 1..536870911.
* `scripts` [WebSource[]](structures/web-source.md)
* `userGesture` Boolean (opsional) - Default adalah `false`.
* `callback` Function (optional) - Called after script has been executed. Unless the frame is suspended (e.g. showing a modal alert), execution will be synchronous and the callback will be invoked before the method returns.  For compatibility with an older version of this method, the error parameter is second.
  * `hasil` Ada
  * ` error </ 0> Kesalahan</li>
</ul></li>
</ul>

<p spaces-before="0">Returns <code>Promise<any>` - A promise that resolves with the result of the executed code or is rejected if execution could not start.</p>

Works like `executeJavaScript` but evaluates `scripts` in an isolated context.

Note that when the execution of script fails, the returned promise will not reject and the `result` would be `undefined`. This is because Chromium does not dispatch errors of isolated worlds to foreign worlds.

### `webFrame.setIsolatedWorldInfo(worldId, info)`

* `worldId` Integer - The ID of the world to run the javascript in, `0` is the default world, `999` is the world used by Electrons `contextIsolation` feature. Chrome extensions reserve the range of IDs in `[1 << 20, 1 << 29)`. You can provide any integer here.
* `info` Object
  * `securityOrigin` String (optional) - Security origin for the isolated world.
  * `csp` String (optional) - Content Security Policy for the isolated world.
  * `name` String (optional) - Name for isolated world. Useful in devtools.

Set the security origin, content security policy and name of the isolated world. Note: If the `csp` is specified, then the `securityOrigin` also has to be specified.

### `webFrame.getResourceUsage()`

Mengembalikan `Objek`:

* `gambar` [DetailPemakaianMemori](structures/memory-usage-details.md)
* `scripts` [MemoryUsageDetails](structures/memory-usage-details.md)
* `cssStyleSheets` [DetailPemakaianMemori](structures/memory-usage-details.md)
* `xslStyleSheets` [DetailPemakaianMemori](structures/memory-usage-details.md)
* `Huruf` [DetailPemakaianMemori](structures/memory-usage-details.md)
* `lain` [DetailPemakaianMemori](structures/memory-usage-details.md)

Mengembalikan objek yang menjelaskan informasi penggunaan memori internal Blink cache.

```javascript
const { webFrame } = require('electron')
console.log(webFrame.getResourceUsage())
```

Ini akan menghasilkan:

```javascript
{
  images: {
    count: 22,
    size: 2549,
    liveSize: 2542
  },
  cssStyleSheets: { /* same with "images" */ },
  xslStyleSheets: { /* same with "images" */ },
  fonts: { /* same with "images" */ },
  other: { /* same with "images" */ }
}
```

### `webFrame.clearCache()`

Upaya untuk membebaskan memori yang tidak lagi digunakan (seperti gambar dari a navigasi sebelumnya).

Perhatikan bahwa secara membabi buta memanggil metode ini mungkin membuat Electron lebih lambat sejak itu harus mengisi ulang cache yang dikosongkan ini, sebaiknya Anda hanya menelponnya jika sebuah acara di aplikasi Anda telah terjadi yang membuat Anda menganggap halaman Anda benar-benar menggunakan lebih sedikit memori (yaitu Anda telah menavigasi dari halaman super berat ke yang kebanyakan kosong, dan berniat untuk tinggal di sana).

### `webFrame.getFrameForSelector(selector)`

* `selector` String - CSS selector for a frame element.

Returns `WebFrame` - The frame element in `webFrame's` document selected by `selector`, `null` would be returned if `selector` does not select a frame or if the frame is not in the current renderer process.

### `webFrame.findFrameByName(name)`

* ` nama </ 0>  Deretan</li>
</ul>

<p spaces-before="0">Returns <code>WebFrame` - A child of `webFrame` with the supplied `name`, `null` would be returned if there's no such frame or if the frame is not in the current renderer process.</p>

### `webFrame.findFrameByRoutingId(routingId)`

* `routingId` Integer - An `Integer` representing the unique frame id in the current renderer process. Routing IDs can be retrieved from `WebFrame` instances (`webFrame.routingId`) and are also passed by frame specific `WebContents` navigation events (e.g. `did-frame-navigate`)

Returns `WebFrame` - that has the supplied `routingId`, `null` if not found.

### `webFrame.isWordMisspelled(word)`

* `word` String - The word to be spellchecked.

Returns `Boolean` - True if the word is misspelled according to the built in spellchecker, false otherwise. If no dictionary is loaded, always return false.

### `webFrame.getWordSuggestions(word)`

* `word` String - The misspelled word.

Returns `String[]` - A list of suggested words for a given word. If the word is spelled correctly, the result will be empty.

## Properti/peralatan

### `webFrame.top` _Readonly_

A `WebFrame | null` representing top frame in frame hierarchy to which `webFrame` belongs, the property would be `null` if top frame is not in the current renderer process.

### `webFrame.opener` _Readonly_

A `WebFrame | null` representing the frame which opened `webFrame`, the property would be `null` if there's no opener or opener is not in the current renderer process.

### `webFrame.parent` _Readonly_

A `WebFrame | null` representing parent frame of `webFrame`, the property would be `null` if `webFrame` is top or parent is not in the current renderer process.

### `webFrame.firstChild` _Readonly_

A `WebFrame | null` representing the first child frame of `webFrame`, the property would be `null` if `webFrame` has no children or if first child is not in the current renderer process.

### `webFrame.nextSibling` _Readonly_

A `WebFrame | null` representing next sibling frame, the property would be `null` if `webFrame` is the last frame in its parent or if the next sibling is not in the current renderer process.

### `webFrame.routingId` _Readonly_

An `Integer` representing the unique frame id in the current renderer process. Distinct WebFrame instances that refer to the same underlying frame will have the same `routingId`.

[spellchecker]: https://github.com/atom/node-spellchecker