FROM gcc:8.4

RUN apt update && \
    apt install -y gcovr lcov wget unzip zip

# Install Boost (1.73.0)
RUN apt update && \
    apt install -y wget zip unzip python-dev libxml2-dev libxslt-dev

WORKDIR /root/
RUN wget https://dl.bintray.com/boostorg/release/1.73.0/source/boost_1_73_0.tar.gz && \
    tar -xzvf boost_1_73_0.tar.gz

WORKDIR /root/boost_1_73_0
RUN ./bootstrap.sh --prefix=/usr/local && \
    ./b2 && \
    ./b2 install

WORKDIR /root/
RUN rm -r boost_1_73_0 && \
    rm boost_1_73_0.tar.gz

# Install CMake (3.17.3)
RUN wget https://github.com/Kitware/CMake/releases/download/v3.17.3/cmake-3.17.3-Linux-x86_64.sh \
      -q -O /tmp/cmake-install.sh \
      && chmod u+x /tmp/cmake-install.sh \
      && mkdir /usr/bin/cmake \
      && /tmp/cmake-install.sh --skip-license --prefix=/usr/bin/cmake \
      && rm /tmp/cmake-install.sh

ENV PATH="/usr/bin/cmake/bin:${PATH}"


####### Install gtest/gmock #######
WORKDIR /root
RUN wget -O googletest.zip https://github.com/google/googletest/archive/release-1.10.0.zip
RUN unzip googletest.zip
RUN mv googletest-release-1.10.0 googletest

WORKDIR /root/googletest
RUN mkdir build

WORKDIR /root/googletest/build
RUN cmake .. && \
    make -j4 && \
    make install && \
    ldconfig

WORKDIR /root
RUN rm -r googletest googletest.zip