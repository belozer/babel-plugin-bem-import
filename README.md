# BEM import

BEM-entities auto resolver for custom import strings.

``` js
import Block from 'b:block';
import Block from 'b:block m:modName';
import Block from 'b:block m:modName=modVal1|modVal2';
import BlockElem from 'b:block e:elem';
import BlockElem from 'b:block e:elem m:modName';
import BlockElem from 'b:block e:elem m:modName=modVal1|modVal2';
```

## Install

> npm i -D babel-plugin-bem-import

## Usage

`.babelrc`

``` json
{
  "plugins": [
    ["bem-import", {
      "naming": "react",
      "levels": [
        "./common.blocks",
        "./desktop.blocks"
      ],
      "techs": ["js", "css"]
    }]
  ]
}
```

## Options

- __naming__: [bem-naming](https://en.bem.info/toolbox/sdk/bem-naming) overrides
- __levels__ <Array>: paths to components declarations
- __techs__ <Array>: list of techs extensions for require in runtime, `['js']` by default
- __techMap__ <Object>: mapping of techs to extensions. Example: `{ 'js' : ['react.js', 'react.ts', 'react.es'], 'css' : ['post.css'] }`
- __langs__ <Array>: list of langs in which resloves '.i18n' tech

## i18n

`.i18n` - represent special technology that provides the opportunity to localize components.

For correct working you need to install `bem-i18n` inside your project.

```
npm i -S bem-i18n
```

On file system:

```
blocks/Attach/
├── Attach.react.js
├── Attach.i18n
│   ├── en.js
│   ├── ru.js
│   └── tr.js
└── Attach.spec.js
```

`en.js`, `ru.js` and `tr.js` are keysets and should be common.js modules.

```sh
$ cat blocks/Attach/Attach.i18n/tr.js
module.exports = {
    "Attach": {
        "button-text": "Dosya seç",
        "no-file": "dosya seçilmedi"
    }
};
```

inside `Attach.react.js`:

```js
import i18n from `b:Attach t:i18n`

console.log(i18n('button-text')) // → Dosya seç
```

`babel-import` transpiles such code to

```js
var i18n = (function() {
    var core = require('bem-i18n');

    if (process.env.BEM_LANG === 'ru') {
        return core().decl(require('../Attach.i18n/ru'))('Attach')
    }

    if (process.env.BEM_LANG === 'en') {
        return core().decl(require('../Attach.i18n/en'))('Attach')
    }

    if (process.env.BEM_LANG === 'tr') {
        return core().decl(require('../Attach.i18n/tr'))('Attach')
    }
})();

console.log(i18n('button-text')) // → Dosya seç
```

`process.env.BEM_LANG` is need to be defined. `ru`, `en` and `tr` are taken from `langs` option.

### License MIT
