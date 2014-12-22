While building websites at Filament Group, there are a couple tools that consistently find their way into our workflow:  
* [GruntJS](http://gruntjs.com/) is a JavaScript Task Runner. It runs on [NodeJS](http://nodejs.org/) and allows the user to easily concatenate and minify files, run unit tests, and perform many other tasks, from [linting](https://github.com/gruntjs/grunt-contrib-jshint) to [minification of images](https://github.com/gruntjs/grunt-contrib-imagemin).
* [PhantomJS](http://phantomjs.org) is a headless ([Webkit](https://www.webkit.org/)-based) web browser. A headless web browser renders a page without having a visible window. Using this functionality, we can write code that you would want to run in a browser, but see its results in the command line. This allows us to run scripts and even render snapshots of pages without having to open a browser and do it manually.
 
Together, these tools allow us to get consistent feedback for our code, by further automating checks that would normally require opening a browser.

For this example, we're going to build a Grunt task that takes a screen shot of the pages we're building (similar to [Wraith](https://github.com/BBC-News/wraith), but far less advanced). There are multiple parts to make this work, so let's break it down. First, we will write a PhantomJS script that renders each page. Second, we make a NodeJS function that calls this script. Finally, we make a GruntJS task that calls that Node function. Fun!

To get started, we need to make sure that PhantomJS is installed. Since we're using Phantom from the context of a NodeJS application, a very easy way to install it is by using the [NPM](https://www.npmjs.org/) [PhantomJS installer package](https://www.npmjs.org/package/phantomjs). Installing Phantom in this manner allows us to make sure we have easy access to the path for the Phantom command while simultaneously having a local, project-specific version of it installed.

To install locally: `npm install phantomjs`.

Now, we need to write a script to give to PhantomJS that will render a given page. This script will take two arguments. The first is the url of the page that needs to be opened. The second is the file name for the output. PhantomJS will open the page, and when the page has opened successfully, it will render the page as a `PNG` and then exit. 

```
var page = require( "webpage" ).create();

var site = phantom.args[0],
    output = phantom.args[1];

page.open( site, function( status ){
    if( status !== "success" ){
        phantom.exit( 1 );
    }
    page.render( output + ".png" );
    phantom.exit( 0 );
  });

```

Let's create a `lib` directory and save this file in it. We'll call it `screenshotter.js`. We can test this quickly by running this command from our command line (in the same directory we installed phantom): `./node_modules/.bin/phantomjs lib/screenshotter.js https://www.google.com google`. This should create a file in the same directory named `google.png`.

Now that we have a PhantomJS script, let's work on making this run from Node. PhantomJS is a completely different runtime than Node, so we need a way to communicate. Luckily, Node gives us an excellent library named [child_process](http://nodejs.org/api/child_process.html) and in particular, a method from that library called [execFile](http://nodejs.org/api/child_process.html#child_process_child_process_execfile_file_args_options_callback).

If we look at the documentation for the `execFile` method, we can see that it takes up to four arguments. One is mandatory, the other three are optional. The first argument is the `file` or, in our case, the path to PhantomJS. For the other arguments, we'll need to pass PhantomJS `args` (the url and output from above), and we'll also want to include our callback function -- so we can make sure we grab any output or errors from running Phantom.

```
var path = require( "path" );
var execFile = require( "child_process" ).execFile;
var phantomPath = require( "phantomjs" ).path;
var phantomscript = path.resolve( path.join( __dirname, "screenshotter.js" ) );

exports.takeShot = function( url, output, cb ){
    execFile( phantomPath, [
            phantomscript,
            url,
            output
    ],
    function( err, stdout, stderr ){
        if( err ){
            throw err;
        }

        if( stderr ){
            console.error( stderr );
        }

        if( stdout ){
            console.log( stdout );
        }
        if( cb ){
            cb();
        }
    });
};
```

Our example code from above is written as Node.js [module](http://nodejs.org/api/modules.html). It has a function that takes three parameters. These parameters are the same parameters that are used in the PhantomJS script from above and a callback function to run when the task has completed. It then calls `execFile` and passes it three arguments. The first is the path to PhantomJS. The second is an `Array` with the our passed in parameters. The third is our callback function. This callback function is called with three arguments: `err`, `stdout`, and `stderr`. `err` is the error thrown by Phantom if something bad happens within that script. `stderr` and `stdout` are the standard error and standard output [streams](http://en.wikipedia.org/wiki/Standard_streams). This should give us everything we need to call our script as though it's a regular NodeJS function, which will make it perfect for a Grunt task. Let's save it in `lib/shot-wrapper.js`.


Now, for the grunt task:

```
var screenshot = require( "../lib/shot-wrapper" );

grunt.registerMultiTask( 'screenshots', 'Use Grunt and PhantomJS to generate Screenshots of pages', function(){
    var done = this.async();
    // Merge task-specific and/or target-specific options with these defaults.
    var options = this.options({
        url: '',
        output: ''
    });

    screenshot.takeShot( options.url, options.output, function(){
        done();
    });
});
```

Let's take a look at this piece by piece. First, we require the `shot-wrapper` library we build above. Then, we create the task `screenshots` by using [grunt.registerMultiTask](http://gruntjs.com/api/grunt.task#grunt.task.registermultitask). Since the `takeShot` method is [asynchronous](http://en.wikipedia.org/wiki/Asynchronous_I/O), we need to create a `done` callback function that lets Grunt know when to complete the task. The `options` object sets defaults for `url` and `output` in case they aren't passed in (in this case, they're empty strings, which won't work). Finally, pass the options and the `done` callback into the `takeShot` method. Now, when somebody calls this grunt task, your code will run!

Let's give it a try! Here's an excerpt from my Gruntfile:

```
    screenshots: {
      default_options: {
        options: {
          url: 'http://www.alistapart.com/',
          output: 'ala'
        }
      }
    }
```

![Grunt task running](https://jlembeck-monosnap.s3.amazonaws.com/grunt-screenshots.gif)

The task has run, so we'll open the file produced:

```
open ala.png
```

And voila.

![CNN Homepage](https://jlembeck-monosnap.s3.amazonaws.com/ala.png)

Just imagine what you can do with your newfound power. Phantom and Grunt give you ample freedom to explore all sorts of new ways to enhance your developement workflow. Go forth and explore!

For more in depth code and to see the way this works when building a project, Check out the [repository](https://github.com/jefflembeck/grunt-screenshots).

Note: You may notice that the Webfonts are missing in the rendered image. That's currently a [known issue](https://github.com/ariya/phantomjs/issues/10592) with PhantomJS.

