This repo introduces support the bundling/delivering shadowsocks/ChinaDNS
through the 'snap' mechanism/store - for more details see https://snapcraft.io.

"A snap is a bundle of your app and its dependencies that works without 
modification across Linux. Snaps are discoverable and installable from the 
Snap store, an app store with an audience of millions." [1]

In case the support/snapcraft.yaml file is accepted it can be published in the 
Snap Store [2] which is required for people to easily install it.

## Build/Snap
$ sudo apt install -y build-essential squashfuse
$ sudo snap install --classic snapcraft
$ git clone https://github.com/shadowsocks/chinadns-snap.git
$ cd chinadns-snap
$ snapcraft
Installing build dependencies: autoconf automake autopoint autotools-dev libltdl-dev libltdl7 libtool m4
...
Pulling chinadns 
Cloning into '/home/ubuntu/chinadns-snap/parts/chinadns/src'...
...
Building chinadns 
env NOCONFIGURE=1 ./autogen.sh
configure.ac:12: installing './compile'
configure.ac:9: installing './install-sh'
configure.ac:9: installing './missing'
src/Makefile.am: installing './depcomp'
./configure --prefix=
...
make -j4
...
Staging chinadns 
Priming chinadns 
Snapping 'chinadns' |                                                                                          
Snapped chinadns_master_amd64.snap

## Install/Contents
$ sudo snap install ./chinadns_master_amd64.snap --dangerous --classic
chinadns master installed

$ which chinadns 
/snap/bin/chinadns

$ find /snap/chinadns/
/snap/chinadns/
/snap/chinadns/current
/snap/chinadns/x1
/snap/chinadns/x1/bin
/snap/chinadns/x1/bin/chinadns
/snap/chinadns/x1/command-chinadns.wrapper
/snap/chinadns/x1/meta
/snap/chinadns/x1/meta/gui
/snap/chinadns/x1/meta/snap.yaml
/snap/chinadns/x1/share
/snap/chinadns/x1/share/chnroute.txt
/snap/chinadns/x1/share/iplist.txt

## Testing
Run chinadns in a shell:
```
$ sudo -i
# curl 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | grep ipv4 | grep CN | awk -F\| '{ printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > ~/chnroute.txt
# chinadns -m -c ~/chnroute.txt -s 114.114.114.114,8.8.8.8 -p 5354 -v
Fri Dec 14 14:53:48 2018 request google.com
Fri Dec 14 14:53:48 2018 response google.com from 114.114.114.114:53 - 216.58.197.110, filter
Fri Dec 14 14:53:48 2018 response google.com from 8.8.8.8:53 - 216.58.197.110, pass
Fri Dec 14 14:53:48 2018 response google.com from 114.114.114.114:53 - 216.58.200.14, filter
Fri Dec 14 14:53:48 2018 response google.com from 8.8.8.8:53 - 172.217.160.110, pass
```
then test chinadns in another shell, it looks like:
```
$ dig google.com @127.0.0.1 -p 5354

; <<>> DiG 9.11.3-1ubuntu1.2-Ubuntu <<>> google.com @127.0.0.1 -p 5354
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51220
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		38446	IN	A	216.58.197.110

;; ADDITIONAL SECTION:
caishikou.redirect.	3600	IN	A	127.0.0.1

;; Query time: 24 msec
;; SERVER: 127.0.0.1#5354(127.0.0.1)
;; WHEN: Fri Dec 14 14:53:48 CST 2018
;; MSG SIZE  rcvd: 78
```

## Reference
[1] "Creating a snap" in https://docs.snapcraft.io/creating-a-snap/6799
[2] "Share with your friends" in https://docs.snapcraft.io/c-c-applications/7817
[3] "Demystifying Snap Confinement" in https://snapcraft.io/blog/demystifying-snap-confinement
[4] "Commands and aliases" in https://docs.snapcraft.io/commands-and-aliases/3950

