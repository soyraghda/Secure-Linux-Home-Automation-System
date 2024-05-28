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

### 2. Passwords must adhere to a minimum length of 10 characters

To implement this, navigate to the directory related to password configurations:

```shell
$ cd /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d
```
Then configure the corresponding file:
```shell-session
$ vi /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d/common-password
In the first line, include the pam_pwquality.so module and set the following parameters:
	password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 
```
#### Explanation
  - `minlen=10`: Ensures passwords contain a minimum of 10 characters.

### 3. Passwords must contain a combination of alpha, numeric, and special characters

```shell-session
        In the same file 'common-password' we added the 'minclass=3' parameter to the pam_pwquality.so module
        password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 lcredit=0 minclass=3
```
#### Explanation
  - `minclass=3`: will enforce that the passwords contain letters, numbers and special characters*
  - `lcredit=0`: will specify that the password must contain at least one lowercase letter*

### 4. Disallow usernames or user IDs to be used as passwords

```shell-session
        In the same file 'common-password' we added the 'usercheck=1' parameter to the pam_pwquality.so module
        password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 lcredit=0 minclass=3 usercheck=1
```
#### Explanation
  - `usercheck=1`: will make sure that the password does not contain the username or user ID of the user

### 5. Prevent the user from using the last 3 passwords

```shell-session
        In the same file 'common-password' we added the 'remember=3' parameter to the pam_pwquality.so module
        password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 lcredit=0 minclass=3 usercheck=1 remember=3
```
#### Explanation
  - `remember=3`: will remember the last 3 passwords for the user in order to prevent him from using them again

### 6. Password should not contain simple/easy to guess words

```shell-session
        In the same file 'common-password' we added the 'dictcheck' parameter to the pam_pwquality.so module
        password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 lcredit=0 minclass=3 usercheck=1 remember=3 dictcheck=1
```
#### Explanation
  - `dictcheck=1`: will make sure that the passwords do not contain simple or easy to guess words

### 7. Hash passwords with function considered as safe

```shell-session
        In the same file 'common-password' we added the 'sha512' parameter to the pam_unix.so module
        password        sufficient       pam_unix.so sha512 shadow use_authtok
```
#### Explanation
  - `sha512`: will hash the passwords using the sha512 algorithm

### 8. Lock the user account for 5 mins after 3 failed trials of entering the password

Since the pam library and the libpwquality package did not include the pam_faillock.so with them so we added it to the following libpam recipe:

```shell-session
	$ vi /yocto/project/poky/meta/recipes-extended/pam/libpam/libpam_1.5.2.bb
	Under the 'RDEPENDS:${PN}-runtime' code we added the following line:
	${MLPREFIX}pam-plugin-faillock-${libpam_suffix} \
```
In order to configure the the pam_faillock.so module we did the following:

```shell-session
	$ vi /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d/common-auth
        In the 'common-auth' we added the following parameters to the pam_faillock.so module  
        auth    required                        pam_faillock.so preauth silent deny=3 unlock_time=300 even_deny_root
	auth    required                        pam_faillock.so authfail
```

#### Explanation
  - `preauth` : Indicates that the account locking mechanism should be applied during the pre-authentication phase.
  - `silent` : Specifies that no error message should be displayed to the user if authentication fails due to account lockout.
  - `deny=3` : Specifies the number of consecutive authentication failures allowed before the account is locked.
  - `unlock_time=300` : Specifies the duration (300sec = 5mins) for which the account remains locked after reaching the maximum number of failed login attempts.
  - `even_deny_root` : Specifies that even the root account can be denied access if it fails authentication.
  - `authfail` : Specifies that this rule applies specifically when authentication fails.

Moreover, in the 'common-account' we made it required to include and check the pam_faillock.so module configurations

```shell-session
        $ vi /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d/common-acoount
        In this file we added the following line at the end of it:
	auth    required                        pam_faillock.so
```

# Implementation of Security Requirment 2 : Kernel Hardening

In order to make use of the sysctl we need to install 'sysctl' and the 'procps' package in our local.conf file.

First naviagate to the local.conf file

```shell-session
        $ vi /yocto/project/build/conf/local.conf
```
We will add the following lines to it:

```shell-session
        DISTRO_FEATURES:append = " systemd"
        IMAGE_INSTALL:append = " procps"
```

### 1. General Kernel Hardening Configurations

To do our configurations we need to navigate to the sysctl.conf file , where the dynamic kernel configurations can be done

```shell-session
	$ vi /yocto/project/poky/meta/recipes-extended/procps/procps/sysctl.conf
```

The we will add the following hardening configurations:

```shell-session
	###################---KERNEL CONFIGURATIONS---################################
	# Restrict access to the dmesg buffer (equivalent to CONFIG_SECURITY_DMESG_RESTRICT=y)
	kernel.dmesg_restrict=1
	# Hide kernel addresses in /proc and various other interfaces ,including from privileged users
	kernel.kptr_restrict=2
	# Disable Magic System Request Key combinations
	kernel.sysrq=0
	# Completely shut down the system if the Linux kernel behaves unexpectedly
	kernel.panic_on_oops=1
```

### 2. File System Hardening Configurations

We also added the file system hardening configurations to the same file:

```shell-session
	###################---FILE SYSTEM CONFIGURATIONS---#############################
	# Allows to prohibit opening FIFOs and "regular" files that are not owned by the user in sticky folders for everyone to write.
	fs.protected_fifos=2
	fs.protected_regular=2
	# Restrict the creation of hard links to files whose user is owner.
	fs.protected_hardlinks=1
```

