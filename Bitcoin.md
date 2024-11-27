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

### Execute all the tests using the framework (and all cores from your cpu)

    python test/functional/test_runner.py -j $(nproc) --coverage

### Or You can execute a single test:
  
    python test/functional/example_test.py

### create symbolic link

    sudo ln -s $PWD/src/* /usr/local/bin/

## Download (No Build)

    wget https://bitcoincore.org/bin/bitcoin-core-27.1/bitcoin-27.1-x86_64-linux-gnu.tar.gz
    tar -xzvf bitcoin-27.1-x86_64-linux-gnu.tar.gz

### create symbolic link

    sudo ln -s $PWD/bitcoin-27.1/bin/* /usr/local/bin/

## Run

    bitcoin-cli --verion
    bitcoind --version

    bitcoind

## Create Client Configurations to connect on external/localhost node

    mkdir -p ~/.bitcoin
    echo "rpcconnect=33.333.333.333" >> ~/.bitcoin/bitcoin.conf
    echo "rpcuser=classroom" >> ~/.bitcoin/bitcoin.conf
    echo "rpcpassword=R433333uFx" >> ~/.bitcoin/bitcoin.conf
