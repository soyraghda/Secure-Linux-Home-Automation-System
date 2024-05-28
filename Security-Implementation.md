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
```
This line is typically used in case of debugging and developing the system, therefore when the system is ready to be deployed this line needs to be removed.

###2. Passwords must adhere to a minimum length of 10 characters**

To implement this, navigate to the directory related to password configurations:

```shell-session
$ cd /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d
```
Then configure the corresponding file:
```shell-session
$ vi /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d/common-password
In the first line, include the pam_pwquality.so module and set the following parameters:
	password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 lcredit=0 minclass=3
```
# Explanation
- Configuring the common-password file to enforce password policies:
  - `minlen=10`: Ensures passwords contain a minimum of 10 characters.
  - `lcredit=0`: Requires at least one lowercase letter in the password.
  - `minclass=3`: Enforces the use of letters, numbers, and special characters in passwords.






