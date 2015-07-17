# `fontgen-loader`: Bam, easy webfonts!

Have you faced this? You have 4 icons from FontAwesome, and 19 from Glyphicons, and maybe you are eyeing another webfont's icons and wishing to use them?

What a mess! Okay, so what do we do? We make our own. How? Good question. In fact, this question comes up just so often... that I decided to write a little thing to help out.

## How `fontgen-loader` Works

This is a tool that generates fonts automatically through Webpack. The font is created by putting several SVG icons together (according to a configuration) and generating the proper file(s):

- Font files in WOF, EOT, TTF and WOFF2 formats. (You can also output an SVG font, if you want. But there is a [trend of removal within browsers](http://caniuse.com/#feat=svg-fonts)...)
- A CSS file with your font definition, including a proper `@font-face` declaration and icon classes.
- An HTML demo page, if desired.

In order to use this loader, you need to be aware that this is a "trigger loader". That means, it cannot just be added to your `webpack.config.js` like any other loader, you need to be aware of what it does in the long run.

## Configuration

```javascript
module.exports = {
    resolve: {
        loaders: [
            {
                test: /\.font.(js|json)$/,
                loader: "style?css?fontgen?formats=woff,eot,ttf"
            }
        ]
    }
}
```

This loader returns CSS. Therefore, you have to pipe it through the proper loaders as shown. You should be able to use this with the `extract-text-plugin` as well.

Other configurations are possible. You can specify a custom template to use, to return different kinds of source. A LESS or SCSS version, for instance? Up to you.

## Usage

Now that we have the loader configured, it's about time we give this a go. First, you want to load your font like so, within your entry code:

```javascript
// main.js
require("./Awesomecons.font.json"); // .js or .json are both valid if you used the config above
```

Now the loader will load in the font from the configuration file(`Awesomecons.font.json`). Here's an example configuration:

```json
{
    "files": [
        "icon/my.svg",
        "icon/awesome.svg",
        "icon/stuff.svg"
    ],
    "fontName": "Awesomecons",
    "classPrefix": "ai-",
    "baseClass": "ai",
    "fixedWidth": true
}
```

(I am using JSON in this example, since I know that my code is more static. JS is allowed as well if you need to do something more dynamic. Make sure the configuration ends up being the contents of `module.exports`.)

Now the loader will pick up this config, pull it through the generator and:

- Generate CSS with the base and class prefix.
- Output font files for the SVG icons listed under `files`.

And there you are; your webfont is done.!

You also can use a module like `glob` to pick up a variable set of icons, too. Mix and match and mind the various licenses... and make your own webfont!

# Configuration

## Loader parameters

- `formats`, Array<br>
Possible values are: `["svg", "eot", "wof", "ttf"]`.

- `template`, String<br>
Which template to use? By default, a CSS one is used. The template is to be processed by Handlebars. See [the generator](https://github.com/nfroidure/svgicons2svgfont)'s readme itself for more info.

## Font configuration (`*.font.js` or `*.font.json`)

- `files`, Array<br>
An array of SVG icon files.

- `fontName`, String<br>
Name of your font.

- `classPrefix`, String<br>
The prefix to be used with each icon class.

- `baseClass`, String<br>
The base class, under which each icon class is to be crated.

- `.rename`, Function<br>
A function that returns the icon's name based on the input (filename).

Any additional options are passed along to `webfonts-generator`. See the [webfonts-generator README](https://www.npmjs.com/package/webfonts-generator) for information on additional options.

### Special configuration
There is one special configuration optin that exists in both the actual font configuration and as a query parameter: `fileName`. This one decides the output of the font filenames. You can create a filename template with these elements (will likely become more in the future):

- `[fontname]`: The name of the font. I.e. "Awesomefont".
- `[ext]`: The extension. I.e.: `.woff`.
- `[hash]`: The hash of your current compilation.

For example: `[fontname]-[hash].[ext]`


