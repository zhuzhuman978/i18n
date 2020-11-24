## Клас: ClientRequest

> Направи искания HTTP/HTTPS.

Процеса: [Main](../glossary.md#main-process)

`ClientRequest` интерфейс [Writable поток](https://nodejs.org/api/stream.html#stream_writable_streams) и следователно е [EventEmitter][event-emitter].

### `Нова ClientRequest(options)`

* `options` (Object | String) - If `options` is a String, it is interpreted as the request URL. If it is an object, it is expected to fully specify an HTTP request via the following properties:
  * `method` String (optional) - The HTTP request method. Defaults to the GET method.
  * `url` String (optional) - The request URL. Must be provided in the absolute form with the protocol scheme specified as http or https.
  * `session` Session (optional) - The [`Session`](session.md) instance with which the request is associated.
  * `сесия` Обект (по избор) - екземплярът на [`сесията`](session.md), с който е свързана заявката. По подразбиране е празен низ. The `session` option supersedes `partition`. Следователно ако изрично е указано `сесия`, `дял` се игнорира.
  * `credentials` String (optional) - Can be `include` or `omit`. Whether to send [credentials](https://fetch.spec.whatwg.org/#credentials) with this request. If set to `include`, credentials from the session associated with the request will be used. If set to `omit`, credentials will not be sent with the request (and the `'login'` event will not be triggered in the event of a 401). This matches the behavior of the [fetch](https://fetch.spec.whatwg.org/#concept-request-credentials-mode) option of the same name. If this option is not specified, authentication data from the session will be sent, and cookies will not be sent (unless `useSessionCookies` is set).
  * `useSessionCookies` Boolean (optional) - Whether to send cookies with this request from the provided session. If `credentials` is specified, this option has no effect. Default is `false`.
  * `protocol` String (optional) - Can be `http:` or `https:`. The protocol scheme in the form 'scheme:'. Defaults to 'http:'.
  * `host` String (optional) - The server host provided as a concatenation of the hostname and the port number 'hostname:port'.
  * `име на хост` Низ (по избор) - името на хоста на сървъра.
  * `порт` Цяло число (по избор) - слушане номера на порта на сървъра.
  * `path` String (по избор) - Пътя на бисквитката.
  * `redirect` String (optional) - Can be `follow`, `error` or `manual`. The redirect mode for this request. When mode is `error`, any redirection will be aborted. When mode is `manual` the redirection will be cancelled unless [`request.followRedirect`](#requestfollowredirect) is invoked synchronously during the [`redirect`](#event-redirect) event.  Defaults to `follow`.
  * `origin` String (optional) - The origin URL of the request.

свойства на `Опции` като `протокол`, `хост`, `име на хост`, `пристанището` и `пътя` следват стриктно Node.js модела, както е описано в модула [URL](https://nodejs.org/api/url.html).

Например ние може да сме създали същото искане за "github.com" както следва:

```JavaScript
const request = net.request({
  method: 'GET',
  protocol: 'https:',
  hostname: 'github.com',
  port: 443,
  path: '/'
})
```

### Събития

#### Събитие: "отговор"

Връща:

* `отговор` IncomingMessage - обект, представляващ HTTP отговор съобщението.

#### Събитие: 'login'

Връща:

* `authInfo` Object
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function
  * `username` String (optional)
  * `password` String (optional)

Отделяни при автентичността прокси е asking за потребителски идентификационни данни.

Функцията `за обратно извикване` се очаква да се обади с потребителски идентификационни данни:

* `потребителско име` Низ
* `парола` Низ

```JavaScript
request.On ("вход", (authInfo, callback) => {обратно повикване ("потребителско име", "парола")})
```

Предоставяне на идентификационни данни на празна ще отмени искането и доклад грешка при удостоверяване на обект на отговор:

```JavaScript
request.on('response', (response) => {
  console.log(`STATUS: ${response.statusCode}`);
  response.on('error', (error) => {
    console.log(`ERROR: ${JSON.stringify(error)}`)
  })
})
request.on('login', (authInfo, callback) => {
  callback()
})
```

#### Събитие: "Готово"

Излъчва само след последното парче от `заявка` на данни е написано в `искането` предмет.

#### Събитие: "недоносче"

Emitted when the `request` is aborted. The `abort` event will not be fired if the `request` is already closed.

#### Събитие: 'error'

Връща:

* `error` Error - an error object providing some information about the failure.

Emitted when the `net` module fails to issue a network request. Typically when the `request` object emits an `error` event, a `close` event will subsequently follow and no response object will be provided.

#### Event: 'close'

Emitted as the last event in the HTTP request-response transaction. The `close` event indicates that no more events will be emitted on either the `request` or `response` objects.

#### Event: 'redirect'

Връща:

* `statusCode` Integer
* `method` String
* `redirectUrl` String
* `responseHeaders` Record<String, String[]>

Emitted when the server returns a redirect response (e.g. 301 Moved Permanently). Calling [`request.followRedirect`](#requestfollowredirect) will continue with the redirection.  If this event is handled, [`request.followRedirect`](#requestfollowredirect) must be called **synchronously**, otherwise the request will be cancelled.

### Инстантни свойства

#### `request.chunkedEncoding`

A `Boolean` specifying whether the request will use HTTP chunked transfer encoding or not. Defaults to false. The property is readable and writable, however it can be set only before the first write operation as the HTTP headers are not yet put on the wire. Trying to set the `chunkedEncoding` property after the first write will throw an error.

Using chunked encoding is strongly recommended if you need to send a large request body as data will be streamed in small chunks instead of being internally buffered inside Electron process memory.

### Инстантни методи

#### `request.setHeader(name, value)`

* `name` String - An extra HTTP header name.
* `value` String - An extra HTTP header value.

Adds an extra HTTP header. The header name will be issued as-is without lowercasing. It can be called only before first write. Calling this method after the first write will throw an error. If the passed value is not a `String`, its `toString()` method will be called to obtain the final value.

Certain headers are restricted from being set by apps. These headers are listed below. More information on restricted headers can be found in [Chromium's header utils](https://source.chromium.org/chromium/chromium/src/+/master:services/network/public/cpp/header_util.cc;drc=1562cab3f1eda927938f8f4a5a91991fefde66d3;bpv=1;bpt=1;l=22).

* `Content-Length`
* `Host`
* `Trailer` or `Te`
* `Upgrade`
* `Cookie2`
* `Keep-Alive`
* `Transfer-Encoding`

Additionally, setting the `Connection` header to the value `upgrade` is also disallowed.

#### `request.getHeader(name)`

* `name` String - Specify an extra header name.

Returns `String` - The value of a previously set extra header name.

#### `request.removeHeader(name)`

* `name` String - Specify an extra header name.

Removes a previously set extra header name. This method can be called only before first write. Trying to call it after the first write will throw an error.

#### `request.write(chunk[, encoding][, callback])`

* `chunk` (String | Buffer) - A chunk of the request body's data. If it is a string, it is converted into a Buffer using the specified encoding.
* `encoding` String (optional) - Used to convert string chunks into Buffer objects. Defaults to 'utf-8'.
* `callback` Function (optional) - Called after the write operation ends.

`callback` is essentially a dummy function introduced in the purpose of keeping similarity with the Node.js API. It is called asynchronously in the next tick after `chunk` content have been delivered to the Chromium networking layer. Противно на Node.js изпълнението не е гарантирано, че `парче` съдържание са изпразнен на тел преди `обратно повикване` се нарича.

Добавя парче на данни към органа по заявка. Първата операция на запис може да причини на искането заглавията да бъдат издадени на тел. След първата пиша операция, не е позволено да добавите или премахнете потребителски горен колонтитул.

#### `request.End ([chunk][, кодиране] [, обратно повикване])`

* `chunk` (String | Buffer) (optional)
* `encoding` String (optional)
* `callback` Function (optional)

Sends the last chunk of the request data. Subsequent write or end operations will not be allowed. The `finish` event is emitted just after the end operation.

#### `request.abort()`

Cancels an ongoing HTTP transaction. If the request has already emitted the `close` event, the abort operation will have no effect. Otherwise an ongoing event will emit `abort` and `close` events. Additionally, if there is an ongoing response object,it will emit the `aborted` event.

#### `request.followRedirect()`

Continues any pending redirection. Can only be called during a `'redirect'` event.

#### `request.getUploadProgress()`

Връща `Object`:

* `active` Boolean - Whether the request is currently active. If this is false no other properties will be set
* `started` Boolean - Whether the upload has started. If this is false both `current` and `total` will be set to 0.
* `current` Integer - The number of bytes that have been uploaded so far
* `total` Integer - The number of bytes that will be uploaded this request

You can use this method in conjunction with `POST` requests to get the progress of a file upload or other data transfer.

[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter