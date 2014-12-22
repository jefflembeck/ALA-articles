While building JavaScript related projects (whether server-side via Node.js or front-end libraries), a build tool to help easily maintain and automate many of the processes â€” including testing, concatenating files, minification, compiling templates, as well as many other options â€” can be incredibly useful. This takes the step out that's most prone to error (me, in the case of my projects) and replaces it with a fast and consistent system that never forgets to update or copy your files over. There are many great build tools, including [Grunt](http://gruntjs.com/), [Broccoli](https://github.com/broccolijs/broccoli), and [Brunch](http://brunch.io/).

One of these build tools that we find particularly helpful is [gulp.js](http://gulpjs.com/). It's fast and I've found it really easy to work with since I learned how to set it up and incorporate it into a workflow. Today, Iâ€™m going to walk through that process.

So, what's gulp? Gulp is build system built on the concept of streams. Bear with me here, I might need to go a little high level on this. Streams are a way to develop large pieces of software out of many small pieces. The philosophy behind it is for each component to do one thing and to have the same way of communicating as the next. This allows me to mix and match these small components so I can take the output from any one that follows this philosophy and plug it into the next. The principles of streaming are significant in Node.js and as a platform, Node gives some helpful syntax for the utilization of streaming. Gulp, as a Node.js tool, utilizes this syntax and follows these principles to allow the user to piece together a large build system while escaping a lot of the complexity that comes with big tools that do a lot of things. A quick example of this is in the way gulp tasks are structured, using the [pipe](http://nodejs.org/api/stream.html#stream_readable_pipe_destination_options) method.

```JavaScript
gulp.src('script/lib/*.js') // read all of the files that are in script/lib with a .js extension
  .pipe(jshint()) // run their contents through jshint
  .pipe(jshint.reporter('default')) // report any findings from jshint
  .pipe(concat('all.js'))
```

One stream is piped into a destination, which is then piped into another destination, and so on and so forth. We'll come back to this example later.

Now, I know what you're thinking, "This sounds like a lot to learn in order to do some basic stuff that I can just do by hand," and if you are thinking that:
* A - I'm a mindreader and this is totally going to work out for me
* B - We should talk about the benefits of an automated build system.
 
If you're already using something to perform the concatenation, minification, testing, etc. of your files - go ahead and skip to the next paragraph. I'll take care of you there. If not, let's get right to the point in business terms: the inital cost incurred in you learning how to use this technology and to spin it up on your projects will be made up for exponentially with a decrease in risk for errors and also a strong decrease in required knowledge transfer if somebody else comes onto the project.

Maybe you're not thinking that, maybe you're thinking "I already use Grunt for all my processes. I'm the Admiral of Automation, the Captain of the Command line, the General of...um... you get where I am going with this. I am very good at setting up my own projects with my build tool of choice." That's awesome! Though, in my experience, it never hurts to learn something new (well, not for a long period of time... I mean, it probably won't kill you) and who knows? You might even like this better!

What's better about Gulp? First, it's fast. Since Gulp uses that stream thing I mentioned earlier, it's very nature is in passing data from one program to another instead of reading a file, performing a task, writing a file, then doing that again with another task. Another thing that's nice about Gulp is how easy it is to read. Since it's just short bits of code, it becomes very clear, very quickly, what your tasks do. This differs from other systems that use a configuration file in that configuration files tend to mean jumping around in a file a lot and making sure you're keeping track in your head what's going on and where it's happening. This may not seem like a big deal if you're the person who set up the configuration or if you're early in the project, but if you're new and debugging - the more you have to keep track of, the worse.

This is what had me, faster and easier to understand? Sold.

Now that you've heard a little bit of the why, let's talk about the how. Installation is dead simple, thanks to our good friend, [NPM](https://www.npmjs.com/).

First, make sure Gulp is installed globally.

`npm install -g gulp`

Now, in your project, install Gulp as a developer dependency

`npm install --save-dev gulp`

Next, make `gulpfile.js` in the root of your project

```JavaScript
var gulp = require('gulp');

gulp.task('default', function() {
  // place code here
});
```

Lastly, run `gulp` from your command line

`gulp`

```
[20:27:35] Using gulpfile ~/code/example/gulpfile.js
[20:27:35] Starting 'default'...
[20:27:35] Finished 'default' after 45 Î¼s
```

_NICE_

Let's get to writing a task. Now, on any normal project, I'm likely going to want to run [JShint](http://jshint.com/) on my files, concatenate all of them, and then minify. I'll also want to make sure a minified and non-minified version are saved. First, let's make sure to install the plugins via NPM as well. We can do that by placing these in our package.json.

```JavaScript
'gulp-jshint': '1.9.0',
'gulp-concat': '2.4.2',
'gulp-rename': '1.2.0',
'gulp-uglify': '1.0.2'
```

And running `npm install`

Now, let's write that gulpfile:

```JavaScript
var gulp = require('gulp');
var jshint = require('gulp-jshint');
var concat = require('gulp-concat');
var rename = require('gulp-rename');
var uglify = require('gulp-uglify');

gulp.task('js-linting-compiling', function(){
  return gulp.src('script/lib/*.js') // read all of the files that are in script/lib with a .js extension
    .pipe(jshint()) // run their contents through jshint
    .pipe(jshint.reporter('default')) // report any findings from jshint
    .pipe(concat('all.js')) // concatenate all of the file contents into a file titled 'all.js'
    .pipe(gulp.dest('dist/js')) // write that file to the dist/js directory
    .pipe(rename('all.min.js')) // now rename the file in memory to 'all.min.js'
    .pipe(uglify()) // run uglify (for minification) on 'all.min.js'
    .pipe(gulp.dest('dist/js')); // write all.min.js to the dist/js file
});
```

I've added some comments next to each line, but let's take a closer look:

```JavaScript 
var gulp = require('gulp');
var jshint = require('gulp-jshint');
var concat = require('gulp-concat');
var rename = require('gulp-rename');
var uglify = require('gulp-uglify');
```

At the top, I'm requiring each plugin that I'll be using as well as gulp itself.

```JavaScript
gulp.task('js-linting-compiling', function(){
```

Now I'm creating a task and naming it "js-linting-compiling", because I am not very creative and that is pretty descriptive.

```JavaScript
return gulp.src('script/lib/*.js')
```

Now I'm reading the files that are in the script/lib folder that have the extension .js. This line begins with a `return`, because I'm returning a stream from the task itself.

From this point forward, think about streams. The component takes in input, makes a change, and then produces output that the next component can read. 

```JavaScript
    .pipe(jshint()) // run their contents through jshint
    .pipe(jshint.reporter('default')) // report any findings from jshint
    .pipe(concat('all.js')) // concatenate all of the file contents into a file titled 'all.js'
    .pipe(gulp.dest('dist/js')) // write that file to the dist/js directory
    .pipe(rename('all.min.js')) // now rename the file in memory to 'all.min.js'
    .pipe(uglify()) // run uglify (for minification) on 'all.min.js'
    .pipe(gulp.dest('dist/js')); // write all.min.js to the dist/js file
```

That will hopefully get you started. If you'd like to learn more, I'd check out:

* [The gulp.js repository](https://github.com/gulpjs/gulp) - including all of the links they suggest, especially - 
* [Substack's Stream Handbook](https://github.com/substack/stream-handbook)
* [Google's Web Starter Kit Gulpfile](https://github.com/google/web-starter-kit/blob/master/gulpfile.js)
* [gulp.js plugin registry](http://gulpjs.com/plugins/)


