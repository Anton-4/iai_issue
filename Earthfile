FROM rust:1.52-slim-buster
WORKDIR /earthbuild

prep-debian:
    RUN apt -y update

install-other-libs:
    FROM +prep-debian
    RUN apt -y install wget libc++-dev libc++abi-dev g++ libunwind-dev pkg-config

install-valgrind:
    FROM +install-other-libs
    # valgrind
    RUN apt -y install autotools-dev cmake automake libc6-dbg
    RUN wget https://sourceware.org/pub/valgrind/valgrind-3.16.1.tar.bz2
    RUN tar -xf valgrind-3.16.1.tar.bz2
    # need to cd every time, every command starts at WORKDIR
    RUN cd valgrind-3.16.1 && ./autogen.sh
    RUN cd valgrind-3.16.1 && ./configure --disable-dependency-tracking
    RUN cd valgrind-3.16.1 && make -j`nproc`
    RUN cd valgrind-3.16.1 && make install

copy-dirs:
    FROM +install-valgrind
    COPY --dir benches src Cargo.toml Cargo.lock ./

bench-iai:
    FROM +copy-dirs
    ENV RUST_BACKTRACE=full
    RUN cargo bench my_benchmark
    
