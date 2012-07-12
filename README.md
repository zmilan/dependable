# Dependable

A minimalist dependency injection framework for javascript (fully tested for node.js)

## Goals

1. Allow creation of modules that can be passed dependencies instead of requiring them. 
2. Simple bootstrapping, minimal footprint
3. Automatic

## Documentation

### What is Dependency Injectino
In CommonJS, modules are normally defined like one of the following

    exports.hello = -> "hello world"

    module.exports =
      hello: "hello world"


If you have a dependency, you normally just require it. The `greetings` module needs some config to work. 

    Greetings = require "greetings"
    greetings = new Greetings("en")

    module.exports = ->
      hello: -> greetings.hello("world")

Instead of requiring it, and setting the config inside our module, we can require that a pre-configured greetings instance be passed in to our function.

    module.exports = (greetings) ->
      hello: -> greetings.hello("world")

This is "Dependency Injection". `greetings` is a dependency of our module. Its dependencies are "injected", meaning they are handed to us by whoever uses our module instead of us reaching out for them. 

### Why Dependency Injection?

If you ever need to change your config options, such as when testing, or to hit different layers of a service, it is easier to have a pre-configured object passed in instead of creating it yourself.

Your module is a little less coupled to its dependency

You can pass in mock or alternate versions of a module if you want

### This is Annoying

It is hard to remember how to construct and configure the dependencies when you want to use your module. This should be automatic.  Here is an example

    # robot.coffee
    module.exports = (greetings) ->
      hello: -> greetings.hello("world")

    # greetings.coffee
    module.exports = (language) ->
      ...

    # app.coffee: or every time you want to use it:
    Greetings = require "greetings"
    greetings = Greetings("en")

    Robot = require "robot"
    robot = Robot greetings

    itsAliiiive = -> 
      console.log robot.hello()


*This gets much worse* when your dependencies have dependencies of their own. You have to remember in which order to configure them so you can pass them into each other. 

### Dependenable

Dependable automates this process. In the following example, you don't need to register the modules in order, or do it more than once (it will work for dependencies of dependencies)


robot.coffee
    module.exports = (greetings) ->
      hello: -> greetings.hello("world")

greetings.coffee
    module.exports = (language) ->
      ...

app.coffee

    # create the container
    container = require("dependable").container
    deps = container()
    deps.register "language", "en"
    deps.register "greetings", require("greetings")
    deps.register "robot", require("robot")

    robot = deps.get "robot"

    itsAliiiive = -> 
      console.log robot.hello()

## Reference


