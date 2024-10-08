# BITCOIN

source: https://github.com/bitcoin/bitcoin/blob/v27.1/doc/build-unix.md

## Build

    sudo pacman --sync --needed autoconf automake boost gcc git libevent libtool make pkgconf python sqlite
    
    git clone https://github.com/bitcoin/bitcoin.git
    
    cd bitcoin/
    
    ./autogen.sh
    
    ./configure
    
    make -j$(nproc)
    **or define core numbers to use**
    make -j16

## Execute all the tests using the framework (and all cores from your cpu)

    python test/functional/test_runner.py -j $(nproc) --coverage

You can execute a single test:
  
    python test/functional/example_test.py

## Run
  
    ./src/bitcoind
