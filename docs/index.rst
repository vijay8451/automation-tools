automation-tools documentation
==============================

.. toctree::
    :hidden:

    api

A set of tools to help automating virtual machines to install Foreman and test
it using `Robottelo <https://github.com/SatelliteQE/robottelo>`_.

Installation
============

Install the following packages on your Operating System::

    sudo yum -y install libcurl-devel libxml-devel libxslt-devel

Automation tools depends on ``pycurl`` being installed, but installing it,
specially on a virtual environment, is not straight forward. You must run the
following script in order to have ``pycurl`` installed properly::

    # Make sure pip is updated
    pip install -U pip

    # Install pycurl using the proper cryptography library
    if [ "$(curl --version | grep NSS 2>/dev/null)" ]; then
        pip install --compile --install-option="--with-nss" pycurl
    else
        pip install --compile --install-option="--with-openssl" pycurl
    fi

Finally, python packages listed in `requirements.txt` must be installed before
automation-tools can be used::

    pip install -r requirements.txt

If you prefer, you can install both ``pycurl`` and automation-tools
`requirements.txt` running just one command::

    PYCURL_SSL_LIBRARY=$(curl -V | sed -n 's/.*\(NSS\|OpenSSL\).*/\L\1/p') \
        pip install -r requirements.txt

Usage examples
==============

Virtual Machine Management
--------------------------

To create a virtual machine will be needed a base image, to list all available
base images::

    fab -H root@example.com vm_list_base

Creating a virtual machine::

    VM_RAM=512 VM_CPU=1 VM_DOMAIN=domain.example.com SOURCE_IMAGE=rhel7-base \
    TARGET_IMAGE=test01 fab -H root@example.com vm_create

Destroying a virtual machine::

    fab -H root@example.com vm_destroy:test01,delete_image=True

Listing virtual machines::

    fab -H root@example.com vm_list

Subscription Management
-----------------------

Subscribe::

    RHN_USERNAME=user@example.com RHN_PASSWORD=mysecret \
    RHN_POOLID=poolid fab -H root@example.com subscribe

Unsubscribe::

    fab -H root@example.com unsubscribe

Satellite Installation
----------------------

To install a compose build::

    RHN_USERNAME=user@example.com RHN_PASSWORD=mysecret \
    RHN_POOLID=poolid BASE_URL=http://example.com/Satellite/x86_64/os/ \
    fab -H root@example.com \
    product_install:satellite6-downstream

To install a nightly build::

    RHN_USERNAME=user@example.com RHN_PASSWORD=mysecret \
    RHN_POOLID=poolid \
    fab -H root@example.com \
    product_install:satellite6-upstream


To install from the CDN::

    RHN_USERNAME=user@example.com RHN_PASSWORD=mysecret \
    RHN_POOLID=poolid \
    fab -H root@example.com \
    product_install:satellite6-cdn


Product install task will set the admin password to `changeme`.

SAM Installation
----------------

To install a nightly build::

    RHN_USERNAME=user@example.com RHN_PASSWORD=mysecret \
    RHN_POOLID=poolid \
    fab -H root@example.com \
    product_install:sam-upstream

Product install task will set the admin password to `changeme`.

Miscellaneous
-------------

Fabric will use your default ssh key, but if you want to specify a different
one, use the `-i` option::

    fab -i path/to/my_ssh_key task

Documentation
-------------

You can generate the documentation for automation-tools as follows, so long
as you have `Sphinx`_ and make installed::

    cd docs
    make html

.. _Sphinx: http://sphinx-doc.org/index.html
