FROM debian:stable

ENV HOME=/root
ENV TZ=Europe/Prague
ENV LC_ALL=en_US.utf8
ENV DEBIAN_FRONTEND=noninteractive

# Install base packages
# TODO once bullseye becomes stable remove the backports
RUN \
  sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
  echo "deb http://deb.debian.org/debian buster-backports main" > /etc/apt/sources.list.d/backports.list && \
  echo "# Installing base packages" && \
  apt-get update && \
  apt-get -y upgrade && \
  apt-get -y install --no-install-recommends \
    lua5.1 liblua5.1-0-dev libjson-c-dev \
    git cmake make pkg-config gcc g++ openssh-client \
    python3-prctl python3-dev python3-setuptools python3-pip \
    locales \
    && \
  apt-get clean

# Set Timezone
RUN \
  rm -f /etc/localtime && \
  echo "$TZ" > /etc/timezone && \
  dpkg-reconfigure -f noninteractive tzdata

# Update python paths
RUN \
  update-alternatives --install /usr/bin/python python /usr/bin/python3 1 && \
  update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1

# Generate locales
RUN \
  echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
  locale-gen

# Compile libubox
RUN \
  mkdir -p ~/build && \
  cd ~/build && \
  rm -rf libubox && \
  git clone git://git.openwrt.org/project/libubox.git && \
  cd ~/build/libubox && \
  git checkout master && \
  cmake CMakeLists.txt -DCMAKE_INSTALL_PREFIX:PATH=/usr && \
  make install

# Compile ubus
RUN \
  mkdir -p ~/build && \
  cd ~/build && \
  rm -rf ubus && \
  git clone https://gitlab.nic.cz/turris/ubus.git && \
  cd ~/build/ubus && \
  git checkout master && \
  cmake CMakeLists.txt -DCMAKE_INSTALL_PREFIX:PATH=/usr && \
  make install

# Add Gitlab's SSH key
RUN \
  mkdir /root/.ssh && \
  ssh-keyscan gitlab.nic.cz > /root/.ssh/known_hosts

CMD [ "bash" ]
