.. _doc_krill_installation:

Installation
============

Getting started with Krill is quite easy either building from Cargo or running
with Docker. In case you intend to serve your RPKI certificate and ROAs to the
world yourself or you want to offer this as a service to others, you will also
need to have a public Rsyncd and HTTPS web server available.

Krill can also be se up as a highly available, scalable service using
:ref:`doc_krill_manager`.  A 1-Click App on the DigitalOcean Marketplace can set
up Krill with all required components, along with integration points for
monitoring and log analysis.

Quick Start
-----------

Assuming you have a newly installed Debian or Ubuntu machine, you will need to
install the C toolchain, OpenSSL, curl and Rust. You can then install Krill
using Cargo.

After the installation has completed, first create a data directory in a
location of your choice. Next, generate a basic configuration file specifying a
`secret token <https://xkcd.com/936/>`_ and make sure to refer to the data
directory you just created. Finally, start Krill pointing to your configuration
file.

.. code-block:: bash

  apt install build-essential libssl-dev openssl pkg-config curl
  curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
  source ~/.cargo/env
  cargo install krill
  mkdir ~/data
  krillc config simple --token correct-horse-battery-staple --data ~/data/ > ~/data/krill.conf
  krill --config ~/data/krill.conf

Krill now exposes its user interface and API on ``https://localhost:3000``
using a self-signed TLS certificate. You can go to this address in a web
browser, accept the certificate warning and start configuring your RPKI
Certificate Authority. A Prometheus endpoint is available at ``/metrics``.

If you have an older version of Rust and Krill, you can update via:

.. code-block:: bash

   rustup update
   cargo install -f krill

If you want to try the master branch from the repository instead of a
release version, you can run:

.. code-block:: bash

   cargo install --git https://github.com/NLnetLabs/krill.git

.. Note:: Using a fully qualified domain name, configuring a real TLS
          certificate such as Let's Encrypt, running on a different port and
          exposing Krill securely to other machines is all possible, but goes
          beyond the scope of this Quick Start.

Installing with Cargo
---------------------

There are three things you need for Krill: Rust, a C toolchain and OpenSSL.
You can install Krill on any Operating System where you can fulfil these
requirements, but we will assume that you will run this on a UNIX-like OS.

Rust
""""

The Rust compiler runs on, and compiles to, a great number of platforms,
though not all of them are equally supported. The official `Rust
Platform Support <https://forge.rust-lang.org/platform-support.html>`_
page provides an overview of the various support levels.

While some system distributions include Rust as system packages,
Krill relies on a relatively new version of Rust, currently 1.40 or
newer. We therefore suggest to use the canonical Rust installation via a
tool called :command:`rustup`.

To install :command:`rustup` and Rust, simply do:

.. code-block:: bash

   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

Alternatively, visit the `official Rust website
<https://www.rust-lang.org/tools/install>`_ for other installation methods.

You can update your Rust installation later by running:

.. code-block:: bash

   rustup update

For some platforms, :command:`rustup` cannot provide binary releases to install
directly. The `Rust Platform Support
<https://forge.rust-lang.org/platform-support.html>`_ page lists
several platforms where official binary releases are not available,
but Rust is still guaranteed to build. For these platforms, automated
tests are not run so it’s not guaranteed to produce a working build, but
they often work to quite a good degree.

One such example that is especially relevant for the routing community
is OpenBSD. On this platform, `patches
<https://github.com/openbsd/ports/tree/master/lang/rust/patches>`_ are
required to get Rust running correctly, but these are well maintained
and offer the latest version of Rust quite quickly.

Rust can be installed on OpenBSD by running:

.. code-block:: bash

   pkg_add rust

Another example where the standard installation method does not work is
CentOS 6, where you will end up with a long list of error messages about
missing assembler instructions. This is because the assembler shipped with
CentOS 6 is too old.

You can get the necessary version by installing the `Developer Toolset 6
<https://www.softwarecollections.org/en/scls/rhscl/devtoolset-6/>`_ from the
`Software Collections
<https://wiki.centos.org/AdditionalResources/Repositories/SCL>`_ repository. On
a virgin system, you can install Rust using these steps:

.. code-block:: bash

   sudo yum install centos-release-scl
   sudo yum install devtoolset-6
   scl enable devtoolset-6 bash
   curl https://sh.rustup.rs -sSf | sh
   source $HOME/.cargo/env

C Toolchain
"""""""""""

Some of the libraries Krill depends on require a C toolchain to be
present. Your system probably has some easy way to install the minimum
set of packages to build from C sources. For example,
:command:`apt install build-essential` will install everything you need on
Debian/Ubuntu.

If you are unsure, try to run :command:`cc` on a command line and if there’s a
complaint about missing input files, you are probably good to go.

OpenSSL
"""""""
Your system will likely have a package manager that will allow you to install
OpenSSL in a few easy steps. For Krill, you will need :command:`libssl-dev`,
sometimes called :command:`openssl-dev`. On Debian-like Linux distributions,
this should be as simple as running:

.. code-block:: bash

    apt install libssl-dev openssl pkg-config

Building
--------

The easiest way to get Krill is to leave it to cargo by saying:

.. code-block:: bash

   cargo install krill

If you want to try the master branch from the repository instead of a
release version, you can run:

.. code-block:: bash

   cargo install --git https://github.com/NLnetLabs/krill.git

If you want to update an installed version, you run the same command but
add the ``-f`` flag, a.k.a. force, to approve overwriting the installed
version.

The command will build Krill and install it in the same directory
that cargo itself lives in, likely :file:`$HOME/.cargo/bin`. This means
Krill will be in your path, too.
