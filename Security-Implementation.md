Implementaion of Security Requirments
=======================================

## Environment Setup

1. Navigate to the local.conf file:
    ```shell
    vi /yocto/project/build/conf/local.conf
    ```

2. Add the following lines:
    ```shell
    DISTRO_FEATURES:append = " pam"
    IMAGE_INSTALL:append = " libpwquality"
    ```

# Implementation of Security Requirement 1: Password Policy

To enforce password policies in a Yocto environment, follow these steps:

## Configuration Setup

1. Navigate to the local.conf file:
    ```shell
    $ vi /yocto/project/build/conf/local.conf
    ```

2. Add the following lines to configure the pam package and libpwquality plugin:
    ```shell
    DISTRO_FEATURES:append = " pam"
    IMAGE_INSTALL:append = " libpwquality"
    ```

## Password Policies Implemented

### 1. Enforcing a Root Password

To enforce the root password, comment out a specific line in the local.conf file:
```shell
$ vi /yocto/project/build/conf/local.conf
# EXTRA_IMAGE_FEATURES ?= "debug-tweaks"
