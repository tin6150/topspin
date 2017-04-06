#$ --size 3300
#$ --debug
#
# CentOS 7 x86_64 container for Bruker Topspin
# This container currently does *NOT* contain the topsin sw itself!
# It only has all the needed dependencies, where the app can be installed
# manually via the GUI installer.
# Will check with Bruker to see if they are okay for their app to be included.
#
# For example command to start topspin with this container, see
# https //github.com/tin6150/singhub/start_topspin_from_container.sh
# 
# Adopted from https //github.com/singularityware/singularity/blob/master/examples/centos.def
# def for container intended to hold NMR sw topspin 
# this container can be bootstrapped from centos6
# build into a centos7 container, and utilize all the necessary rhel7-level rpm from yum.
# resulting topspin sw in this container works well in a centos 6 workstation
# 
## RPM (Berkeley) DB is okay, inside the centos7 container, yum works.
## manually yum install eog adds 81 rpm, 42 MB of space, don't seems to replicate a 
## whole new DB for rpm.  Centos7 rpm can read what was created from RHEL6.6 (bootstrap host).
# 
# ftp //ftp.bruker.de/pub/nmr/CentOS/7/TopSpinInstallationRequirements.html
#
# this .def file result in 1.3 GB image
# topspin sw install will subsequently takes a lot of space.
# recommended to create this image with 3200 MB to include all the fonts
#
#
# *** Remember to adjust UID and GID numbers to your need ***

BootStrap: yum
OSVersion: 7

MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum

%runscript
    cat /opt/run_topspin.cmd 

%post
    STRUCTBIO_GID=8000
    NMRSU_UID=8001
    NMRSU_GID=8001
    NMR_UID=8002
    NMR_GID=8002
    FLEXLM_UID=8003
    FLEXLM_GID=8003
    yum -y install bash
    yum -y install vte-profile		# so that it properly parse $PROMPT_COMMAND passed from a centos7 host
    yum -y install environment-modules
    # source /etc/profile.d/modules.sh # to get the "module command" to work
    yum -y install which
    yum -y install less
    yum -y install wget
    yum -y install coreutils
    yum -y install bzip2
    yum -y install tar
    yum -y install gzip
    yum -y install vi
    yum -y install util-linux-ng        # u/mount, etc
    yum -y install openssh-clients      # to get scp
    yum -y install xterm

    yum -y --nogpgcheck install ftp://ftp.bruker.de/pub/nmr/CentOS/7/Bruker-Addon/bruker-addon-latest.x86_64.rpm
    yum -y install epel-release
    yum -y install --enablerepo=bruker   bruker-topspin-environ

    mkdir /nonexistent
    mkdir -p /export/home
    groupadd -g $STRUCTBIO_GID sbio
    groupadd -g $NMRSU_GID     nmrsu
    groupadd -g $NMR_GID       nmr
    groupadd -g $FLEXLM_GID    flexlm
    useradd -d /export/home/nmrsu -m  -c "nmr super user"     -s /bin/bash -p '*place*your*shadow*entry*here*' -g $NMRSU_GID  -u $NMRSU_UID  nmrsu
    useradd -d /export/home/nmr   -m  -c "nmr user"           -s /bin/bash -p '*place*your*shadow*entry*here*' -g $NMR_GID    -u $NMR_UID    nmr
    useradd -d /nonexistent       -m  -c "FLEXlm License Mgr" -s /bin/bash -p '*LK*no*login*'                  -g $FLEXLM_GID -u $FLEXLM_UID flexlm

    # optional step -- Install additional fonts, without them topspin menu looks kinda ugly
    # done as add-on script as other container to host GUI program may use it as well
    wget -nc https://raw.githubusercontent.com/tin6150/singhub/master/fonts_addition.sh
    bash fonts_addition.sh

    # next step is to run the topspin interactive install as root inside the container
    # not included cuz it is commercial sw req license, and it is interactive GUI install.

    echo "
    # To run topspin, execute the following from the host \
    # (replacing equals with colons, tmp work around on potential singularity bug) \
\
        HOST_BASE_DIR=/prog/structchem/topspin_singularity \
        CONTAINER_DIR=/opt/topsping3.5.b.85pl7 \
\
        singularity exec -B \
          ${HOST_BASE_DIR}/user=${CONTAINER_DIR}/prog/curdir,\
          ${HOST_BASE_DIR}/logfiles=${CONTAINER_DIR}/prog/logfiles,\
          ${HOST_BASE_DIR}/au_script=${CONTAINER_DIR}/exp/stan/nmr/au/src/user,\
          ${HOST_BASE_DIR}/py_script=${CONTAINER_DIR}/exp/stan/nmr/py/user,\
          ${HOST_BASE_DIR}/conf_global=${CONTAINER_DIR}/conf/global,\
          ${HOST_BASE_DIR}/nonexistent=/nonexistent,\
        topspin.img   ${CONTAINER_DIR}/topspin
     " > /opt/run_topspin.cmd
