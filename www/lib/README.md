# ptrLight

A pull to refresh (ptr) jQuery plugin for iOS (mobile Safari, WebView) and Android (Chromium/Webkit). Based on xpull from [Slobodan Jovanovic](https://github.com/sjovanovic/xpull).

A live demo is [available here](http://aurasalexander.github.io/demo.html). On desktop: please consider using the development tools of your browser to switch into mobile device mode. This will allow you to emulate touch events. Your default mouse cursor won't be able to trigger anything.

<img src="http://aurasalexander.github.io/github_io_ptrLight.png" alt="QR code with demo page url" width="200px"/>

## Features
- lightweight (as long as you don't have to include jQuery *just* for this plugin)
- easy to implement, intuitive to use
- verbosely documented, customizable behaviour and style
- for users:
  - rubber band effect
  - comprehensible user feedback in every stage of the process
  - user can always abort the pull process as long as the indicator wasn't let go from above the specified pull threshold

## Install

- download the git repository or use bower

```
  bower install ptrLight
```

- include jQuery, ptrLight.min.js and ptrLight.min.css

```html
  <link rel="stylesheet" href="ptrLight.css">
  <script src="https://code.jquery.com/jquery-2.2.3.min.js" type="text/javascript"></script>
  <script src="ptrLight.js"></script>
```

## Use

- craft your options:
  - **paused**: disable ptrLight temporarily
  - **ignoreThreshold**: scroll distance in pixels that will not count towards the pull distance; the element won't move and an axis check will be applied to see if the user pulls the element vertically; this will
                         prevent ptrLight from animating the element when the user swiped horizontally
  - **pullThreshold**: amount of pixels the scroll element needs to get pulled down by in order to execute the pull to refresh 'refresh()' function on drag stop
      - *hint*: You can use something to extend of `$(window).height() / 3` as value to make the threshold dependent on the screen dimension. Neat for mobile where you wouldn't want to set the drag threshold to a pixel perfect amount that you carefully crafted on your 4" android device just to see it gloriously fail on a retina iPad.
      - amount in pixel
  - **maxPullThreshold**: maximum amount of pixels the scroll element will be allowed to scroll down by
      - amount in pixel
  - **spinnerTimeout**: reset the pull to refresh indicator after this amount of time in ms or don't if the value is falsy
  - **scrollingDom**: if not otherwise specified here, the parent node of your selected element is assumed as the scroll element of your page
  - **allowPtrWhenStartedWhileScrolled**:
      - *true*: a user can start his upward scroll/downward drag on a scroll position > 0, reaching the top and pulling even further to activate ptr
      - *false*: a user will have to scroll to the very top, release his tap/drag and drag again from scroll position 0 to activate the ptr
  - **refresh**: is triggered after a user dragged and released while exceeding the pullThreshold
      - call the *.done()* method to stop and hide the ptr spinner

- call ptrLight on your desired node:

```javascript
  /* all options are optional, but you will want to at least add your refresh() handler */
  var options = {
    paused: false,
    ignoreThreshold: 20,
    pullThreshold: 200,
    maxPullThreshold: 500,
    spinnerTimeout: 10000,
    scrollingDom: $('selectorOfScrollableParent'),
    allowPtrWhenStartedWhileScrolled: false,
    refresh: function(ptrLightInstance) {
      console.log('Updating...');
      //do some reloading stuff instead of a timeout
      setTimeout(function(){
        console.log('Updated!');
        ptrLightInstance.done();
      }, 2000);
    }
  }

  $('selector').ptrLight(options);
```

- get the instance of ptrLight:
```javascript
  var ptrLight = $('selector').data("plugin_ptrLight");
```

## Style

- change the spinner:
  - *hint*: you can edit the reload.svg itself to change its appearance (e.g. the color)

```css
  body #ptr-light-spinner { /* overwrite ptrLight css by cascading */
      /*icon by loading.io, overwrite background attribute with your own loading indicator if you want*/
      background: url('reload.svg');
      /*spinner sizes*/
      height: 35px;
      width: 35px;
      /* place the spinner inside your vertical space */
      position: absolute;
      top: 10px;
  }
```

- style sizes, indirectly paddings:

```css
  body #ptr-light-indicator {
    /* adjust the vertical space the ptr indicator will live in */
    height: 55px;
    /* just set this to (height * -1) */
    top: -55px;
    /* horizontal space should equal spinner width to center correctly */
    width: 35px;    
  }
```

## Methods

- end the spinning and reset ptrLight to its initial state:

```javascript
  $('selector').data('plugin_ptrLight').done();
```

- enable or disable pull to refresh handling:

```javascript
  $('selector').data('plugin_ptrLight').options.paused = true;
```

## Angular

- ptrLight is usable with angular:

```html
  <div ptr-light="reload()"></div>
```

```javascript
  angular.module("ptrLight").directive("ptrLight", function() {
    return function(scope, elm, attr) {
      return $(elm[0]).ptrLight({
        'refresh': function() {
          return scope.$apply(attr.ptrLight);
        }
      });
    };
  });
```

## Changelog

### v2.0.0
- position pull indicator right before selector element instead as first child element of parent

### v1.1.0
- make use of requestAnimationFrame for silky smooth animation performance
- removed throttleTimeout as it is obsolete now
- refractored code

### v1.0.3
- optimized performance on touchmove event calls

### v1.0.2
- refactor the way the spinner positions itself
- add configuration options throttleTimeout and allowPtrWhenStartedWhileScrolled

### v1.0.1
- initial release
- refactored code, base project: xpull
- ptrLight won't trigger while already triggered
- new spinner styling
- throttled touchmove event
