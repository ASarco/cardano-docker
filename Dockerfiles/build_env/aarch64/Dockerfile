FROM ubuntu:focal
LABEL name="cardano_env"
LABEL description="AARCH64 Environment for building Cardano node & tools"
LABEL maintainer="https://github.com/pascallapointe"

# Define environment variables
ENV PATH=/root/.cabal/bin:$PATH
ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /root

# Install base utilities and dependencies
RUN apt-get update
RUN apt-get install -y apt-utils
RUN apt-get install -y git
RUN apt-get install -y wget
RUN apt-get install -y pkg-config
RUN apt-get install -y libgmp-dev
RUN apt-get install -y libssl-dev
RUN apt-get install -y libtinfo-dev
RUN apt-get install -y libsystemd-dev
RUN apt-get install -y zlib1g-dev
RUN apt-get install -y llvm-9
RUN apt-get install -y automake
RUN apt-get install -y make
RUN apt-get install -y build-essential
RUN apt-get install -y libffi-dev
RUN apt-get install -y libncursesw5
RUN apt-get install -y g++
RUN apt-get install -y tmux
RUN apt-get install -y jq

# Install Haskell plateform
RUN apt-get install -y haskell-platform

# Some preparation before building GHC from source ...
RUN git config --global url."git://github.com/ghc/packages-".insteadOf     git://github.com/ghc/packages/
RUN git config --global url."http://github.com/ghc/packages-".insteadOf    http://github.com/ghc/packages/
RUN git config --global url."https://github.com/ghc/packages-".insteadOf   https://github.com/ghc/packages/
RUN git config --global url."ssh://git\@github.com/ghc/packages-".insteadOf ssh://git\@github.com/ghc/packages/
RUN git config --global url."git\@github.com:/ghc/packages-".insteadOf      git\@github.com:/ghc/packages/

# Install IOHK version 8.6.5 of haskell compiler GHC
RUN git clone https://github.com/input-output-hk/ghc.git ghc-8.6.5
WORKDIR /root/ghc-8.6.5
RUN git checkout release/8.6.5-iohk
RUN git submodule update --init
RUN ./boot
RUN ./configure
RUN make -j3
RUN make install
WORKDIR /root
RUN rm -rf ghc-8.6.5

# Make the ghc symlink point to the newer version 8.6
RUN ln -sf /usr/local/bin/ghc /usr/bin/ghc

# Install version 3.2 of Cabal-Install
RUN git clone https://github.com/haskell/cabal.git
WORKDIR /root/cabal
RUN git checkout cabal-install-v3.2.0.0
WORKDIR /root/cabal/cabal-install
RUN ./bootstrap.sh
WORKDIR /root
RUN rm -rf cabal

# Install Cardano Libsodium dependencies
RUN apt-get install -y libtool
RUN apt-get install -y autoconf

RUN git clone https://github.com/input-output-hk/libsodium

WORKDIR /root/libsodium
RUN git checkout 66f017f1
RUN ./autogen.sh
RUN ./configure
RUN make
RUN make install

ENV LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
ENV PKG_CONFIG_PATH="/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH"

WORKDIR /root
RUN rm -rf libsodium

# Image clean-up
RUN cabal clean
RUN apt-get autoremove -y
RUN apt-get clean
RUN apt-get autoclean