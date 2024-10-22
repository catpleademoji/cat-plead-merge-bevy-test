# Instructions

## Dependencies

Ensure the `wasm-bindgen` is installed 

```sh
cargo install wasm-bindgen-cli
```

and that `rustup` is targeting `wasm32-unknown-unknown`.

```sh
rustup target add wasm32-unknown-unknown
```

## Building

First build the project targeting `wasm32-unknown-unknown`.

Then use `wasm-bindgen` to create the js bindings.

```sh
cargo build --target wasm32-unknown-unknown
wasm-bindgen --out-dir ./dist --target web ./target/wasm32-unknown-unknown/debug/cat-plead-merge.wasm
```

## Release mode
```sh
cargo build --release --target wasm32-unknown-unknown
wasm-bindgen --out-dir ./dist --target web ./target/wasm32-unknown-unknown/release/cat-plead-merge.wasm
```

# Publishing

Create an `index.html` along with your game `.js` file.

A minimal html file to serve the js.

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta title="cat plead merge">
</head>

<body>

</body>
<script type="module">
    import init from "./cat-plead-merge.js"
    import "./restart-audio-context.js"

    init().catch((error) => {
        if (!error.message.startsWith("Using exceptions for control flow, don't mind me. This isn't actually an error!")) {
            throw error;
        }
    });
</script>

</html>
```

We use `restart-audio-context.js` to make sure the audio contexts plays.

```js
// taken from https://developer.chrome.com/blog/web-audio-autoplay/#moving-forward
(function () {
    // An array of all contexts to resume on the page
    const audioContextList = [];

    // An array of various user interaction events we should listen for
    const userInputEventNames = [
        'click',
        'contextmenu',
        'auxclick',
        'dblclick',
        'mousedown',
        'mouseup',
        'pointerup',
        'touchend',
        'keydown',
        'keyup',
    ];

    // A proxy object to intercept AudioContexts and
    // add them to the array for tracking and resuming later
    self.AudioContext = new Proxy(self.AudioContext, {
        construct(target, args) {
            const result = new target(...args);
            audioContextList.push(result);
            return result;
        },
    });

    // To resume all AudioContexts being tracked
    function resumeAllContexts(event) {
        let count = 0;

        audioContextList.forEach(context => {
            if (context.state !== 'running') {
                context.resume();
            } else {
                count++;
            }
        });

        // If all the AudioContexts have now resumed then we
        // unbind all the event listeners from the page to prevent
        // unnecessary resume attempts
        if (count == audioContextList.length) {
            userInputEventNames.forEach(eventName => {
                document.removeEventListener(eventName, resumeAllContexts);
            });
        }
    }

    // We bind the resume function for each user interaction
    // event on the page
    userInputEventNames.forEach(eventName => {
        document.addEventListener(eventName, resumeAllContexts);
    });
})();
```

# Issues 

## Asset loading

If you run into errors related to `.meta` files not being found, make sure to change this line in your `main.rs`. 


```diff
-   .add_plugins(DefaultPlugins)
+   .add_plugins(DefaultPlugins.set(AssetPlugin {
+       meta_check: bevy::asset::AssetMetaCheck::Never,
+       ..default()
+   }))
```
