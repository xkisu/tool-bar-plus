# Atom Tool Bar+

This package in an enhansed version of the tool-bar atom package which provides an extendable tool bar for Atom.

**Note:** Like the original tool-bar package, this package by itself adds an empty toolbar. To propagate it with
content you can [install plugins](#plugins).

## Configuration

### Position

On which edge of the editor should the tool bar appear. Possible options:

*   Top
*   Right
*   Bottom
*   Left

### Icon size

*   Very Small *(12px)*
*   Small *(16px)*
*   Big *(24px)*
*   Large *(32px)*

### Visibility

*   Visible
*   Hidden

### Full Width (available in Atom >= 1.7)

When on top/bottom, expand to full window width.

### Use TouchBar

If your computer is equipped with a TouchBar (only Apple MacBook Pro series
currently) it can display up to seven tool bar buttons there.

## Plugins

All the plugins for tool-bar can be used with tool-bar-plus. See a summary of the Atom packages that have the keyword [tool-bar](https://atom.io/packages/search?utf8=%E2%9C%93&q=keyword%3Atool-bar).

## Packages using tool-bar

*   [Particle Dev](https://atom.io/packages/spark-dev)
*   [Facebook Nuclide](https://atom.io/packages/nuclide)
*   [PlatformIO IDE](https://atom.io/packages/platformio-ide)
*   [Organized](https://atom.io/packages/organized)

## Integrating instructions

By itself this package shows an empty tool bar. To add buttons and spacers to
the tool bar, follow the instructions below.

### Package.json

Make sure the following properties are part of your `package.json`.

```json
"consumedServices": {
  "tool-bar": {
    "versions": {
      "^0 || ^1": "consumeToolBar"
    }
  }
}
```

We recommend using [Atom-Package-Deps](https://github.com/steelbrain/package-deps)
in your package for installing dependency packages like this package.

### Main package file

In your main package file, add the following methods and replace
`your-package-name` with your package name.

```js
let toolBar;

export function consumeToolBar(getToolBar) {
  toolBar = getToolBar('your-package-name');
  // Add buttons and spacers here...
}

export function deactivate() {
  if (toolBar) {
    toolBar.removeItems();
    toolBar = null;
  }
}
```

### Example

```js
let toolBar;

export function consumeToolBar(getToolBar) {
  toolBar = getToolBar('your-package-name');

  // Adding button
  toolBar.addButton({
    icon: 'octoface',
    callback: 'application:about',
    tooltip: 'About Atom'
  });

  // Adding spacer
  toolBar.addSpacer();

  // Using custom icon set (Ionicons)
  const button = toolBar.addButton({
    icon: 'gear-a',
    callback: 'application:show-settings',
    tooltip: 'Show Settings',
    iconset: 'ion'
  });

  // Disable button
  button.setEnabled(false);

  // Function with data in callback
  toolBar.addButton({
    icon: 'alert',
    callback(data) {
      alert(data);
    },
    tooltip: 'Show Alert',
    data: 'foo'
  });

  // Callback with modifiers
  toolBar.addButton({
    icon: 'octoface',
    callback: {
      '': 'application:cmd-1',      // Without modifiers is default action
      'alt': 'application:cmd-2',
      'ctrl': 'application:cmd-3',  // With function callback
      'shift'(data) {
        console.log(data);
      },
      'alt+shift': 'application:cmd-5',       // Multiple modifiers
      'alt+ctrl+shift': 'application:cmd-6'   // All modifiers
    },
    data: 'foo'
  });

  // Calling multiple callbacks at once
  toolBar.addButton({
    icon: 'octoface',
    callback: ['application:cmd-1', 'application:cmd-2']
  });  

  // Adding spacer and button at the beginning of the tool bar
  toolBar.addSpacer({priority: 10});
  toolBar.addButton({
    icon: 'octoface',
    callback: 'application:about',
    priority: 10
  });

  // Adding text button
  toolBar.addButton({
    text: 'hello',
    callback: 'application:about'
  });

  // Text buttons can also have an icon
  toolBar.addButton({
    icon: 'octoface',
    text: 'hello',
    callback: 'application:about'
  });

  // Text buttons can be html
  toolBar.addButton({
    icon: 'octoface',
    text: '<b>BIG</b> button',
    html: true,
    callback: 'application:about'
  });

  // Cleaning up when tool bar is deactivated
  toolBar.onDidDestroy(() => {
    this.toolBar = null;
    // Teardown any stateful code that depends on tool bar ...
  });
}
```

## Methods

### `.addButton({icon, iconset, text, html, callback, priority, tooltip, data})`

The method `addButton` requires an object with at least the property `callback`. The
`callback` must be an Atom command string, a custom callback function or an
object where the keys are key modifiers (`alt`, `ctrl` or `shift`) and the
values are commands or custom functions (see [example](#example)).

The remaining properties
`tooltip` (default there is no tooltip),
`text` (default there is no text),
`html` (default `false`),
`icon` (default there is no icon),
`iconset` (defaults to `Octicons`),
`data`, and
`priority` (defaults `50`)
are optional.

The `tooltip` option may be a `string` or an `object` that is passed to Atom's
[TooltipManager](https://atom.io/docs/api/latest/TooltipManager#instance-add)

The return value of this method shares another method called
`setEnabled(enabled)` to enable or disable the button.

### `.addSpacer({priority})`

The method `addSpacer` has only one optional property `priority` (defaults
`50`).

### `.removeItems()`

Use the method `removeItems` to remove the buttons added by your package. This
is particular useful in your package `deactivate` method, but can be used at
any time.

### `.onDidDestroy(callback)`

The `onDidDestroy` method takes a function that will be called when the
`tool-bar` package is destroyed. This is useful if your package needs to do
cleanup when the `tool-bar` is deactivated but your package continues running.

## Supported icon sets

*   [Octicons](https://octicons.github.com) (Atom's flavour)
*   [Ionicons](http://ionicons.com) (`ion`)
*   [FontAwesome](http://fortawesome.github.io/Font-Awesome) (`fa`)
*   [Foundation](http://zurb.com/playground/foundation-icon-fonts-3) (`fi`)
*   [IcoMoon](https://icomoon.io) (`icomoon`)
*   [Devicon](http://konpa.github.io/devicon/) (`devicon`)
*   [MaterialDesignIcons](https://materialdesignicons.com/) (`mdi`)

## Supported commands

*   `tool-bar:toggle`
*   `tool-bar:position-top`
*   `tool-bar:position-right`
*   `tool-bar:position-bottom`
*   `tool-bar:position-left`

## Authors

*   [Wojtek Siudzinski](http://suda.pl) <sup>(owner)</sup>
*   [Jeroen van Warmerdam](https://github.com/jerone)
*   [Ryo Narita](https://github.com/cakecatz)

## Contributors

Issues and pull requests are very welcome. Feel free to write your own packages
using this one. For all contributions credits are due:

*   [Pascal Bihler](https://github.com/pbihler)
*   [Nikita Gusakov](https://github.com/nkt)
*   [Simon AKA simurai](https://github.com/simurai)
*   Carlos Santos
*   [Daniel Alejandro Cast](https://github.com/lexcast)
*   [James Coyle](https://github.com/JamesCoyle)
*   [Andres Suarez](https://github.com/zertosh)
*   [Tony Brix](https://github.com/UziTech)
*   [Gareth McMullin](https://github.com/gsmcmullin)
*   [Christopher Chedeau](https://github.com/vjeux)
*   [Paul Brown](https://github.com/PaulBrownMagic)
*   [Raphael Fetzer](https://github.com/pheraph)
