revealjs-embed-code
===================

This repository contains a small and simple js plugin for embedding code files into revealjs presentations, without the need to copy and paste their contents into the presentation file.

The idea and basic design is based off of [a repository](https://github.com/dafrenchyman/reveal.js-extrernal-code) from `dafrenchyman`.

A somewhat similar, but still different approach, can be found in [this repository](https://github.com/ldionne/reveal-sampler).

## Features
- include code from files without copy-pasting it into your presentation
```html
<code data-url="link-to-code-file"></code>
```
- specify line numbers to limit the range of the file to be included
```html
<code data-url="link-to-code-file" data-line-start="2" data-line-end="10"></code>
```
- automatically remove a common indentation in every line (especially useful for highly indented lines in the middle of code files)
```html
<!-- in your presentation in the browser -->
<div>
    <p>This is a test</p>
</div>
```

instead of

```html
<!-- your code file -->
            <div>
                <p>This is a test</p>
            </div>
```
- use delimiters instead of fix line numbers to allow for dynamic code listings, in case the code file changes
```html
<!-- your presentation -->
<code data-url="test.html" data-line-start-delimiter="listing-1" data-line-end-delimiter="listing-1-end"></code>
```

```html
<!-- your code file: test.html -->
<p>Here is some HTML</p>

<!-- listing-1 -->
<p>This HTML should be listed!</p>
<p>And this too!</p>
<!-- listing-1-end -->

<p>But this not anymore</p>
```

```html
<!-- in your presentation in the browser -->
<code>
    <p>This HTML should be listed!</p>
    <p>And this too!</p>
</code>
```

## Installation
### Prerequisites
This plugin requires the presentation to be served via a webserver, for more information see [Why is a web server required?](#why-is-a-web-server-required).

In case you need a local webserver just for presenting or working on the presentation, I recommend steps at [How to install a local web server?](#how-to-install-a-local-web-server).

### Copy plugin
To get started, first copy the `embed-code.js` file into the `plugin` folder of your revealjs presentation.

### Link into presentation
Then, link the js file into your main html file before your script section that calls `Reveal.initialize()`, e.g. like this

```html
<script src="plugin/embed-code.js"></script>
```

Now, tell revealjs to load the plugin

```javascript
plugins: [
    EmbedCode,
    RevealMarkdown,
    RevealHighlight,
],
```

In case you also use e.g. `RevealHighlight`, make sure to load the `EmbedCode` plugin first, as the code content must be embedded first before it can be used by other plugins.

That's it! Now you can use the plugin in your presentation.

## Usage
To use this plugin, you just have to specify the `data-url` attribute with a url to the desired text file in a `code` tag in your presentation.

```html
<code data-url="plugin/revealjs-external-code/external-code.js"></code>
```

This includes the whole file into the code tag.

There are more parameters to specify which range of the file should be included.

|Attribute|Value|Description|
|-|-|-|
|`data-url`|URL|specifies that this code has dynamically loaded content and the URL at which it can be found|
|`data-line-start`|`number`|the number of the first line to be included|
|`data-line-end`|`number`|the number of the last line to be included|
|`data-line-start-delimiter`|`string`|a string that can be used to dynamically specify the first line to be included. The plugin searches each line for an occurrence of this string and uses the first one it finds|
|`data-line-end-delimiter`|`string`|a string that can be used to dynamically specify the last line to be included. The plugin searches each line for an occurrence of this string and uses the first one it finds|
|`data-no-beautify`|_none_|specifies whether the included code should be beautified, i.e. remove a common indentation in all lines|
|`data-include-delimiters`|_none_|specifies whether the lines where the delimiters were found should be included or not|

## Why is a web server required?
Unfortunately, a web server is required to load these external code files into your presentation.

This is due to the mitigation of several security vulnerabilities.
Basically, each locally server file opened by your browser is its own 'domain' and therefore no other file can be loaded by js without triggering a CORS error.
This cannot be circumvented, also not by using `<iframe>` or `<object>` tags.

More information can be found [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors/CORSRequestNotHttp).

## How to install a local web server?
The easiest way to get a local webserver up and running is likely via the `python` module `http.server` (in `python2` it's `HttpServer`).
As it is bundled with `python` by default, it can simply be started with

```
python -m http.server
```

That's it!
If you want to use the comfort of auto-reloading, I'd recommend the usage of the `npm` package `live-server`, which is explained in the following part.

<details>
<summary>Local Webserver with Live Reload</summary>

Install [`nodejs`](https://nodejs.org/en/download/) and then initialize a new package with

```sh
npm init -y
```

This creates a `package.json` file in your current directory, which you can basically ignore.

Now, install and start a local webserver

```sh
npm install live-server
node node_modules/live-server/live-server.js
```

Assuming that you presentation file lies in you current working directory and has the filename `index.html`, it should now being opened in your web browser at the address http://127.0.0.1:8080/.

After making changes and saving them into your presentation file, the website should reload automatically.

For more information take a look [here](https://www.npmjs.com/package/live-server).
</details>
