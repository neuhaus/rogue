# Rogue in the browser (WebAssembly)

Rogue can run in a web browser: it is compiled to WebAssembly with
Emscripten and shown in a terminal emulator on a web page. High scores and
saved games are kept in the browser's storage, so they survive a reload.

## Building

You need [CMake](https://cmake.org/), a C compiler, `curl` and the
[Emscripten SDK](https://emscripten.org/docs/getting_started/downloads.html)
(emsdk). Run these commands from the top of the repository:

```sh
# Get the browser runtime (a git submodule), e.g. on a fresh clone
git submodule update --init --recursive

# Install and activate the Emscripten version the build expects
# (currently 6.0.13)
git clone https://github.com/emscripten-core/emsdk.git ~/emsdk
~/emsdk/emsdk install 6.0.13
~/emsdk/emsdk activate 6.0.13
source ~/emsdk/emsdk_env.sh

# Build, then serve the result and open http://localhost:8000/
./wasm/build-wasm.sh
cd build/web && python3 -m http.server 8000
```

The finished web page is in `build/web`. To try a different Emscripten
version, set `ALLOW_UNTESTED_EMSDK=1`; the build then only warns.

## Native sanity build

A native sanity build compiles the same sources for your own machine, with
no Emscripten involved. It is a quick way to check that the code still
builds and runs outside the browser:

```sh
cmake -S . -B build/native
cmake --build build/native
TERMCAP=wasm/rogue.termcap TERM=xterm ./build/native/rogue
```

## Game options in the browser

Rogue's `ROGUEOPTS` options can be set in the URL hash, using the same name.
The hash is not sent to the server, so it stays out of server logs. Options
are separated by commas:

    https://neuhaus.github.io/rogue/#ROGUEOPTS=qwertz
    https://neuhaus.github.io/rogue/#ROGUEOPTS=qwertz,nojump,name=Bob

`name=` fills in the page's player name field (at most 29 characters) and
skips the first-visit name prompt; the player can still edit it. It is not
passed to the game itself.

Editing the name field updates `name=` in the address bar, so the URL can be
bookmarked or shared.
