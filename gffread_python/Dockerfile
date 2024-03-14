FROM debian:trixie-20240311

ARG gffread_version='0.12.7'
ARG gffread_architecture='Linux_x86_64'
ARG wget_version='2.1.0'
ARG python_version='3.11.0'

#Install the Debian dependencies to allow us to make packages from source
RUN apt-get update \
    && apt-get install -y procps curl gcc libssl-dev make zlib1g-dev \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*

#Install wget2 from source and symlink it to wget
#--with-ssl: ensures wget will use secure communications with remote servers or endpoints
#--disable-dependency-tracking: we do not need to check which dependencies need to be rebuilt (side effect: speeds up build time)
RUN curl -O "https://ftp.gnu.org/gnu/wget/wget2-${wget_version}.tar.gz" -o "/wget2-${wget_version}.tar.gz" && \
    tar xvzf "/wget2-${wget_version}.tar.gz" && cd "wget2-${wget_version}" && \
    ./configure --prefix=/usr --with-ssl --disable-dependency-tracking && make && make install && \
    rm -rf "/wget2-${wget_version}"* && ln -s /usr/bin/wget2 /usr/bin/wget

RUN apt-get update \
    && apt-get install -y zlib1g-dev

#Install python3.11 from source and symlink it to python (python3-as-python package is an alternative for this)
#--enable-shared: builds a shared python library so that when executing python in memory, the OS can link dependent shared libraries so python can run
#--enable-optimizations: improves performance as python has built in profile guided optimisation for use when compiling
#--enable-ipv6: allows python to interact with internet protocal v6 addresses
#LDFLAGS=-Wl allows gcc to interpret following tokens as options when compiling
#-rpath: tells gcc what the path to hard code for shared libraries is
#--disable-new-dtags: tells the linker software in gcc to use the rpath argument instead of dynamically creating tags itself
RUN curl -O "https://www.python.org/ftp/python/${python_version}/Python-${python_version}.tgz" -o "/Python-${python_version}.tgz" && \
    tar xvzf "/Python-${python_version}.tgz" && cd Python-${python_version} && \
    ./configure --prefix=/opt/python/${python_version} --enable-shared --enable-optimizations --enable-ipv6 LDFLAGS=-Wl,-rpath=/opt/python/${python_version}/lib,--disable-new-dtags && \
    make && make install && \
    rm -rf "/Python-${python_version}"* && \
    ln -s /opt/python/${python_version}/bin/python3 /opt/python/${python_version}/bin/python

ENV PATH="${PATH}:/opt/python/${python_version}/bin"

#"gffread-${gffread_version}.${gffread_architecture}.tar.gz" is no a gzipped file despite extension
RUN wget "https://github.com/gpertea/gffread/releases/download/v${gffread_version}/gffread-${gffread_version}.${gffread_architecture}.tar.gz" && \
    tar xvf "gffread-${gffread_version}.${gffread_architecture}.tar.gz" -C /opt && \
    chmod u+x "/opt/gffread-${gffread_version}.${gffread_architecture}/gffread" && \
    rm "/gffread-${gffread_version}.${gffread_architecture}.tar.gz"

ENV PATH="${PATH}:/opt/gffread-${gffread_version}.${gffread_architecture}"
