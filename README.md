# Haskell binding to Apple's SpriteKit framework

Open source under BSD3 license. Contributions under the same license are most welcome.

To build this project, you need a recent version of Xcode and GHC 8.0.2. If you don't want to build it yourself, a pre-compiled version comes with [Haskell for Mac](http://haskellformac.com), which also provides the best Haskell SpriteKit development experience.

This code has currently only been tested on macOS. The main obstacle to using it on iOS is lack of support for Template Haskell (and hence, `language-c-inline`) by GHC for iOS, but [What is New in Cross Compiling Haskell](https://medium.com/@zw3rk/what-is-new-in-cross-compiling-haskell-cf9c9a590ac8) might help.


## An example game: Shades

![Shades Loop](https://raw.githubusercontent.com/gckeller/shades/master/images/ShadesLoop.gif)

Have a look at a clone of the mobile games Shades as a simple example to get you started:

> https://github.com/gckeller/shades

This implementation of Shades is explained in detail in the paper [Haskell SpriteKit — Transforming an Imperative Object-oriented API into a Purely Functional One](http://www.cse.unsw.edu.au/~chak/papers/CK17.html), which provides an overview of the design and internals of Haskell SpiteKit.


## Another example game: Lazy Lambda

![Lazy Lambda Loop](https://raw.githubusercontent.com/mchakravarty/lazy-lambda/master/images/LazyLambdaLoop.gif)

As a second example of a simple game in Haskell SpriteKit, have a look at Lazy Lambda, a Flappy Bird clone:

> https://github.com/mchakravarty/lazy-lambda

For an explanation of the main concepts of the Haskell SpriteKit binding including live coding of Lazy Lambda, watch the talk [Playing with Graphics and Animations in Haskell](https://speakerdeck.com/mchakravarty/playing-with-graphics-and-animations-in-haskell) (includes video and slides).


## Building with a stock GHC distribution

Unfortunately, a quick and easy cabal or stack-based build is not possible. In addition to Haskell, this project includes Objective-C and Swift code, and neither cabal nor stack can handle this properly.

The build requirements are as follows:

* Xcode 8 (from the Mac App Store) on macOS 10.11 or 10.12. (The latest Xcode 7 might also work, maybe with light tweaking, but I haven't tried in a while.)

* GHC 8.0.2 along with alex, cabal, cpphs, and happy. (The build system expects to find those in your `$PATH`.)

Open the project in Xcode and build or archive. In either case, the build procedure will install a fair few packages from Hackage into your user package database (see the `.cabal` file in the `spritekit` directory for details), including a `spritekit` package. Moreover, Xcode will produce a `HaskellSpriteKit.framework`. The documentation is only built in Release mode.

To use, `HaskellSpriteKit.framework` in an app, you need to do the following:

* Write some GUI code in Swift (or Objective-C), to open a window and put an `SKView` into that window (in which you can present the SpriteKit scene generated by the Haskell code).

* Write Haskell code that uses the `spritekit` package to implement a SpriteKit scene. Call this code from Swift, implementing the GUI, using the Haskell FFI or by using the Objective-C support in `language-c-inline`. The latter is what Haskell SpriteKit uses internally. To learn more, check out my Haskell Symposium 2014 talk: [Foreign Inline Code in Haskell](https://speakerdeck.com/mchakravarty/foreign-inline-code-in-haskell-haskell-symposium-2014).

* Link all this together into one app that includes `HaskellSpriteKit.framework` as an embedded framework. I recommend to compile all Haskell code using custom build scripts in Xcode in the same way that the Haskell SpriteKit build system works. This makes it easy to do all packaging, code signing, etc right there in Xcode as well. In fact, you may like to put all your own Haskell code into your own embedded framework inside your app. This speeds up builds and simplifies linking in my experience.

The build system assumes that everything is going to be linked dynamically.

### Caveat

By default, the dynamic libraries will be spread over two locations: (1) all Haskell library code is in the user package database and (2) all Swift library code is embedded in the framework bundle (as customary on macOS). To get a self-contained application bundle, you need to copy all the Haskell libraries into the bundle and adjust the RPATHs in objects that refer to those libraries. (You need to do this before code signing.)


## Feature set

For details of the supported API, see the current [Haddock documentation](http://support.hfm.io/1.3/api/spritekit-0.9.0.0/Graphics-SpriteKit.html) (currently version 0.9.0.0).

### Supported features

The Haskell binding supports the following SpriteKit node types:

* *Plain* (`SKNode`): [Original SpriteKit documentation](https://developer.apple.com/reference/spritekit/sknode?language=objc)
* *Sprites* (`SKSpriteNode`): [Original SpriteKit documentation](https://developer.apple.com/reference/spritekit/skspritenode?language=objc)
* *Shapes* (`SKShapeNode`): [Original SpriteKit documentation](https://developer.apple.com/reference/spritekit/skshapenode?language=objc)
* *Labels* (`SKLabelNode`): [Original SpriteKit documentation](https://developer.apple.com/reference/spritekit/sklabelnode?language=objc)

Moreover, almost all SpriteKit animation actions (`SKAction`) are supported as well as textures and graphics paths (to define polygons and Bézier curves). All the basic features of the physics engine are supported, such as gravity, collisions, contact handlers, various material properties, as well as volume-based and edged-based physics bodies.

### Unsupported features

* Unsupported node types: video nodes, emitter nodes, crop nodes, effect nodes, light nodes, field nodes, camera nodes, audio nodes, and reference nodes.
* The various search routines are not supported.
* Physics: joints, constraints, and fields are not supported.
* No macOS 10.12 features are supported.

Most of the missing features can be supported quite easily by simply following the same approach as used in the existing code. (Just open an issue if you are looking for something specific. Or, if you can, implement it and open a pull request.)

Moreover, this project needs a proper suite of unit tests.
