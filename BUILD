apt install git wget curl snapd debootstrap

cd /

git clone https://github.com/lxc/lxc-ci

wget https://github.com/the-guong/debian-ifupdown2-lxc/raw/main/debian.yaml -O /lxc-ci/images/debian.yaml

snap install distrobuilder --classic

export PATH=/snap/bin:$PATH

mkdir ./build


distrobuilder build-lxc /lxc-ci/images/debian.yaml ./build -o image.architecture=arm64 -o image.release=bookworm -o image.variant=default -o source.url=http://ftp.us.debian.org/debian

