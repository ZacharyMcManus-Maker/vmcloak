VMCloak
=======

Automated Virtual Machine Generation and Cloaking for Cuckoo Sandbox.

Introduction
------------

VMCloak is a tool to fully create and prepare Virtual Machines that can be
used by Cuckoo Sandbox. In order to create a new Virtual Machine one should
prepare a few configuration values that will be used later on by the tool.

Dependencies
------------

In order to run VMCloak you'll at the very least require the following:

* Python 3.6 or higher.
* mkisofs *or* genisoimage.
* QEMU 2.11 or higher (tested with 4.2.1).
* **root** access to mount images and perform actions such as creating network bridges.


Installation
------------

It is recommended that VMCloak is installed in a Virtualenv and on the user
that should own the created virtual machines.

**Do not use the PyPI-version of VMCloak. It is outdated and heavily bugged.**

Install the requirements. See the docs/ for a full list of requirements.

```bash
$ sudo apt update
$ sudo apt install python3 genisoimage qemu-system-x86 qemu-utils qemu-system-common
```


Open Terminal and run the following commands
```bash
sudo apt-get install -y build-essential \
    software-properties-common \
    unzip \
    curl \
    git \
    python3.10 python3.10-dev python3.10-venv \
    libhyperscan5 libhyperscan-dev \
    libjpeg8-dev zlib1g-dev p7zip-full rar unace-nonfree cabextract \
    yara \
    tcpdump \
    libssl-dev libcapstone-dev \
    uwsgi uwsgi-plugin-python3 \
    nginx \
    genisoimage qemu-system-common qemu-utils qemu-system-x86

cd ~ && git clone https://github.com/ZacharyMcManus-Maker/vmcloak.git 
cd vmcloak
python3.10 -m venv venv
source venv/bin/activate
python3.10 -m pip install .

vmcloak isodownload --win10x64 --download-to /home/cuckoo/win10x64.iso
sudo /home/cuckoo/vmcloak/bin/vmcloak-qemubridge br0 192.168.30.1/24
sudo mkdir -p /etc/qemu/ && echo "allow br0" | sudo tee /etc/qemu/bridge.conf
sudo chmod u+s /usr/lib/qemu/qemu-bridge-helper
sudo mkdir -p /mnt/win10x64 && sudo mount -o loop,ro /home/cuckoo/win10x64.iso /mnt/win10x64
sudo adduser cuckoo kvm && sudo chmod 666 /dev/kvm

vmcloak --debug init --win10x64 --hddsize 128 --cpus 2 --ramsize 4096 --network 192.168.30.0/24 --vm qemu --ip 192.168.30.2 --iso-mount /mnt/win10x64 win10base br0 --vm-visible
vmcloak --debug install win10base --recommended --vm-visible
vmcloak --debug snapshot --count 1 win10base win10vm_ 192.168.30.10 --vm-visible
```

It is recommended to install VMCloak in a virtualenv.

Fetching the [Git repository](https://github.com/Cryss76/vmcloak) is the way to go.
There you get the latest working version with the least bugs.
A full example of installing VMCloak manually can be
as follows:

```bash
$ (venv) git clone https://github.com/Cryss76/vmcloak.git
$ (venv) cd vmcloak
$ (venv) pip install .
```

Docs
----

```bash
# For zsh use \[ and \]
$ (venv) pip install -e .[docs]
$ (venv) cd docs
$ (venv) make html
$ (venv) <your browser>/_build/html/index.html
```

<!--
Usage
-----

For a quick walkthrough on the general usage of VMCloak, please refer to the
following blogpost: [http://jbremer.org/vmcloak3][blogpost].

[blogpost]: http://jbremer.org/vmcloak3

--->
<!---
Testing
-------

In order to run the VMCloak unit tests, which should be run on a custom build
server due to its huge resource requirements (i.e., setting up multiple
virtual machines is not something to take lightly), we provide some pointers
for setting up such an environment.

First of all, the _~/.vmcloak/config.json_ should be created containing a JSON
blob with, currently, one value. The _winxp.serialkey_ value should be
featured with a serial key that matches your Windows XP ISO file. An example
config.json file may look as follows.

```javascript
{
    "winxp": {
        "serialkey": "windows xp serial key here"
    }
}
```

Then install _pytest_ and _pytest-xdist_:

```bash
$ pip install -U pytest pytest-xdist
```

Mount all of the ISO files as required, for a default configuration this looks
as follows (the following commands should be run as _root_ user):

```bash
$ mkdir /mnt/winxp
$ mount -o loop,ro vms/winxppro.iso /mnt/winxp

$ mkdir /mnt/win7x64
$ mount -o loop,ro vms/win7ultimate.iso /mnt/win7x64

$ mkdir /mnt/win81x64
$ mount -o loop,ro vms/Win8.1_EnglishInternational_x64.iso /mnt/win81x64

$ mkdir /mnt/win10x64
$ mount -o loop,ro vms/Win10_1511_2_EnglishInternational_x64.iso /mnt/win10x64
```

Now we're going to run the actual unit tests. Note that we can speed them up
by specifying N unit tests to be ran in parallel. As most of the tests
actually install Windows or run a virtual machine, we recommend to run at most
one unit test per CPU core. Also reserve about two to four gigabytes of RAM
for each extra unit test in parallel. E.g., if you want to run four unit tests
in parallel, then your computer should have at least four CPU cores and 16GB
of RAM.

Finally run the unit tests:

```bash
py.test -n 8
```
--->
Credits
-------

The development of the VMCloak project initially started out as part of the
ITES Project at Avira, thanks to **Thorsten Sick**. Many thanks to
**Rasmus Männa** for lots of great contributions lately.


<!---
[![Coverage Status](https://coveralls.io/repos/github/jbremer/vmcloak/badge.svg)](https://coveralls.io/github/jbremer/vmcloak)
--->
