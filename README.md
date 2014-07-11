Alloy Editor
==================

## What is that?
Alloy Editor is a WYSIWYG editor, based on CKEditor, with completely rewritten UI.

## How to build it

1. Fork the repository, so you can send then patches.
2. Navigate to the directory you forked/cloned the repo.
2. Install NodeJS (http://nodejs.org/)
3. Run ```` [sudo] npm install -g gulp ````
4. Run ```` npm install ````
5. Run ```` gulp ````

If you want to have a zip file, execute ```` gulp release ````

## How to run the demo

1. Build it following the steps above.
2. Download some simple web server. I like very much "mongoose" (https://code.google.com/p/mongoose/)
3. If you decided to go with mongoose, then go to project folder and execute:
```` mongoose -document_root dist/alloy-editor-0.1.0 ````, where 0.1.0 is the current version of the editor. Don't worry, "dist" folder will contain only one folder, so you don't have to remember this 0.1.0

## How to embed it in my own page?

1. Add Boostrap 3 and Alloy Editor's CSS to the page.

````html
<link href="bootstrap.css" rel="stylesheet">
<link href="alloy-editor/assets/alloy-editor.css" rel="stylesheet">
````

2. Add YUI3 or AlloyUI seed file to the page

````html
<script src="http://yui.yahooapis.com/3.17.2/build/yui/yui-min.js"></script>
````

or

````html
<script src="http://cdn.alloyui.com/2.5.0/aui/aui-min.js"></script>
````

3. Add CKEditor's JavaScript file and Alloy Editor JavaSript:

````html
<script src="alloy-editor/ckeditor/ckeditor.js"></script>
<script src="alloy-editor/all.js"></script>
````

4. Add Alloy Editor's JavaScript:

```javascript
var editor = CKEDITOR.inline('editable');

editor.config.allowedContent = true;

editor.config.toolbars = {
    add: ['image', 'code'],
    image: ['left', 'right'],
    styles: ['strong', 'em', 'u', 'h1', 'h2', 'a', 'twitter']
};

editor.config.removePlugins = 'contextmenu,toolbar,elementspath,resize,liststyle,tabletools,link';
editor.config.extraPlugins = 'debounce,linktools,uicore,selectionregion,dropimages,placeholder,linktooltip,uitools,uiloader';
editor.config.placeholderClass = 'alloy-editor-placeholder';
editor.config.title = false;
````

In the above configuration, "editable" is the ID of the element, which will be made editable. You can fully reconfigure the toolbars using the configuration object. See below for more information.
````javascript
editor.config.toolbars = {....}
````

Aloy Editor comes with optimized version of CKEditor. Many plugins were removed. However, if you prefer the OOTB version, then you have to remove some of its plugins, like this:

````javascript
editor.config.removePlugins = 'contextmenu,toolbar,elementspath,resize,liststyle,tabletools,link';
````

"extraPlugins" is a list with the default plugins from Alloy Editor. However, you can customize these. For example, if you don't want the plugin for adding images via D&D, then just remove "dropimages" from "extraPlugins" property above.

## What is wrong with the UI of CKEditor? Why do you think Alloy Editor provides better UI?

CKEditor's UI is just old school. The toolbar appears on top, in case of inline editing it appears on top or on bottom. However, any modern editor UI places the toolbar just above the selection.
Alloy Editor actually goes a step forward in this direction and it shows the toolbars in the most appropriate position. That might be where user releases the mouse after he finishes the selection, or just where selection begins or ends.

It supports multiple toolbars, and super easy toolbar configuration. Adding buttons is also very easy. The core is fully separated from the UI, so you can add your own UI if you want. The default UI is built using YUI3, but you can create one using your favorite UI framework.

On top of that, Alloy Editor also offers some plugins. One of them is a plugin, which allows Drag&Drop of images directly in the editor. Another one is placeholder plugin, which works just as the standard placeholder HTML5 attribute, but it also allows specifying a class to be added when there is no value in the editor's area.

## Toolbar configuration
#### Default toolbar configuration

The defalt toolbar configuration in editor is as follows:

````
editor.config.toolbars = {
    add: ['image', 'code'],
    image: ['left', 'right'],
    styles: ['strong', 'em', 'u', 'h1', 'h2', 'a', 'twitter']
};
````

The configuration above represents the three toolbars - for adding (images, code, etc.), aligning images and styling text. You may remove any of those and this tollbar won't be shown. Not only that, but its code won't be loaded at all. This is just the opposite what most editor's UI do - they load everything and then just hide it.

So, if you want to remove the tollbar for adding, just delete the property "add" from the object above.

#### Buttons reordering

If you are not happy with the order of the buttons, you can just reorder them in the toolbar configuration. If you remove them, then this button won't be available. Also, its code will be not loaded at all. For example, of you want "em" to be before "strong", then do the following:
````
styles: ['em', 'strong']
````

Now the tollbar of styles will have only two buttons, and they will be for "em" (aka. italic) and "strong" (aka bold).

## How to create my own button?

If you run the editor with the default UI, which is written in YUI3, then adding a button will be straightforward. Buttons are standard YUI3 modules, which extend Widget. For your convenience, there is also base class, which you can extend.
Use CKEditor's API to do the real job and that is it!

For example, if you want to add a button, which posts the selected text to Facebook, create a YUI3 module, let's say Y.ButtonFacebook and load it on the page.
Then, add it to the configuration of the tollbar, like this:

```` styles: ['strong', 'em', 'u', 'h1', 'h2', 'a', 'twitter', 'facebook'] ````

Of course, the buttons accept configuration parameters, so you can do this with your button:

````
styles: ['strong', 'em', 'u', 'h1', 'h2', 'a', 'twitter', 'facebook': {
    customParam: 'param1',
    customParam2: 'param2'
}];

````

Btw, you may overwrite any of the params of the default buttons, just by passing such config object.

## How to create my own toolbar?


Adding a new toolbar is also as straightforward, as adding a button. If you use the default YUI3 based UI, you may create a standard module, which extends Widget. For your convenience, there is an extension, called ToolbarBase, which you can mix in your Widget. Then, name your toolbar somehow, for example ToolbarTable and load it on the page. Don't forget to add it to the configuration of the toolbars, like this:

````
editor.config.toolbars = {
    table: ['button1', 'button2'],
    add: ['image', 'code'],
    image: ['left', 'right'],
    styles: ['strong', 'em', 'u', 'h1', 'h2', 'a', 'twitter']
};

````

In this exmaple, "button1" and "button2" will be the buttons, which will be loaded on this toolbar.

Example:

````javascript
YUI.add('toolbar-table', function (Y) {
    'use strict';

    // Create a standard YUI3 module, which extends Widget. It also mixes Y.ToolbarBase extension, and two others:
    // Y.WidgetPosition and Y.WidgetAutohide
    var ToolbarTable = Y.Base.create('toolbartable', Y.Widget, [Y.ToolbarBase, Y.WidgetPosition, Y.WidgetAutohide], {
        initializer: function () {
            // write some initialization code, if needed
        },

        // render the toolbar here, this function is part of the standard YUI3 Widget API
        renderUI: function() {
        },

        // bind some events here, this function is part of the standard YUI3 Widget API
        bindUI: function() {
        },

        // This is the main part of your toolbar. If you use Y.ToolbarBase extension, as above, this function will be
        // invoked when user interacts somehow with the editor. This might be mouse click or keypress. Here you have to
        // check if toolbar should be visible or not. If so, you will have to calculate the position of the toolbar and show it.
        // The "event" attribute contains everything you need. See the comment inside the function for more information.
        _onEditorInteraction: function(event) {
            // Check if the selection is empty

            var editor = this.get('editor');

            var selectionEmpty = editor.isSelectionEmpty();

            // If there is selection, and only text is selected (not an image for example), show the toolbar

            if (!selectionEmpty && !selectionData.element && selectionData.region) {
                // Get the position where user released the mouse
                var nativeEvent = event.data.nativeEvent;

                var pos = {
                    x: nativeEvent.pageX,
                    y: nativeEvent.pageY
                };

                // You may get also the selection direction. This will help you to determine if user selected the text from
                // top to bottom or from bottom to top. Using this information, you will be able to show the toolbar
                // above or below the selection.

                var direction = selectionData.region.direction;

                // direction can be one of these: CKEDITOR.SELECTION_BOTTOM_TO_TOP or CKEDITOR.SELECTION_TOP_TO_BOTTOM

                // Then show the toolbar on the position.
                // Of course, it may happen that user used keyboard to make a selection. In this case, you may get the starting rectangle of the
                // selection.
            }
        }
    });
````

### How to help

You may help in many ways:
* Help us to improve the documentation.
* Create some buttons.
* Create some toolbars.
* Create UI, which is written using different framework than YUI3.


### License
MIT License
