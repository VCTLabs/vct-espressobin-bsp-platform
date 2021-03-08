======================================
 VCT Marvell espressobin BSP Manifest
======================================

The various branches (other than this one) available here will configure the repo build
for the appropriate branches in each repository and clone them in the typical fashion,
ie, with either poky or oe-core as the parent directory and the additional metadata
layers underneath (as documented in the upstream setup).

The main branches build either the poky reference distro or oe-core with your choice
of distro.  Note that oe-core will build "distroless" by default, however, you can set
DISTRO = "vctlabs" in your local.conf if you want the mainline kernel to be the default.
See the `arm64-multiplatform`_ repo for the mainline kernel branches tested, and the
espessobin page on the `Gentoo wiki`_ for more info.

.. _Gentoo wiki: https://wiki.gentoo.org/wiki/ESPRESSOBin
.. _arm64-multiplatform: https://github.com/sarnold/arm64-multiplatform

espressobin variants
--------------------

There are basically 3 different espressobin hardware variants:

* espressobin - v5 (machine name either espressobin or espressobin-v5)
* espressobin - v7 (machine name espressobin-v7, vendor only)
* espressobin - ultra (machine name espressobin-ultra)

The actual names of device tree files and u-boot configs vary, mainly depending
on whether kernel/u-boot comes from mainline or vendor forks.  The ``v5/v7``
boards look almost identical (the main difference is essentially DDR size/timing)
while the ``ultra`` variant adds additional hardware/peripherals.

* mainline u-boot - espressobin(v5) support, use mvebu_espressobin-88f3720_defconfig
* mainline kernel - at least basic device tree and driver support (all 3 variants)

The "factory" u-boot is a 2017 version of Marvell's u-boot fork, and should
work fine with a mainline kernel as long as their devive tree blob and kernel
name appear in the ``boot/`` directory::

  # ls -l /boot/
  total 28480
  lrwxrwxrwx 1 root root       25 Mar  8 00:44 Image -> vmlinuz-4.14.3-aarch64-r0
  lrwxrwxrwx 1 root root       58 Mar  8 00:44 armada-3720-community.dtb -> dtbs/4.14.3-aarch64-r0/marvell/armada-3720-espressobin.dtb
  drwxr-xr-x 4 root root     4096 Mar  8 00:46 dtbs
  -rwxr-xr-x 1 root root 14567936 Jan 26  2018 vmlinuz-4.13.12-aarch64-r0
  -rwxr-xr-x 1 root root 14656000 Mar  8 00:18 vmlinuz-4.14.3-aarch64-r0


Build branches
--------------

There are 3 main branches for each of the above choices: dunfell, rocko, and master.
Select the main build branch using the github branch button above, which will select the
correct manifest branches and BSP/metadata using the respective branches in this
repo as shown below.

Follow the steps in the readme for the branch you select, then use the same branch
name with the "repo init" command.  This will checkout the
corresponding branch HEAD commits on the configured branch for each repository.

You can get status and more using the ``repo`` command in the top level directory
once you've run the ``repo init`` and ``repo sync`` commands.  Try::

  $ repo info
  $ repo show
  $ repo status

See the default.xml file for repo and branch details; the following example is generic
so go back and select a branch from this repo.

Install the repo utility
------------------------

::

  $ mkdir ~/bin
  $ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
  $ chmod a+x ~/bin/repo

Download the BSP source
-----------------------

::

  $ PATH=${PATH}:~/bin
  $ mkdir espressobin-bsp
  $ cd espressobin-bsp
  $ repo init -u https://github.com/VCTLabs/vct-espressobin-bsp-platform -b oe-dunfell
  $ repo sync

At the end of the above commands you have all the metadata you need to start
building with poky and meta-oe on fido branches.

To start a simple image build for a espressobin Devices iMX6 nitrogen board::

  $ cd oe-core
  $ source ./oe-init-build-env build-dir  # you choose name of build-dir
  $ ${EDITOR} conf/local.conf             # set MACHINE to espressobin
  $ bitbake core-image-minimal

You can use any directory (build-dir above) to host your build. The above
commands will build an image for espressobin using the espressobin BSP
machine config and the default marvell-linux kernel.

For espressobin boards, you can replace the default marvell kernel with
a patched mainline kernel; see the kernel branches in `arm64-multiplatform`_
(note new baords will be added as this manifest evolves).

The main source code is checked out in the bsp dir above, and the build
output dir will default to oe-core/build-dir unless you choose a different
path above.

Source code
-----------

Download the manifest source here::

  $ git clone https://github.com/VCTLabs/vct-espressobin-bsp-platform

Using Development and Testing/Release Branches
----------------------------------------------

Replace the repo init command above with one of the following:

For developers - dunfell

::

  $ repo init -u https://github.com/VCTLabs/vct-beagleboard-bsp-platform -b oe-dunfell

For intrepid developers and testers - master

Patches are typically merged into master-next and then are merged into master
after a testing and comment period. It’s possible that master has
something you want or need.  But it’s also possible that using master
breaks something that was working before.  Use with caution.

::

  $ repo init -u https://github.com/VCTLabs/vct-beagleboard-bsp-platform -b oe-master


