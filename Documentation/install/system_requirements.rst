.. only:: not (epub or latex or html)

    WARNING: You are looking at unreleased Cilium documentation.
    Please use the official rendered version released here:
    http://docs.cilium.io

.. _admin_system_reqs:

*******************
System Requirements
*******************

Before installing Cilium, please ensure that your system meets the minimum
requirements below. Most modern Linux distributions already do.

Summary
=======

When running Cilium using the container image ``cilium/cilium``, the host
system must meet these requirements:

- `Linux kernel`_ >= 4.9.17
- :ref:`req_kvstore` etcd >= 3.1.0 or consul >= 0.6.4

When running Cilium as a native process on your host (i.e. **not** running the
``cilium/cilium`` container image) these additional requirements must be met:

- `clang+LLVM`_ >=3.7.1
- iproute2_ >= 4.8.0

.. _`clang+LLVM`: https://llvm.org
.. _iproute2: https://www.kernel.org/pub/linux/utils/net/iproute2/


======================== =============== ===================
Requirement              Minimum Version In cilium container
======================== =============== ===================
`Linux kernel`_          >= 4.9.17       no
Key-Value store (etcd)   >= 3.1.0        no
Key-Value store (consul) >= 0.6.4        no
clang+LLVM               >= 3.7.1        yes
iproute2                 >= 4.8.0        yes
======================== =============== ===================

Linux Distribution Compatibility Matrix
=======================================

The following table lists Linux distributions that are known to work
well with Cilium.

===================== ====================
Distribution          Minimum Version
===================== ====================
CoreOS_               stable (>= 1298.5.0)
Debian_               >= 9 Stretch
`Fedora Atomic/Core`_ >= 25
LinuxKit_             all
Ubuntu_               >= 16.04.2, >= 16.10
Opensuse_             Tumbleweed, >=Leap 15.0
===================== ====================

.. _CoreOS: https://coreos.com/releases/
.. _Debian: https://wiki.debian.org/DebianStretch
.. _Fedora Atomic/Core: http://www.projectatomic.io/blog/2017/03/fedora_atomic_2week_2/
.. _LinuxKit: https://github.com/linuxkit/linuxkit/tree/master/kernel
.. _Ubuntu: https://wiki.ubuntu.com/YakketyYak/ReleaseNotes#Linux_kernel_4.8
.. _Opensuse: https://www.opensuse.org/

.. note:: The above list is based on feedback by users. If you find an unlisted
          Linux distribution that works well, please let us know by opening a
          GitHub issue or by creating a pull request that updates this guide.

.. _admin_kernel_version:

Linux Kernel
============

Cilium leverages and builds on the kernel BPF functionality as well as various
subsystems which integrate with BPF. Therefore, host systems are required to
run Linux kernel version 4.8.0 or later to run a Cilium agent. More recent
kernels may provide additional BPF functionality that Cilium will automatically
detect and use on agent start.

In order for the BPF feature to be enabled properly, the following kernel
configuration options must be enabled. This is typically the case  with
distribution kernels. When an option can be built as a module or statically
linked, either choice is valid.

.. code:: bash

        CONFIG_BPF=y
        CONFIG_BPF_SYSCALL=y
        CONFIG_NET_CLS_BPF=y
        CONFIG_BPF_JIT=y
        CONFIG_NET_CLS_ACT=y
        CONFIG_NET_SCH_INGRESS=y
        CONFIG_CRYPTO_SHA1=y
        CONFIG_CRYPTO_USER_API_HASH=y

.. note::

   Users running Linux 4.10 or earlier with Cilium CIDR policies may face
   :ref:`cidr_limitations`.

.. _req_kvstore:

Key-Value store
===============

Cilium uses a distributed Key-Value store to manage, synchronize and distribute
security identities across all cluster nodes. The following Key-Value stores
are currently supported:

- etcd >= 3.1.0
- consul >= 0.6.4

See :ref:`install_kvstore` for details on how to configure the
``cilium-agent`` to use a Key-Value store.

clang+LLVM
==========


.. note:: This requirement is only needed if you run ``cilium-agent`` natively.
          If you are using the Cilium container image ``cilium/cilium``,
          clang+LLVM is included in the container image.

LLVM is the compiler suite that Cilium uses to generate BPF bytecode programs
to be loaded into the Linux kernel. The minimum supported version of LLVM
available to ``cilium-agent`` should be >=3.7.1. The version of clang installed
must be compiled with the BPF backend enabled.

See https://releases.llvm.org/ for information on how to download and install
LLVM.

.. note:: Beginning with clang 3.9.x, the minimum kernel version is >= 4.9.17.

iproute2
========

.. note:: iproute2 is only needed if you run ``cilium-agent`` directly on the
          host machine. iproute2 is included in the ``cilium/cilium`` container
          image.

iproute2 is a low level tool used to configure various networking related
subsystems of the Linux kernel. Cilium uses iproute2 to configure networking
and ``tc``, which is part of iproute2, to load BPF programs into the kernel.

The minimum version of iproute2_ must be >= 4.8.0. Please see
https://www.kernel.org/pub/linux/utils/net/iproute2/ for documentation on how
to install iproute2.
