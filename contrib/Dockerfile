# Cross compilation environment for librespot
# Build the docker image from the root of the project with the following command :
# $ docker build -t librespot-cross -f contrib/Dockerfile .
#
# The resulting image can be used to build librespot for linux x86_64, armhf(with support for armv6hf), armel, mipsel, aarch64
# $ docker run -v /tmp/librespot-build:/build librespot-cross
#
# The compiled binaries will be located in /tmp/librespot-build
#
# If only one architecture is desired, cargo can be invoked directly with the appropriate options :
# $ docker run -v /tmp/librespot-build:/build librespot-cross cargo build --release --no-default-features --features "alsa-backend"
# $ docker run -v /tmp/librespot-build:/build librespot-cross cargo build --release --target arm-unknown-linux-gnueabihf --no-default-features --features alsa-backend
# $ docker run -v /tmp/librespot-build:/build librespot-cross cargo build --release --target arm-unknown-linux-gnueabi --no-default-features --features alsa-backend
# $ docker run -v /tmp/librespot-build:/build librespot-cross cargo build --release --target aarch64-unknown-linux-gnu --no-default-features --features alsa-backend

# $ docker run -v /tmp/librespot-build:/build librespot-cross contrib/docker-build-pi-armv6hf.sh

FROM debian:buster-slim

RUN dpkg --add-architecture arm64
RUN dpkg --add-architecture armhf
RUN dpkg --add-architecture armel
RUN apt-get update

RUN apt-get install -y curl git build-essential crossbuild-essential-armhf pkg-config
RUN apt-get install -y libasound2-dev libasound2-dev:armhf
RUN apt-get install -y libpulse0 libpulse0:armhf

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
ENV PATH="/root/.cargo/bin/:${PATH}"
RUN rustup target add armv7-unknown-linux-gnueabihf

RUN mkdir /.cargo && \
    echo '[target.armv7-unknown-linux-gnueabihf]\nlinker = "/gcc-arm/bin/arm-none-linux-gnueabihf-gcc"' >> /.cargo/config

RUN mkdir /build && \
    mkdir /pi-tools && \
    mkdir /gcc-arm && \
    curl -L https://github.com/raspberrypi/tools/archive/648a6eeb1e3c2b40af4eb34d88941ee0edeb3e9a.tar.gz | tar xz --strip-components 1 -C /pi-tools && \
    curl -L 'https://developer.arm.com/-/media/Files/downloads/gnu-a/10.3-2021.07/binrel/gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf.tar.xz?rev=302e8e98351048d18b6f5b45d472f406&hash=95ED9EEB24EAEEA5C1B11BBA864519B2' > /gcc-arm/archive.xz && \
    tar xf /gcc-arm/archive.xz --strip-components=1 -C /gcc-arm

ENV CARGO_TARGET_DIR /build
ENV CARGO_HOME /build/cache
ENV PKG_CONFIG_ALLOW_CROSS=1
ENV PKG_CONFIG_PATH_armv7-unknown-linux-gnueabihf=/usr/lib/arm-linux-gnueabihf/pkgconfig/

ADD . /src
WORKDIR /src
CMD ["/src/contrib/docker-build.sh"]
