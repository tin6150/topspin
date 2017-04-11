#$ --size 3300
 
# CentOS 7 x86_64 container for Bruker Topspin
# This container currently does *NOT* contain the topsin sw itself!
# It only has all the needed dependencies, where the app can be installed
# manually via the GUI installer.
# recommended to create this image with 3200 MB to include all the fonts
 
# Remember to adjust UID and GID numbers to your need 

BootStrap:docker
From:centos:7

#yum bootstrap works from a CentOS host, but not in Singularity-hub ~2017-04
#BootStrap: yum
#OSVersion: 7

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
    yum -y install vte-profile		
    yum -y install environment-modules
    yum -y install which
    yum -y install less
    yum -y install wget
    yum -y install coreutils
    yum -y install bzip2
    yum -y install tar
    yum -y install gzip
    yum -y install vi
    yum -y install util-linux-ng        
    yum -y install openssh-clients     
    yum -y install xterm

    yum -y --nogpgcheck install ftp://ftp.bruker.de/pub/nmr/CentOS/7/Bruker-Addon/bruker-addon-latest.x86_64.rpm
    yum -y install epel-release
    yum -y install --enablerepo=bruker   bruker-topspin-environ

    mkdir -p /export/home
    groupadd -g $STRUCTBIO_GID sbio
    groupadd -g $NMRSU_GID     nmrsu
    groupadd -g $NMR_GID       nmr
    groupadd -g $FLEXLM_GID    flexlm
    useradd -d /export/home/nmrsu -m  -c "nmr super user"     -s /bin/bash -p '*place*your*shadow*entry*here*' -g $NMRSU_GID  -u $NMRSU_UID  nmrsu
    useradd -d /export/home/nmr   -m  -c "nmr user"           -s /bin/bash -p '*place*your*shadow*entry*here*' -g $NMR_GID    -u $NMR_UID    nmr
    useradd -d /nonexistent       -m  -c "FLEXlm License Mgr" -s /bin/bash -p '*LK*no*login*'                  -g $FLEXLM_GID -u $FLEXLM_UID flexlm
    # optional step -- Install additional fonts, without them 
    wget --random-file=/var/log/lastlog -nc https://raw.githubusercontent.com/tin6150/singhub/master/fonts_addition.sh
    bash fonts_addition.sh
    # next step is to run the topspin interactive install as root inside the container
    # not included cuz it is commercial sw req license, and it is interactive GUI install.
    echo "See http //github.com/tin6150/singhub/start_topspin_from_container.sh for example on starting topspin" > /opt/run_topspin.cmd
