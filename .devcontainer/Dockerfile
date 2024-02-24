FROM --platform=$TARGETPLATFORM mcr.microsoft.com/devcontainers/base:jammy

ARG TARGETPLATFORM
ARG BUILDPLATFORM
ARG ARM_GNU_TOOLCHAIN_VERSION=13.2.rel1

RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM" > /log

# Install packages to build STM32 CMake project
RUN apt-get update && apt-get install -y \
	build-essential \
	cmake ninja-build \
	git \
	stlink-tools \
	curl \
	usbutils \
	minicom

# Downlaod ARM GNU Toolchain
WORKDIR /opt/arm-gnu-toolchain

RUN if [ "$TARGETPLATFORM" = "linux/amd64" ]; then \
		export HOST_ARCH=x86_64; \
	else \  
		export HOST_ARCH=aarch64; \
    fi \
		&& echo "Downloading ARM GNU Toolchain URL: https://developer.arm.com/-/media/Files/downloads/gnu/${ARM_GNU_TOOLCHAIN_VERSION}/binrel/arm-gnu-toolchain-${ARM_GNU_TOOLCHAIN_VERSION}-${HOST_ARCH}-arm-none-eabi.tar.xz" >> /log \
		&& curl -Lo ${ARM_GNU_TOOLCHAIN_VERSION}.tar.xz "https://developer.arm.com/-/media/Files/downloads/gnu/${ARM_GNU_TOOLCHAIN_VERSION}/binrel/arm-gnu-toolchain-${ARM_GNU_TOOLCHAIN_VERSION}-${HOST_ARCH}-arm-none-eabi.tar.xz" \
		&& mkdir ${ARM_GNU_TOOLCHAIN_VERSION} \
		&& tar xf 13.2.rel1.tar.xz -C ./${ARM_GNU_TOOLCHAIN_VERSION} --strip-components=1 \
		&& rm ${ARM_GNU_TOOLCHAIN_VERSION}.tar.xz

# Install packages(libncursesw5, python 3.8) for arm-none-eabi-gdb
WORKDIR /opt

RUN apt-get install -y libncursesw5 \
						zlib1g-dev \
						libncurses5-dev \
						libgdbm-dev \
						libnss3-dev \
						libssl-dev \
						libreadline-dev \
						libffi-dev \
						libsqlite3-dev \
						libbz2-dev

RUN curl -Lo Python-3.8.0.tgz "https://www.python.org/ftp/python/3.8.0/Python-3.8.0.tgz" \
	&& tar -xf Python-3.8.0.tgz \
	&& rm Python-3.8.0.tgz

RUN cd Python-3.8.0 \
	&& ./configure --enable-optimizations \
	&& make -j $(nproc) \
	&& make altinstall

# Install openocd
WORKDIR /opt

RUN apt-get install -y make \
						libtool \
						pkg-config \
						autoconf \
						automake \
						texinfo \
						libusb-1.0-0-dev

RUN git clone https://github.com/openocd-org/openocd \
	&& cd openocd \
	&& ./bootstrap \
	&& ./configure --enable-stlink \
	&& make \
	&& make install

# Add .gitconfig project
WORKDIR /home

RUN git clone --recurse-submodules https://github.com/bangjinyoung/git-config

# # Add Toolchain to PATH
ENV PATH="$PATH:/opt/arm-gnu-toolchain/${ARM_GNU_TOOLCHAIN_VERSION}/bin"
ENV PATH="$PATH:/opt/openocd/src/openocd"