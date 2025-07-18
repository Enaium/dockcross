FROM dockcross/base:latest

RUN apt-get update && apt-get install -y unzip

ENV CROSS_TRIPLE=i686-linux-android
ENV CROSS_ROOT=/usr/${CROSS_TRIPLE}
ENV ANDROID_NDK=${CROSS_ROOT}
ENV AS=${CROSS_ROOT}/bin/llvm-as \
    AR=${CROSS_ROOT}/bin/llvm-ar \
    CC=${CROSS_ROOT}/bin/clang \
    CXX=${CROSS_ROOT}/bin/clang++ \
    LD=${CROSS_ROOT}/bin/ld

ENV ANDROID_NDK_REVISION=28b
ENV ANDROID_API=23

RUN mkdir -p /build && \
    cd /build && \
    curl -O https://dl.google.com/android/repository/android-ndk-r${ANDROID_NDK_REVISION}-linux.zip && \
    unzip ./android-ndk-r${ANDROID_NDK_REVISION}-linux.zip && \
    cd android-ndk-r${ANDROID_NDK_REVISION} && \
    ./build/tools/make_standalone_toolchain.py \
      --arch x86 \
      --api ${ANDROID_API} \
      --stl=libc++ \
      --install-dir=${CROSS_ROOT} && \
    cd / && \
    rm -rf /build && \
    find ${CROSS_ROOT} -exec chmod a+r '{}' \; && \
    find ${CROSS_ROOT} -executable -exec chmod a+x '{}' \;

# Prepare Rust
ENV PATH="/root/.cargo/bin/:$PATH"
RUN rustup target add i686-linux-android
COPY config.toml /root/.cargo/

# Prepare CMake
COPY Toolchain.cmake ${CROSS_ROOT}/
ENV CMAKE_TOOLCHAIN_FILE=${CROSS_ROOT}/Toolchain.cmake

# Build-time metadata as defined at http://label-schema.org
ARG BUILD_DATE
ARG IMAGE=dockcross/android-x86
ARG VERSION=latest
ARG VCS_REF
ARG VCS_URL
LABEL org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.name=$IMAGE \
      org.label-schema.version=$VERSION \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.vcs-url=$VCS_URL \
      org.label-schema.schema-version="1.0"
ENV DEFAULT_DOCKCROSS_IMAGE=${IMAGE}:${VERSION}
