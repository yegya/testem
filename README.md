Test&rsquo;em &rsquo;Scripts!
=================

[![Build Status](https://secure.travis-ci.org/airportyh/testem.png?branch=master)](http://travis-ci.org/airportyh/testem)

Javascript unit testing can be tedious and painful - Testem aims to make this as easy as it possibly can be - so that you will no longer have *any* excuse for not writing tests. 

Features
--------

* Test framework agnostic. Support for
    - [Jasmine](http://pivotal.github.com/jasmine/)
    - [QUnit](http://docs.jquery.com/QUnit)
    - [Mocha](http://visionmedia.github.com/mocha/)
    - Can be made to work with others through custom test framework adapters. 
* Running tests in browsers as well as in [Node](http://nodejs.org)
* Two distinct use-cases: 
    - *Test-Driven-Development(TDD)* workflow
    - *Continuous Integration(CI)*. 

Screencast
----------

Watch this [screencast](http://youtu.be/ZyUN__C3xgY) to see it in action. 

Requirements
------------

You need [Node](http://nodejs.org/) version 0.6.2 or later installed on your system.

Installation
------------
To install:

    npm install testem -g
    
This will install the `testem` executable globally on your system.

Usage
-----

As stated before, Testem supports two use cases: test-driven-development and continuous integration. Let's go over each one.

Development Mode
----------------

The simplest way to use Testem, in the TDD spirit, is to start in an empty directory and run the command

    testem
    
You will see a terminal-based interface which looks like this
    
![Initial interface](https://github.com/airportyh/testem/raw/master/images/initial.png)
    
Now open your browser and go to the specified URL. You should now see

![Zero of zero](https://github.com/airportyh/testem/raw/master/images/zeros.png)
    
We see 0/0 for tests because at this point we haven't written any code, but as we write them, Testem will pickup any `.js` files
 that were added, include them, and if there are tests, run them automatically. So let's first write `hello_spec.js` in the spirit of "test first"(written in Jasmine)

    describe('hello', function(){
        it('should say hello', function(){
            expect(hello()).toBe('hello world')
        })
    })
    
Save that file and now you should see

![Red](https://github.com/airportyh/testem/raw/master/images/red.png)

Testem should automatically pickup the new files you've added and also any changes that you make to them, and rerun the tests. The test fails as we'd expect. Now we implement the spec like so in `hello.js`

    function hello(){
        return "hello world"
    }

So you should now see

![Green](https://github.com/airportyh/testem/raw/master/images/green.png)
    
### Using the Text User Interface

In development mode, Testem has a text-based graphical user interface which uses keyboard-based controls. Here is a list of the control keys

* ENTER : Run the tests
* q : Quit
* ← LEFT ARROW  : Move to the next browser tab on the left
* → RIGHT ARROW : Move to the next browser tab on the right
* ↑ UP ARROW : scroll up in the error window
* ↓ DOWN ARROW : scroll down in the error window
* TAB : switch between the top and bottom halves of the split panel (if a split is present)

### Command line options

To see all command line options do

    testem --help

### DIY: Use Any Test Framework

If you want to use Testem with a test framework that's not supported out of the box, you can write your own custom test framework adapter. See [customAdapter.js](https://github.com/airportyh/testem/blob/master/examples/custom_adapter/customAdapter.js) for an example of how to write a custom adapter.

Then, to use it, in your `testem.yml` simply set

    framework: custom

And then make sure you include the adapter code in your test suite and you are ready to go. Here for the [full example](https://github.com/airportyh/testem/tree/master/examples/custom_adapter).

### Example Projects

I've created [examples](https://github.com/airportyh/testem/tree/master/examples/) for various setups

* [Simple QUnit project](https://github.com/airportyh/testem/tree/master/examples/qunit_simple)
* [Simple Jasmine project](https://github.com/airportyh/testem/tree/master/examples/jasmine_simple)
* [Custom Jasmine project](https://github.com/airportyh/testem/tree/master/examples/jasmine_custom)
* [Custom Jasmine project using Require.js](https://github.com/airportyh/testem/tree/master/examples/jasmine_requirejs)
* [Simple Mocha Project](https://github.com/airportyh/testem/tree/master/examples/mocha_simple)
* [Custom Test Framework](https://github.com/airportyh/testem/tree/master/examples/custom_adapter)

Continuous Integration Mode
---------------------------

To use Testem for continuous integration you'd do

    testem ci
    
In CI mode, Testem runs your tests on all the browsers that are available on the system one after another. To find out what browsers are currently available - those that Testem knows about and can make use of

    testem ci -l
    
Will print them out. The output might look like

    $ testem ci -l
    Browsers available on this system: 
    IE7
    IE8
    IE9
    Chrome
    Firefox
    Safari
    Opera
    PhantomJS
    
Did you notice that this system has IE versions 7-9? Yes, actually it has only IE9 installed, but Testem uses IE's compatibility mode feature to emulate IE 7 and 8.

When you run `testem ci` to run tests, it outputs the results in the [TAP](http://testanything.org/wiki/index.php/Main_Page) format, which looks like

    ok 1 Chrome 16.0 - hello should say hello.

    1..1
    # tests 1
    # pass  1

    # ok
    
TAP is a human-readable and language-agnostic test result format. TAP plugins exist for popular CI servers

* [Jenkins TAP plugin](https://wiki.jenkins-ci.org/display/JENKINS/TAP+Plugin) - I've added [detailed instructions](https://github.com/airportyh/testem/blob/master/docs/use_with_jenkins.md) for setup with Jenkins.
* [TeamCity TAP plugin](https://github.com/pavelsher/teamcity-tap-parser)

### Command line options

To see all command line options for CI, do

    testem ci --help
    
Configuration File
------------------

For the simplest Javascript projects, the TDD workflow described above will work fine, but there are times when you want
to structure your sources files into separate directories, or want to have finer control over what files to include, this calls for the `testem.yml` configuration file (you can also alternatively use the JSON format with a `testem.json` file). It looks like this

    framework: jasmine
    src_files:
    - hello.js
    - hello_spec.js

The src_files can also be glob patterns (See: [node-glob](https://github.com/isaacs/node-glob))

    src_files:
    - js/**/*.js
    - spec/**/*.js

Custom Test Pages
-----------------

You can also use a custom page for testing. To do this, first you need to specify `test_page` to point to your test page in the config file(`framework` and `src_files` are irrelevant in this case)

    test_page: tests.html
    
Next, the test page you use needs to have the adapter code installed on them, as specified in the next section.

### Include Snippet

Include this snippet directly after your `jasmine.js`, `qunit.js` or `mocha.js` include to enable *Testem* with your test page

    <script>
    if (location.hash === '#testem')
        document.write('<script src="/testem.js"></'+'script>')
    </script>

Launchers
---------

Testem has the ability to automatically launch browsers or processes for you. To see the list of launchers Testem knows about, you can use the command

    testem launchers

This will display something like the following

    Have 5 launchers available; auto-launch info displayed on the right.

    Launcher      Type          CI  Dev
    ------------  ------------  --  ---
    Chrome        browser       ✔           
    Firefox       browser       ✔           
    Safari        browser       ✔           
    Opera         browser       ✔           
    Mocha         process(TAP)  ✔

This displays the current list of launchers that are available. Launchers can launch either a browser or a custom process - as shown in the "Type" column. Custom launchers can be defined to launch custom processes. The "CI" column indicates the launchers which will be automatically launch in CI-mode. Similarly, the "Dev" column those that will automatically launch in Dev-mode.

Running Tests in Node
---------------------

To run tests in Node you need to create a custom launcher which launchs a process which will run your tests: this is nice because it means you can use any test framework - or lack thereof. For example, to make a launcher that runs mocha tests, you would write the following in the config file `testem.yml`

    launchers:
        Mocha:
            command: mocha tests/*_tests.js

When you run `testem`, it will auto-launch the mocha process based on the specified command every time the tests are run. It will display the stdout and well as the stderr of the process inside of the "Mocha" tab in the UI. It will base the pass/fail status on the exit code of the process. In fact, because Testem can launch any arbitrary process for you, you could very well be using it to run programs in other languages.

### Processes with TAP Output

If your process outputs test results in [TAP](http://en.wikipedia.org/wiki/Test_Anything_Protocol) format, you can tell that to testem via the `protocol` property. For example

    launchers:
        Mocha:
            command: mocha tests/*_tests.js -R tap
            protocol: tap

When this is done, Testem will read in the process' stdout and parse it as TAP, and then display the test results in Testem's normal format. It will also hide the process' stdout output from the console log panel, although it will still display the stderr.

PhantomJS
---------

If you have [PhantomJS](http://www.phantomjs.org/) installed in your system and the `phantomjs` executable is in your path. In development mode, Testem will use it automatically to run your tests for your convenience. For CI, PhantomJS will be one of the available browsers and will be made use of by default.

Contributing
------------

If you want to [contribute to the project](https://github.com/airportyh/testem/blob/master/docs/contributing.md), I am going to do my best to stay out of your way. 

Roadmap
-------

1. Getting feedback from other folks using Testem in the real world, and then improve the user experience as much as possible.
2. [BrowserStack](http://www.browserstack.com/user/dashboard) integration - after seeing [Bunyip](http://www.thecssninja.com/javascript/bunyip) I was like oh snap! I need to get on this. 
3. Support for testing Node programs.
4. Browserify and Testling integration.
4. Travis CI integration.
5. Buster.JS integration.
6. Figure out a happy path for testing on mobile browsers (maybe BrowserStack).
7. Direct Coffeescript support.

Contributors
------------

* [Toby Ho](https://github.com/airportyh)
* [Raynos](https://github.com/Raynos)

Credits
-------

Testem depends on these great software

* [Node](http://nodejs.org/)
* [Socket.IO](http://socket.io/)
* [PhantomJS](http://www.phantomjs.org/)
* [Node-Tap](https://github.com/isaacs/node-tap)
* [Node-Charm](https://github.com/substack/node-charm)
* [Node Commander](http://tjholowaychuk.com/post/9103188408/commander-js-nodejs-command-line-interfaces-made-easy)
* [JS-Yaml](https://github.com/nodeca/js-yaml)
* [Winston](http://blog.nodejitsu.com/put-your-logs-anywhere-with-winston)
* [Express](http://expressjs.com/)
* [jQuery](http://jquery.com/)
* [Backbone](http://backbonejs.org/)

License
-------

(The MIT License)

Copyright (c) 2011 Toby Ho &lt;airportyh@gmail.com&gt;

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
