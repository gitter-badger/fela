# Fela API

* [FontFace(family, files [, properties])](#fontfacefamily-files--properties)
* [Keyframe(composer)](#keyframecomposer)
* [Renderer(node)](#renderernode)
  * [.render(element [, props, plugins])](#renderelement--props-plugins)
  * [.clear()](#clear)
* [applyMiddleware(renderer, middleware)](#applymiddlewarerenderer-middleware)


## `FontFace(f>amily, files [, properties])`
**string\<family>**<br>
**string[]\<files>**<br>
**Object?\<properties>**

Instantiates a new FontFace referencing a font `family` with a set of source `files` which are passed as relative paths. Optionally adds font-face specific style properties.

Valid properties are:
* `fontWeight`
* `fontStretch`
* `fontStyle`
* `unicodeRange`

```javascript
const files = [
  '../fonts/Arial.ttf',
  '../fonts/Arial.woff'
]

const fontFace = new Fela.FontFace('Arial', files, { fontWeight: 300 })
```

## `Keyframe(composer)`
**Function\<composer>**

Instantiates a new Keyframe with a pure keyframe *composer*. It is used similar to basic Selectors.

```javascript
const composer = props => ({
  '0%': {
    color: 'red'
  },
  '50%': {
    color: 'blue'
  },
  '75%': {
    color: 'yellow'
  }
})

const keyframe = new Fela.Keyframe(composer)
```


## `Renderer(node)`
**HTMLElement\<node>**<br>

Instantiates a new DOM Renderer and binds itself to a valid DOM `node`.

### `render(element [, props, plugins])`
**Function|Keyframe|FontFace|Object|string\<element>**<br>
**Object?\<props>**<br>
**Function[]?\<plugins>**

A universal method to render either selector & Keyframe variations, FontFaces or static styles. Optionally processes the variation with a set of  `plugins`. <br>
Automatically updates the associated DOM node if the overall CSS markup changed.<br>
Returns the mounted *className*, *animationName* or *fontFamily* reference.
```javascript
const node = document.getElementById('style-element')
const renderer = new Fela.Renderer(node)

const selector = props => ({ color: props.color })

renderer.render(selector, { color: 'red' }) // => c0-se22d
renderer.render(selector, { color: 'blue' }) // => c0-ee414
```

### `clear()`

Clears the associated DOM node and all cached Selector variations.
```javascript
const node = document.getElementById('style-element')
const renderer = new Fela.Renderer(node)

const selector = props => ({ color: props.color }))

renderer.render(selector, { color: 'red' }) // => c0-se22d
renderer.render(selector, { color: 'blue' }) // => c0-ee414

// node.textContent === .c0-se22d{color:red}.c0-ee414{color:blue}

renderer.clear()
// node.textContent === ''
```

## `applyMiddleware(renderer, middleware)`
**Renderer\<renderer>**<br>
**Function[]?\<middleware>**

Helper to apply `middleware` to a Renderer instance.

```javascript
const renderer = new Fela.Renderer(node)

// example renderer that logs a text
// on every render call
const logger = options => {
  return renderer => {
    const existingRender = renderer.render.bind(renderer)
    renderer.render = (selector, props, plugins) => {
      console.log("Render has been called!")
      return existingRender(selector, props, plugins)
    }
  }
}

const enhancedRenderer = Fela.applyMiddleware(renderer, [ logger() ])
```
