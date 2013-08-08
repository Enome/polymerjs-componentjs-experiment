# Component.js + Polymer.js test

This is a small experiment I did with [Polymer.js](http://www.polymer-project.org/) and [Component.js](https://github.com/component/component) to create a distributable web component. Component.js' package manager and Polymer.js' encapsulating looks like a solid combination for doing front-end development.

## Polymer.js

First of all you need to import the Polymer.js.

```html
<script src='http://www.polymer-project.org/polymer/polymer.min.js'></script>
```

What would be even better is if you turned Polymer.js into a Component.js component as well. That way when you build your main application you only need to import the `build/build.js` file so you save one request. 

There are probably a few additional steps you need to take to turn Polymer.js into a Component.js component because it seems Polymer.js does some lazy loading (only checked the source briefly).

## Html

If you are familiar with Polymer.js you know you get new components by importing .html files. This is different from what you would mostly do with Component.js which would be converting the html files with `component convert` and then requiring them as a string. Component.json has a few fields for assets (`fonts` and `images`) but there is no field dedicated to html files. What we can use though is the generic `files` field which will copy files to `/build/<component-name>/<file>`.

```json
"files": ["bl-nk.html"]
```

This will copy `bl-nk.html` to `/build/bl-nk/bl-nk.html`. Which means we can import it in our main app html with `<link rel='import' href='build/bl-nk/bl-nk.html'>`. This isn't optimal because you need to make an extra request for each component. A better solution would be to have some sort of `html` field in the component.json that would concatenate all your html into one `build.html` file.

## Code

For adding Javascript to your Polymer.js component you have a few options. You can add it inline in your html or you can define it in a global Javascript file. The former is easy, you just add your code between script tags and you are ready to go. The latter has one gotcha because Component.js will put your code in CommonJS modules. This means if you define your `<script src='build/build.js'></script>` tag your code isn't run by default because the module wasn't required.

To solve this you can add the require statement to your component's html:

```html
<script>require('bl-nk');</script>
```

I admit, it's an extra step you need to take but having the power of CommonJS in your components is really great. This means you can divide your complicated components into small modules. This also means that you can use all the modules from the Component.js registry inside your Polymer.js components. If you are not familiar with the Component.js registry then I suggest you checkout the [website](http://component.io/) or do `component search <keyword>`. It's a very solid repository for all the problems you might encounter when doing front-end js.


## CSS

With component.js you mostly define a `styles` field with an array of paths pointing to your css files. These files get concatenated into one file at `/build/build.css`. 

```json
"styles": [ "bl-nk.css" ]
```

This is great for general styles but Polymer.js encapsulates your styles using the shadow dom. You can control this behavior with `resetStyleinheritance` and `applyAuthorStyles` on your Polymer.js element. For this example I am just using general styles and set the `applyAuthorStyles` to `true`. 

If you want to keep your styles encapsulated you need to add the path to your css file to the component.json `files` array field. That way your css gets copied to `/build/<component-name>/<file.css>` and you can require it with a `<style>` tag. This causes an extra http request though.

More info about css encapsulation can be found here: [Youtube Video](http://www.youtube.com/watch?feature=player_detailpage&list=PLRAVCSU_HVYu-zlRaqArF8Ytwz1jlMOIM&v=fqULJBBEVQE&t=703)

## Next up (untested)

In my next experiment I am going to try and import a component inside an other component. First step will be to install a component.

```sh
component install github-user/respository
```

To import it you will need to use a relative path. If the component defined their html file in the component.json `files` field it will end up at `/build/<component-name>/<file.html>`. With multiple components your `/build` structure will be something like this: 

```
/build/foo/index.html
/build/bar/index.html
```

So theoretically we should be able to import `bar` into `foo` by doing:

```html
<link rel='import' href='../bar/index.html'>
```

## Final thoughts

While there are a few gotcha with using Polymer.js and Component.js I think the overall workflow is pretty solid. Since Polymer.js is still pre-alpha you most likely shouldn't use this in production yet but it's a nice glimpse at what the future has in store for front-end development.
