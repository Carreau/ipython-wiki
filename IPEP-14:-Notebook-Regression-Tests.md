<table>
<tr><td> Status </td><td> Active </td></tr>
<tr><td> Author </td><td> @dwyde</td></tr>
<tr><td> Created </td><td> April 2013</td></tr>
<tr><td> Updated </td><td> April 2013</td></tr>
<tr><td> Discussion </td><td> </td></tr>
<tr><td> Implementation </td><td> </td></tr>
</table>

# Introduction

The HTML notebook currently lacks unit or integration tests. Regression tests would help with potential [refactoring](https://github.com/ipython/ipython/wiki/IPEP-5:-Notebook-JavaScript-organization), and are generally good to have.

Features to test:

- User interface: toolbars, keyboard shortcuts
- JavaScript code
- Code cell execution
- Code output event handling / rendering
- Text cell (Markdown / LaTeX) rendering

# Test Frameworks

## CasperJS

[CasperJS](http://casperjs.org/) is a JavaScript test utility built on top of [PhantomJS](http://phantomjs.org/), a headless WebKit implementation.

### Pros

- Can test code cell execution from JavaScript
- Multi-page navigation
- xUnit-friendly output option

### Cons

- Extra software dependencies

## Selenium

Selenium is a web browser automation library with [Python bindings](https://pypi.python.org/pypi/selenium). It can be installed with `pip`.

### Pros

- Integrates with Python's standard `unittest` module
- Can test code execution in the kernel

### Cons

- Tests still need to execute strings of JavaScript, for example, to get text from CodeMirror input areas.
- Relatively slow
- An extra software dependency

### Prep work

Customize whether to run tests in Firefox, Chrome, Internet Explorer, etc.

- Use a wrapper script, or a config file

Make the tests aware of a notebook server's URL

- Run a notebook server in a subprocess, and write the port to the test process? This is straightforward with `multiprocessing`.

## QUnit

[QUnit](http://qunitjs.com/) is a JavaScript unit test framework.

### Pros

- No additional software dependencies: just HTML, JavaScript, and CSS.

### Cons

- Can't easily test executing code in a kernel

### Questions

- Can it directly test the menubar and other UI elements, or just JavaScript models?

### Prep work

Create an HTML page that includes the notebook's JavaScript,
and QUnit's code.

- Inherit a *tests* page from the notebook.html template,
  and add an associated handler to the Tornado app

## Jasmine

[Jasmine](http://pivotal.github.com/jasmine/) is a JavaScript behavior-driven development ([BDD](http://en.wikipedia.org/wiki/Behavior-driven_development)) framework.
It's a head-to-head competitor with QUnit, modeled after test tools in *Ruby on Rails*.

### Pros

Behavior-driven development, if you're a fan.

### Cons

DOM-less test runner?

### Prep work

Similar to QUnit.

# Recommendations

The notebook's regression tests should check that code cell execution works,
which is a vote for either CasperJS or Selenium.

CasperJS has less of an impedance mismatch for testing JavaScript - Selenium tests have to write JavaScript code as strings.

# Related discussions

- https://github.com/ipython/ipython/pull/1493
- https://github.com/ipython/ipython/issues/2292