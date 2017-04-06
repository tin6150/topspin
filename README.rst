CentOS 7 x86_64 container for Bruker Topspin
This container currently does *NOT* contain the topsin sw itself!
It only has all the needed dependencies, where the app can be installed
manually via the GUI installer.
Will check with Bruker to see if they are okay for their app to be included.


Creating containers (if not using Singularity Hub):

Singularity=/usr/local/bin/singularity
Singularity=/export/singularity/2.3/bin/singularity
sudo    $Singularity create --size 3200 topspin.img
sudo -E $Singularity bootstrap topspin.img Singularity | tee sing_log.txt 2>&1 


Ref:

- https://github.com/tin6150/singhub/centos7_topspin.def
- https://singularity-hub.org/collections/104/

