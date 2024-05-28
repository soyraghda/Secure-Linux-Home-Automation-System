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

## Implementation of Security Requirement 1: Password Policy

1. Navigate to the local.conf file:
    ```shell
    vi /yocto/project/build/conf/local.conf
    ```

2. Comment the following line to enforce root password:
    ```shell
    # EXTRA_IMAGE_FEATURES ?= "debug-tweaks"
    ```

3. Navigate to the directory related to password configurations:
    ```shell
    cd /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d
    ```

4. Configure the corresponding file:
    ```shell
    vi /yocto/project/poky/meta/recipes-extended/pam/libpam/pam.d/common-password
    ```

5. Add the following line as the first line:
    ```shell
    password        requisite         /usr/lib/security/pam_pwquality.so minlen=10 lcredit=0 minclass=3
    ```

### Explanation

- Enforcing a root password by commenting out the line allowing root entry without a password.
- Configuring the common-password file to enforce password policies:
  - `minlen=10`: Ensures passwords contain a minimum of 10 characters.
  - `lcredit=0`: Requires at least one lowercase letter in the password.
  - `minclass=3`: Enforces the use of letters, numbers, and special characters in passwords.
