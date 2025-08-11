# 3.8-container-arm64
# CRUX for ARMv8 (64-bit architecture)

# Stage 1: Build dependencies
FROM git.crux.nu/system/crux-arm:3.8-updated

# Prepare volume
VOLUME ["/usr/ports/"]

# Prepare collections needed for dependencies
RUN sed -e "s|#prtdir /usr/ports/contrib|prtdir /usr/ports/contrib|g" \
	-e '/runscripts/s/.*/runscripts yes/' \
	-i /etc/prt-get.conf
RUN mv /etc/ports/contrib.rsync.inactive /etc/ports/contrib.rsync

# Prepare sudo
RUN echo "Setting up sudoers.d/nopasswd" ; \
	mkdir -p /etc/sudoers.d ; \
	echo "%wheel ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/nopasswd ; \
	chmod 0440 /etc/sudoers.d/nopasswd

# Prepare pkgmk and prt-get
RUN echo "Preparing pkgmk" ; \
	sed \
	  -e 's/# export JOBS.*/export JOBS=$(nproc)/g' \
	  -e 's/# export MAKEFLAGS.*/export MAKEFLAGS="-j $JOBS"/g' \
	  -e 's/# PKGMK_IGNORE_NEW="no"/PKGMK_IGNORE_NEW="yes"/' \
	  -e 's/# PKGMK_COMPRESSION_MODE="gz"/PKGMK_COMPRESSION_MODE="xz"/' \
	  -e 's/# PKGMK_IGNORE_MD5SUM="no"/PKGMK_IGNORE_MD5SUM="yes"/' \
	  -e 's!# PKGMK_SOURCE_DIR="$PWD"!PKGMK_SOURCE_DIR=/home/pkgmk/distfiles!' \
	  -e 's!# PKGMK_PACKAGE_DIR="$PWD"!PKGMK_PACKAGE_DIR=/home/pkgmk/packages!' \
	  -e 's!# PKGMK_WORK_DIR="$PWD/work"!PKGMK_WORK_DIR=/home/pkgmk/work/${PWD##*/}!' \
	  -i /etc/pkgmk.conf

# Create pkgmk user
RUN /usr/bin/getent group pkgmk || /usr/sbin/groupadd pkgmk
RUN /usr/bin/getent passwd pkgmk || /usr/sbin/useradd -m -g pkgmk pkgmk

# Prepare its home directory
RUN mkdir -vp /home/pkgmk/distfiles
RUN mkdir -vp /home/pkgmk/packages
RUN mkdir -vp /home/pkgmk/work
RUN chown -R pkgmk:pkgmk /home/pkgmk/
RUN chmod -R 775 /home/pkgmk/

# Uncomment and adapt to fit your needs
# Create a normal user
#RUN /usr/bin/getent passwd pitillo || /usr/sbin/useradd -m -G wheel,pkgmk -s /bin/bash pitillo

# Prepare personal collection
#COPY ./ports/pitillo /usr/ports/pitillo

# Update ports
RUN ports -u core opt xorg contrib

# Prepare the environment to build ports as a non root user
# First build fakeroot
RUN prt-get depinst fakeroot

# Prepare prt-get.conf to use this user for building ports
RUN echo "makecommand sudo -H -u pkgmk fakeroot pkgmk" >> /etc/prt-get.conf

# Sync ports
RUN if [[ ! -d "/usr/ports/core-arm64" ]]; then cd /usr/ports/ ; git clone https://github.com/crux-arm/crux-ports-core-arm64.git core-arm64; cd core-arm64; git checkout 3.8; fi
RUN if [[ ! -d "/usr/ports/opt-arm64" ]]; then cd /usr/ports/ ; git clone https://github.com/crux-arm/crux-ports-opt-arm64.git opt-arm64; cd opt-arm64; git checkout 3.8; fi
RUN if [[ ! -d "/usr/ports/xorg-arm64" ]]; then cd /usr/ports/ ; git clone https://github.com/crux-arm/crux-ports-xorg-arm64.git xorg-arm64; cd xorg-arm64; git checkout 3.8; fi

# Copy ports to the image
COPY ./ports/core-arm64 /usr/ports/core-arm64
COPY ./ports/opt-arm64 /usr/ports/opt-arm64
COPY ./ports/xorg-arm64 /usr/ports/xorg-arm64

# prt-snapshot logic (sync, setup and store initial state)
# You can set it up getting this port from my personal collection or directly from github:
# https://github.com/pitill0/prt-snapshot
#RUN cd /usr/ports/pitillo/prt-snapshot; fakeroot pkgmk -d -is; pkgadd /home/pkgmk/packages/prt-snapshot*.tar.xz; prt-snapshot store "Initial status"

# Install prtup dependencies (Control clean builds on the container, Tim's development)
#RUN prt-get depinst git python3-build python3-installer python3-wheel prt-utils

# Cleanup
RUN rm -rf /home/pkgmk/distfiles/* /home/pkgmk/packages/*

# Build prtup (I have my own copy of prtup)
#RUN cd /usr/ports/pitillo/prtup && pkgmk -d -is

# set the entrypoint to bash
ENTRYPOINT ["/bin/bash"]
