# Creating a dylib with Go
A small project showing how to create a dynamic library (dylib) using the basic Go compiler, and then how to incorporate and call that dylib from a Xojo desktop app.

(All macOS only.)

[Download the Go compiler etc from golang.org...](https://golang.org)

The dylib is already prebuilt in the “go dylib” subdirectory, but to start from scratch, delete the dylib and simplemath.h files.

In the Terminal, cd to the directory, then run:

`go build -buildmode=c-shared -o simplemath.dylib simplemath.go`

The new dylib and header file are generated. (For our purposes here we don’t need the .h file.)

This dylib can then be used as you would any other.

## Use in a Xojo app...

In a Xojo project, this is usually done by incorporating the dylib into your app via a copy build step (don’t forget that this copy step will come AFTER the main “compile and build” step).

The copy should (usually) be done to your app’s `Frameworks` directory.

In your code, you set up a constant with the path to your lib:

`const dylibPath as String = "@executable_path/../Frameworks/simplemath.dylib"`

(The `@executable_path` part of the path string above is not a Xojo feature as such, it’s part of OSX’s system of resolving dylib locations. [A brief explanation of @executable path, @load path and @rpath](https://wincent.com/wiki/%40executable_path%2C_%40load_path_and_%40rpath).)

You can then test the availability of the functions using `System.IsFunctionAvailable`:

`var isAvailable as Boolean = System.IsFunctionAvailable( "Add", dylibPath )`

To actually use the function:

`soft declare function Add lib dylibPath ( x as integer, y as integer ) as integer`

`var result as Integer = Add( 1, 2 )`

If you are not using `IsFunctionAvailable`, or you want to be ultra-safe you might wrap the whole thing with some exception handling:

```
try
  
    soft declare function Add lib dylibPath ( x as integer, y as integer ) as integer
  
    var result as Integer = Add( 1, 2 )

catch e as FunctionNotFoundException
  
    // various ways of checking e, via e.Message, e.ErrorNumber etc, to see what the problem is
  
end try
```

[Xojo Documention for “FunctionNotFoundException”](https://docs.xojo.com/FunctionNotFoundException)

