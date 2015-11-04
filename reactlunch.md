
title: React lunch&learn
author:
  name: Alex Tsai
output: reactlunch.html
controls: true

--

# React lunch&learn
## aka my first PolicyStat React component

--

### stuff you should have installed

- node.js 4.x.x
	- you can generally install the version of least resistance (for example, https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager, whatever happens to be on homebrew for OSX, etc.) 
	- `npm install -g n` to install the [n version manager](https://github.com/tj/n) globally
	- `n latest` or `n stable` or `n 4.2.2` to install a given version
	- `n` lets you select from previously installed versions
- [React Dev Tools](https://github.com/facebook/react-devtools) for your browser of choice

--

### npm and package.json in <1 minute

- kinda like `setup.py` 
- defines dependencies that need to be installed 
- where your project lives and what it would be published as (for public packages on npm)
- defines commands for doing things like running tests, linting, building a dist, etc.

`npm install` installs all dependencies from a given package.json (will be spaced under `node_modules` under the same directory as `package.json`)

`npm install <somepackage> --save` installs a package and edits your package.json to add it to the list
use `--save-dev` instead for dev dependencies.  

Generally these are not going to matter unless you're actually reusing the module or installing it elsewhere  (the cases where dev dependencies are not installed)
 
--

### why React
- declarative component style
- composable
- not all or nothing (Angular, etc)
- [minimal API surface area](https://www.youtube.com/watch?v=4anAwXYqLG8)

--

### Refresh: A Mostly Simple React Component

    class Photo extends React.Component {
	      render() {
		      return (
			    <div>
				  <img src={this.props.src}/>
				  {this.props.caption}
		        </div>
		      );
		  }
    }
    Photo.propTypes = {
        src: React.PropTypes.string.isRequired,
        caption: React.PropTypes.string,
    }
    Photo.defaultProps = {
        caption: '',
    }

Simpler React components are basically functions:
`(props, state) => (DOM)`

[a side note on ES6 classes](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4 "a side note")

--
## JSX (HTML in my JavaScript?!)

    var Nav;
    // Input (JSX):
    var app = <Nav color="blue" />;
    // Output (JS):
    var app = React.createElement(Nav, {color:"blue"});

You can choose not to use JSX.  But that's a lot of `React.createElement`

--

## composability

Given an array of photos (an object with a src & caption) , make a `<ul>` where the `<li>`s contain `<Photo>` components (the previous example)

    class PhotoList extends React.Component {
      render() {
        return (
          <ul>
            {this.props.photos.map(
              photo => <li>
                <Photo {key={photo.somethingConsistentlyUnique} ...photo} />
              </li>)}
          </ul>
        );
      }
    }
    PhotoList.propTypes = {
      photos: React.PropTypes.array
    }

It's just JavaScript [Array.prototype.map](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/map) (minus the JSX.)  No magical directives here.
    
--

### a really simple React entrypoint

    import React from 'react';
    import App  from './app';
    
    React.render( <App/>, document.getElementById('react-app'))

--

### state of React

Currently on version 0.14.2

interesting stuff in 0.14
- starting to decouple React from the DOM (native, ART, canvas, three, etc)
- `react-tools` and `JSXTransformer` (in browser JSX transform) deprecated (use Babel)

--

### Flux and data flow

Common data flow pattern for React apps. 
![enter image description here](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)

--

### WTFlux??

Key points:

- one way data flow
- views do not directly mutate state
- consequently, no two way data binding 

[There are probably too many implementations.](https://github.com/voronianski/flux-comparison)  

[I like alt](http://alt.js.org/)

It didn't make sense until I read [Flux for Stupid People](http://blog.andrewray.me/flux-for-stupid-people/)

--

### Smart and dumb components

Generally you'll have smart components, which will connect to stores and pass their state and relevant action creators down to dumb components as props.

This lets you have a separate higher order component that handles async and data sources, while letting you write and test your dumb components without being aware of network concerns, choice of Flux implementation, etc.

[more reading](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

--

### Babel

- "Transforms ES2015 and beyond to plain JavaScript"
- also transforms JSX (the funny inline HTML-looking syntax from React) to JavaScript
- ships with a Polyfill (core.js & regenerator) in order to support the full set of ES6 features
- can use it on single files from the command-line, with nodejs (`babel-node`) or with other tools
- you can write ES6/2015 and beyond today!  (minus a few features that can't be transpiled, such as tail call optimization)
- going beyond - you can actually start playing with ES7 (decorators) and other experimental features today.  
 
ES2015 reference: http://babeljs.io/docs/learn-es2015/  
https://kangax.github.io/compat-table/es6/ 

--

### Webpack

![https://webpack.github.io/assets/what-is-webpack.png](https://webpack.github.io/assets/what-is-webpack.png)

"webpack takes modules with dependencies and generates static assets representing those modules."

--

### Cool Webpack features

- supports multiple JavaScript module formats (AMD/CommonJS)
- also supports HTML, CSS, files, any static resource really
- loaders do input transform (babel-loader transforms stuff through babel before doing webpack stuff on it)
- you can do cool (and scary) things 
- `imports-loader` to bring in a constant or global into file scope 
- `exports-loader` to make globals available to your bundled code, or to export your bundle in a certain way (as global variables)
- split out common code (vendor code) with `CommonsChunkPlugin` that can be shared across entrypoints

--

### a really simple webpack config

    // webpack.config.js
      module.exports = {
        entry: './main.js',
        output: {
          filename: 'bundle.js'       
         }
       };

`webpack` for building once for development
`webpack -p` for building once for production (minification)
`webpack --watch` for continuous incremental build in development (fast!)
`webpack -d` to include source maps

--

### further webpack reading
- http://survivejs.com/webpack_react/introduction/
- https://github.com/petehunt/webpack-howto
- https://christianalfoni.github.io/react-webpack-cookbook/
- http://owaislone.org/blog/webpack-plus-reactjs-and-django/

--

### Jest

- Automatically finds tests to execute in your repo
- Automatically mocks dependencies for you when running your tests
- Allows you to test asynchronous code synchronously
- Runs your tests with a fake DOM implementation (via jsdom) so that your tests can run on the command line
- Runs tests in parallel processes so that they finish sooner

--

### Jest (cont'd)

Add

      "scripts": {
        "test": "jest"
      },
      "jest": {
        "scriptPreprocessor": "<rootDir>/node_modules/babel-jest",
        "unmockedModulePathPatterns": [
          "node_modules/react",
          "node_modules/react-dom",
          "node_modules/react-addons-test-utils",
          "node_modules/fbjs"
          // any other core libs you need should go here (moment.js, etc)
          ]
       }

to `package.json` if you didn't add it initially.

Then run `npm test` or `npm test -- --watch`

Actually writing tests - [the Jest tutorial](https://facebook.github.io/jest/docs/tutorial-react.html#content) does a pretty good job.  

The main thing to be aware of is that the automocking is a double edged sword.  

Writing tests with `mocha` is a reasonable alternative....

--

## putting things together with Django

--

### tooling

Use front end tools for front end things.  Keep things mostly separate.

In our case (for the initial component) - just build the dist with `webpack` or `webpack -p` and plop it into `site-media`.

include it as any other JavaScript.

--

## passing initial data into the component / shared state

Lots of ways to manage this in your entrypoint.

--

### data attributes

    var mountElement = document.getElementById('shareTable');
    var url = mountElement.dataset.url;
    React.render(
	    <ShareTable sourceUrl={url}/>,
	    mountElement
	    );

--

### entrypoint provides a function that can be invoked from your Django template

`entry.js`

    window.makePhotoComponent = function(id, image_uri) {
      React.render(
        <Photo src={image_uri}/>,
        document.getElementById(id)
      )
    }

`template.html`

    <script src='dist/entry.dist.js'/>
    <script>
       makePhotoComponent('someId', '{{ image_url }}');
    </script>

--

### Don't pass things through templates at all

DRF-only backend + React app as a front end is a thing too, for new apps.

--

### next steps

- build more components, make more choices
- global webpack config for all 'modern' JS (React components, editors, etc.) - have proper module chunking, code splitting, etc.
- move to django-webpack-loader to handle the bridge between webpack'ed JavaScript and Django