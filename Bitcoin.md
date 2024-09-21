# BITCOIN

source: https://github.com/bitcoin/bitcoin/blob/v27.1/doc/build-unix.md

## Build

  sudo pacman --sync --needed autoconf automake boost gcc git libevent libtool make pkgconf python sqlite
  
  git clone https://github.com/bitcoin/bitcoin.git
  
  cd bitcoin/
  
  ./autogen.sh
  
  ./configure
  
  make check

## Execute all the tests using the framework

  python test/functional/test_runner.py --coverage

  ** you can execute a single test **
  
  python test/functional/example_test.py

## Run

  ./src/bitcoind
