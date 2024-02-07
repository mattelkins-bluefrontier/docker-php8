# Docker with PHP 8.2.15

This repository aims to facilitate the creation of a PHP development environment.

## What's in the environment:

- [PHP-FPM](https://php.net/)
  - [php](https://hub.docker.com/_/php):[8.2.15-fpm-alpine3.19](https://hub.docker.com/layers/library/php/8.2.15-fpm-alpine3.19/images/sha256-b15e71c6d9e549d4b69d4200a6f9d49386c5ae617c38e27fc79dc146dc7fd407)
- [Apache2](https://httpd.apache.org/)
  - [httpd](https://hub.docker.com/_/httpd):[2.4-alpine](https://hub.docker.com/layers/library/httpd/2.4-alpine/images/sha256-faaa3c93157a9c34ac00190b859e7a22975cbc6dd79f357609db03e17f0db178)
- [MariaDB](https://mariadb.com/)
  - [mariadb](https://hub.docker.com/_/mariadb):[latest](https://hub.docker.com/layers/library/mariadb/latest/images/sha256-ac933f87a5fc8b743a3c522179116ee63aec31105795dc28dea8b80bb74cdd36)
- [phpMyAdmin](https://www.phpmyadmin.net/)
  - [phpmyadmin](https://hub.docker.com/_/phpmyadmin):[latest](https://hub.docker.com/layers/library/phpmyadmin/latest/images/sha256-25c6b614d3190d1993a2b55a8af77d4dab67983e9ef51fde5227bd9289b99f95)

## Prerequisites:

### Windows users:

- [Enable hardware virtualisation in the BIOS](https://www.virtualmetric.com/blog/how-to-enable-hardware-virtualization)

- [Install the Windows Subsytem for Linux (a.k.a. WSL 2)](https://learn.microsoft.com/en-us/windows/wsl/install)
  - The default Linux distro is Ubuntu. Make sure you [set up your Linux user info](https://learn.microsoft.com/en-us/windows/wsl/install#set-up-your-linux-user-info).
  - You should also [check WSL version 2 is running](https://learn.microsoft.com/en-us/windows/wsl/install#check-which-version-of-wsl-you-are-running), which it should be unless your computer is ancient.

- [Enable Windows features](https://docs.docker.com/desktop/troubleshoot/topics/#virtualization):
  - Virtual Machine Platform
    - ***NB:** If this feature is already enabled, you may need to disable it, restart Windows, re-enable it, and restart Windows again. This is a Windows bug apparently.*
  - Windows Subsystem for Linux
    - *This feature should already be enabled after completing the step above.*
  - Hyper-V
  - Windows Hypervisor Platform
    - *The two features above aren't used (using WSL 2 is the recommended option), but still need to be enabled for some reason.*

- [Install the WSL extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) for Visual Studio Code
  - [Install Visual Studio Code](https://code.visualstudio.com/Download) first if needed.

- Create a folder in your Linux home directory to use as the "root" for this container, and set up a few required folders, by running the following commands in Command Prompt:
  - `wsl`
  - `cd ~`
  - `mkdir www; mkdir www/dbdata; mkdir -p www/logs/apache; mkdir -p www/logs/php; mkdir www/public_html`
  - `touch www/public_html/index.php`

- Add `phpinfo();` or something in to `public_html/index.php` - this will be used to check everything is behaving. To open in Visual Studio Code, run the following commands:
  - `cd ~/www/public_html`
  - `code .`

- Set permissions to allow Apache to run the website and also allow code to be edited.
  - The `www-data` user in [Alpine Linux](https://www.alpinelinux.org/) (the distro used in the container) has an ID of 82, which is not the same as the equivalent `www-data` user in the default distro ([Ubuntu](https://ubuntu.com/)) installed in WSL. Run the following command to change the user and group IDs in Ubuntu to match Alpine:
    - `sudo usermod -u 82 www-data`
    - `sudo groupmod -g 82 www-data`
  - To ensure you still have the ability to edit code, add your Linux user to the `www-data` group:
    - `sudo usermod -a -G www-data your_linux_username`
  - Change ownership of folders the `www-data` user and group will need to access:
    - `cd ~/www`
    - `sudo chown -R www-data:www-data logs`
    - `sudo chown -R www-data:www-data public_html`
  - Give the `www-data` group (to which your Linux user is now a member) write access to everything inside `public_html`:
    - `sudo chmod -R g+w public_html`

- **NB:** Having code living inside, and running from, the Linux file system provided by WSL 2 has significant performance advantages. The alternative is to have code running from the Windows file system via a Linux mount, which is prohibitively slow.

### Docker:

- [Install Docker Desktop](https://www.docker.com/products/docker-desktop/) (recommended)

*OR*

- [Install Docker](https://docs.docker.com/install/)
- [Install Docker Compose](https://docs.docker.com/compose/install/)

## How to use:

- Clone the repository
- Enter the repository folder
- Create a `.env` file at the same level as `compose.yaml` and add the following environment variables:
  - `PATH_ROOT` - the folder in your Linux home directory within WSL 2 to map to `/var/www`, e.g. `\\wsl$\Ubuntu\home\YOUR_LINUX_USERNAME\www`, and which will act as the "root" for the container
  - `PATH_WEBROOT` - the folder within `PATH_ROOT` from which web pages will be served, e.g. `${PATH_ROOT}\public_html` which will be interpreted as `\\wsl$\Ubuntu\home\YOUR_LINUX_USERNAME\www\public_html`
  - `PATH_DBROOT` - the folder within `PATH_ROOT` which will store database data, e.g. `${PATH_ROOT}\dbdata` which will be interpreted as `\\wsl$\Ubuntu\home\YOUR_LINUX_USERNAME\www\dbdata`
- Run the command `docker-compose up -d`
- The address `http://localhost:8080` will load phpMyAdmin
  - User access:
    - User: mysql
    - Password: mysql
    - Host: mariadb
  - Root access:
    - User: root
    - Password: root
    - Host: mariadb
- The address `http://localhost` will load `public_html/index.php`

## php.ini configuration:

Local php.ini configuration is located in the `./docker/php/php.ini` file. After making changes, you will need to run `docker-compose up -d --build` to copy the updated file in to the correct location within the container.

## Apache named virtual hosts

By default, a single application can be run out of `PATH_WEBROOT`, but it's also possible to leverage virtual hosts in Apache to run multiple applications using the same versions of PHP and MariaDB, e.g.:

```
|_ PATH_ROOT
   |_ my_joomla_site
      |_ index.php
      |_ etc.

   |_ my_wp_site
      |_ index.php
      |_ etc.

   |_ my_drupal_site
      |_ index.php
      |_ etc.

   |_ index.php
```

Named virtual can be created by adding an `httpd-vhosts.conf` file to `docker/apache`, and adding `<VirtualHost>` entries as required, e.g.:

```
<VirtualHost *:80>
  DocumentRoot "${WWW_ROOT}/public_html/my_joomla_site"
  ServerName my_joomla_site.local
  <Directory "${WWW_ROOT}/public_html/my_joomla_site">
    AllowOverride All
  </Directory>
</VirtualHost>

<VirtualHost *:80>
  DocumentRoot "${WWW_ROOT}/public_html/my_wp_site"
  ServerName my_wp_site.local
  <Directory "${WWW_ROOT}/public_html/my_wp_site">
    AllowOverride All
  </Directory>
</VirtualHost>

<VirtualHost *:80>
  DocumentRoot "${WWW_ROOT}/public_html/my_drupal_site"
  ServerName my_drupal_site.local
  <Directory "${WWW_ROOT}/public_html/my_drupal_site">
    AllowOverride All
  </Directory>
</VirtualHost>
```

*NB:* Note the use of the variable `${WWW_ROOT}` which will be interpreted as `/var/www`, which, in turn, will reference the folder path specified by the `PATH_ROOT` environment variable.

Continuing with the examples above, the following would need adding to your `hosts` file:

```
127.0.0.1   my_joomla_site.local
127.0.0.1   my_wp_site.local
127.0.0.1   my_drupal_site.local
```

## License:

[MIT](https://opensource.org/licenses/MIT)
