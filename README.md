# prompt [![Build Status](https://secure.travis-ci.org/flatiron/prompt.png)](http://travis-ci.org/flatiron/prompt)

A beautiful command-line prompt for node.js

## Features

* prompts the user for input
* supports validation and defaults
* hides passwords

## Installation

### Installing npm (node package manager)
```
curl http://npmjs.org/install.sh | sh
```

### Installing prompt
```
[sudo] npm install prompt
```

## Usage
Using prompt is relatively straight forward. There are two core methods you should be aware of: `prompt.get()` and `prompt.addProperties()`. There methods take strings representing property names in addition to objects for complex property validation (and more). There are a number of [examples][0] that you should examine for detailed usage.

### Getting Basic Prompt Information
Getting started with `prompt` is easy. Lets take a look at `examples/simple-prompt.js`:

``` js
var prompt = require('prompt');

//
// Start the prompt
//
prompt.start();

//
// Get two properties from the user: username and email
//
prompt.get(['username', 'email'], function (err, result) {
  //
  // Log the results.
  //
  console.log('Command-line input received:');
  console.log('  username: ' + result.username);
  console.log('  email: ' + result.email);
})
```

This will result in the following command-line output:

```
$ node examples/simple-prompt.js 
prompt: username: some-user
prompt: email: some-user@some-place.org
Command-line input received:
  username: some-user
  email: some-user@some-place.org
```

### Prompting with Validation, Default Values, and More (Complex Properties)
In addition to prompting the user with simple string prompts, there is a robust API for getting and validating complex information from a command-line prompt. Here's a quick sample:

``` js
var schema = {
  properties: {
    name: {
      pattern: /^[a-zA-Z\s\-]+$/,
      message: 'Name must be only letters, spaces, or dashes',
      required: true
    },
    password: {
      hidden: true
    }
  }
};

//
// Start the prompt
//
prompt.start();

//
// Get two properties from the user: email, password
//
prompt.get(schema, function (err, result) {
  //
  // Log the results.
  //
  console.log('Command-line input received:');
  console.log('  name: ' + result.name);
  console.log('  password: ' + result.password);
});
```

Pretty easy right? The output from the above script is: 

```
$ node examples/property-prompt.js
prompt: name: nodejitsu000
error:  Invalid input for name
error:  Name must be only letters, spaces, or dashes
prompt: name: Nodejitsu Inc
prompt: password: 
Command-line input received:
  name: Nodejitsu Inc
  password: some-password  
```

## Valid Property Settings
`prompt` understands JSON-schema with a few extra parameters and uses [revalidator](https://github.com/flatiron/revalidator) for validation.

Here's an overview of the properties that may be used for validation and prompting controls:

``` js
{
  description: 'Enter your password',     // Prompt displayed to the user. If not supplied name will be used.
  pattern: /^\w+$/,                  // Regular expression that input must be valid against.
  message: 'Password must be letters', // Warning message to display if validation fails.
  hidden: true,                        // If true, characters entered will not be output to console.
  default: 'lamepassword',             // Default value to use if no value is entered.
  required: true                        // If true, value entered must be non-empty.
}
```

Alternatives to `pattern` include `format` and `conform`, as documented in [revalidator](https://github.com/flatiron/revalidator).

### Alternate Validation API:

Prompt, in addition to iterating over JSON-Schema properties, will also happily iterate over an array of validation objects:

```js
var prompt = require('../lib/prompt');

//
// Start the prompt
//
prompt.start();

//
// Get two properties from the user: username and password
//
prompt.get([{
    name: 'username',
    required: true
  }, {
    name: 'password',
    hidden: true,
    conform: function (value) {
      return true;
    }
  }], function (err, result) {
  //
  // Log the results.
  //
  console.log('Command-line input received:');
  console.log('  username: ' + result.username);
  console.log('  password: ' + result.password);
});
```

Note that, while this structure is similar to that used by prompt 0.1.x, that the object properties use the same names as in JSON-Schema and are not backards compatible with prompt 0.1.x.

### Skipping Prompts

Sometimes power users may wish to skip promts and specify all data as command line options. 
if a value is set as a property of `prompt.override` prompt will use that instead of 
prompting the user.

``` js
//prompt-override.js

var prompt = require('prompt'),
    optimist = require('optimist')

//
// set the overrides
//
prompt.override = optimist.argv

//
// Start the prompt
//
prompt.start();

//
// Get two properties from the user: username and email
//
prompt.get(['username', 'email'], function (err, result) {
  //
  // Log the results.
  //
  console.log('Command-line input received:');
  console.log('  username: ' + result.username);
  console.log('  email: ' + result.email);
})

//: node prompt-override.js --username USER --email EMAIL

```


### Adding Properties to an Object 
A common use-case for prompting users for data from the command-line is to extend or create a configuration object that is passed onto the entry-point method for your CLI tool. `prompt` exposes a convenience method for doing just this: 

``` js
var obj = {
  password: 'lamepassword',
  mindset: 'NY'
}

//
// Log the initial object.
//
console.log('Initial object to be extended:');
console.dir(obj);

//
// Add two properties to the empty object: username and email
//
prompt.addProperties(obj, ['username', 'email'], function (err) {
  //
  // Log the results.
  //
  console.log('Updated object received:');
  console.dir(obj);
});
```

## Customizing your prompt
Aside from changing `property.message`, you can also change `prompt.message`
and `prompt.delimiter` to change the appearance of your prompt.

The basic structure of a prompt is this:

``` js
prompt.message + prompt.delimiter + property.message + prompt.delimiter;
```

The default `prompt.message` is "prompt," the default `prompt.delimiter` is
": ", and the default `property.message` is `property.name`.
Changing these allows you to customize the appearance of your prompts! In
addition, prompt supports ANSI color codes via the
[colors module](https://github.com/Marak/colors.js) for custom colors. For a
very colorful example:

``` js
var prompt = require("prompt");
//
// The colors module adds color properties to String.prototype
//
require("colors");

//
// Setting these properties customizes the prompt.
//
prompt.message = "Question!".rainbow;
prompt.delimiter = "><".green;

prompt.start();

prompt.get([{ name: "name",
              message: "What is your name?".magenta }], function (err, result) {
  console.log("You said your name is: ".cyan + result.name.cyan);
});
```

## Running tests
```
vows test/*-test.js --spec
```

#### Author: [Charlie Robbins][1]

[0]: https://github.com/flatiron/prompt/tree/master/examples
[1]: http://nodejitsu.com
