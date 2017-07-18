One of the first concepts I came in contact with after making the transition from playing with html, css and js files to building actual web applications was the concept of task runners.

Task runners help automate a lot of the repetitive or mundane tasks a developer goes through. They help convert pre-processor files like turning SASS or LESS into CSS. They help bundle smaller multiple files, e.g javascript files, into one main file, they help minify file sizes, auto-refresh the browser and watch our files in order to run any or more of the above listed processes.

Anyone developing web for more than just leisure should be using some task runner or the other and one of my favourites is Gulp. Gulp is lightweight, easy to use - once you understand basic Javascript syntax - and comes with a lot of plug-ins to get your started. Gulp also has a great community and the answers to your questions are usually a few clicks away from a smart google search.

So what is Gulp?

According to the official gulp website, gulp is a toolkit for automating painful or time-consuming tasks in your development workflow, so you can stop messing around and build something.

It really is as simple as that. Let's dive in and see if we can get a gulp workflow up and running.


Installing gulp
-------------------
Gulp is easy to install thanks to npm (node package manager). You need to have this installed on your machine before you can run the below commands. There's a great article here with instructions on how to do that:

You're going to be working quite a bit in the terminal for this one so if you're not yet comfortable with the cli (Command Line Interface), read my article 'Getting Comfortable With The Command Line' here. It is a 5 minute read and totally necessary if you're not comfortable with the cli.

Okay, now we need to create a folder called *app* (or whatever you want). Enter the following command in your terminal.

``` $ mkdir app```

Then *cd* into the folder you just created. i.e Change Directory:

```$ cd app```

Now you're inside the app folder.

Start by running ```npm init``` and following the prompts. If you don't know what to do, just press enter all the way till the end of the prompts for now. After that, run the next commands:

```
$ npm install gulp -g
$ npm install gulp --save-dev
```

First we need to install gulp globally. That is what the '-g' stands for. Installing globally means you can access the gulp module (or package) anywhere on your machine.

The second line installs it as a development dependency on your project. Which means it's a package you want to use while developing your app but not necessary once you are actually in production mode and the app is live.

Alright. Let's get started. We'll start with a task to bundle our scss files into one css file.

```
$ npm install gulp-sass --save-dev
```

Next, create a gulp file, ```gulpfile.js``` in the root of your application. 

Then enter the following code:
```javascript
var gulp = require('gulp');
var sass = require('gulp-sass');
```
This pulls in the modules/packages we installed earlier so we can use them. 

The gulp module exposes a .task() method which we use to create tasks. Let's make the first task:

```javascript
gulp.task('sass', function() {
  gulp.src('scss/main.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest('public/css'));
});
```

So what's going on up there? Well, first we invoke the gulp task() method, give it a name as the first parameter (in this case 'sass' but it could be anything you want) and then pass a function as the second parameter. The function grabs our file at the source ```gulp.src(path-to-file)``` pipes it through the sass() function, checks for any errors and logs it to the console and finally places it in the specified destination folder ```gulp.dest(path-to-destination).

The next task we could run is the task that will take all our js files and bundle them into one single javascript file.

So we can simply create another task:
```javascript
gulp.task('js', function () {
  gulp.src('js/main.js')
    .pipe(gulp.dest('public/js'));
});
```

Seems familiar doesn't it? We name our task as we did previously. This time we call it ```'js'``` then we pass the function as the second parameter. In it we grab our main.js files at folder *js* .

Fantastic. Now let's create our files. Create a ```js``` folder and place a ```main.js``` file in it, then create an ```scss``` folder and place a ```main.scss``` file in it.

Now let's run our tasks. You do this by typing 'gulp' in the command line followed by the name of the task. Like so:

```
$ gulp sass
$gulp js
```

This should take our files and output them in the specified folders. Notice how gulp creates folders where none exist. If the folder exists, it uses the folder, if not, it creates a new one along the specified ```gulp.dest()``` path.

Try adding code to each of the source files, ```main.js``` and ```main.scss``` and see how gulp moves your code over to the output files. See how powerful this is. With regards to the css, this means we can write our code with scss and not worry about getting the final product converted to css. And with the Javascript files, we can require lots of smaller modules into the main.js and have that output as one file. This allows us to have separate files in our development stage but one file for production when the app goes live.

One last thing. It would be very bothersome if all the time we made a change, we had manually type each command. What if there was a way to watch our files for changes and run our commands and also a way to have all the commands in one major 'default' command. Well gulp allows you to do that. First let's take advantage of gulp's ```watch``` method. It will watch the specified files for changes:

```javascript
gulp.task('watch', ['js', 'sass'], function () {
  gulp.watch('js/*.js', ['js']);
  gulp.watch('scss/*.scss', ['sass']);
});
```
Let's break this one down. Like before, we create a new task called 'watch'. Though we could have called it anything else. We also pass in an array, the tasks this one will be dependent on. That means that this 'watch' task will always be run only after the specified tasks (in this case 'js' and 'sass') run.

The we tell it the files to watch using the ```gulp.watch()```  method. We pass the path to the file or files we want to watch, and then in an array we pass the tasks we want to run any time the specified files are changed. Notice the ```*.js``` and ```*.scss``` above. The ```*``` character simply says 'match with any characters regardless of what they are. So in this case we are watching any '.js' files at the specified folder.

Lastly, we want a default task to set this up and running. The gulp 'default' task maps all your tasks to a single ```$ gulp``` command. The code is:

```javascript
gulp.task('default', ['watch']);
```

Now we've set our default task to ```watch```. It will run the ```watch```  task which will in turn run the other tasks. It will do this every time a file is changed. Try it for yourself. Enter the ```gulp``` command in your terminal like so:

```
$ gulp
```

And that's all to get up and running with gulp. But gulp is way more powerful than this. In posts to come, we'll be looking at other really neat things you can do with gulp. Things like combining files together, minifying file sizes, handling images and more.