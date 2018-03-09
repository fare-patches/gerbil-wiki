## Using Docker to test drive Gerbil
- The latest Gerbil images are available via Dockerhub.

`docker pull -it gerbil/scheme:latest`

- To get to the repl

`docker run -it gerbil/scheme:latest /root/gerbil/bin/gxi`

## Building Gerbil/Gambit via Dockerfile

### Dockerfile

```
from ubuntu:latest

MAINTAINER gerbil@cons.io

RUN apt update -y
RUN apt install -y libsqlite3-dev build-essential git autoconf libblas3 wget sudo lsb-release tmux tzdata libsnappy1v5  libleveldb1v5 zsh zlib1g-dev libssl-dev x11-xserver-utils pkg-config libffi-dev libffi6 gfortran libblas-dev liblapack-dev

RUN cd /root && git clone https://github.com/gambit/gambit
RUN cd /root && git clone https://github.com/vyzo/gerbil

RUN apt install -y

RUN cd /root/gambit && ./configure --prefix=/usr/local/gambit --enable-single-host --enable-c-opt --enable-gcc-opts --enable-multiple-versions --enable-openssl --enable-default-runtime-options=f8,-8,t8 --enable-poll
RUN cd /root/gambit && make -j4
RUN cd /root/gambit && make install

RUN find /usr/local/gambit/ -ls
ENV PATH "/usr/local/gambit/v4.8.9/bin:$PATH"
RUN cd /root/gerbil/src && ./build.sh stage0
RUN cd /root/gerbil/src && ./build.sh stage1
RUN cd /root/gerbil/src && ./build.sh stdlib
RUN cd /root/gerbil/src && ./build.sh lang
RUN cd /root/gerbil/src && ./build.sh tools
RUN cd /root/gerbil/src && ./build.sh tags

CMD /root/gerbil/bin/gxi
```
