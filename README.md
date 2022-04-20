This is a simple playground that demonstrates the ability to build code using `uWebsockets` with cmake


# NOTE (WINDOWS ONLY)

Taken out of the `uSockets` README:

```
The default event-loop is native epoll on Linux, native kqueue on macOS and finally libuv on Windows.
```

In order to build on windows, it is at minimum required that libuv be available.

You must clone this repo recursively via:

```
> git clone --recursive  git@github.com:tobocop2/uwebsockets-cmake-playground.git
```

as it depends on `vcpkg` for additional third party dependencies

The build system will use vcpkg that is bootstrapped out of this directory in

```
./vcpkg
```

placing the  `CMAKE_TOOLCHAIN_FILE` at

```
./vcpkg/scripts/buildsystems/vcpkg.cmake
```

For info about vpkg see: https://github.com/microsoft/vcpkg and make note of the above path


# Building

Building is the same for all platforms

```
> cmake -B build
```

Will download `uWebsockets` and generate all the necessary build files

Once generated

```
> cmake --build build
```

Will build and link all of the examples. Navigate to where the executables have been generated and run them


# Running the examples on mac/linux
The most convenient way to run the examples is without installing anything and just using the build artifacts


## Echo Server

```
./build/echo_server
```

# Running the debug build on windows if built with default args on 64bit machine


## Echo Server

```
.\build\Debug\echo_server
```

An extremely convenient way to test websockets from the commandline  is using the tool: `websocat`

For example, after installing `websocat`

You can test the echo server via the following command

```
> websocat   ws://0.0.0.0:9001
```

And watch the echo server spit back whatever you type in
