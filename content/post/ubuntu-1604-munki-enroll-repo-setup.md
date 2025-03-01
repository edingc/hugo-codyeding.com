+++
Categories = ["Tutorials"]
Description = "Setup a secure Munki/munki-enroll repository on Ubuntu."
Tags = ["Linux","Ubuntu","Munki","munki-enroll"]
date = "2016-11-21T08:34:21-06:00"
title = "Setup secure Munki repository and munki-enroll on Ubuntu 16.04 LTS"

+++

I managed several thousand Macintosh desktops in a previous job with the help of [Munki](https://github.com/munki/munki), an awesome open-source tool written by Greg Neagle. Alongside Munki, I created [munki-enroll](https://github.com/edingc/munki-enroll), which is now a popular way to manage the automated creation of computer-specific Munki manifests.

I recently worked on a GitHub bug report that was actually not a bug in the munki-enroll code, but instead a problem stemming from a lack of documentation regarding the server-side requirements of munki-enroll. So, without further ado, here's a proper guide to setting up a Munki repository with munki-enroll on Ubuntu 16.04 LTS.

Following this guide will result in a Apache web server, running PHP with the necessary extensions for munki-enroll, all secured with Basic Authentication and a free SSL certificate from Let's Encrypt. In this case, the server will be located at *munki.domain.com*.

**External DNS (munki.domain.com) must resolve to the Munki server in order for the Let's Encrypt certificate bot to generate an SSL certificate successfully.**

Update a fresh installation of Ubuntu 16.04 LTS and install all required dependencies. Munki-enroll requires PHP version 5.3 or later and the php-xml extensions package. These commands also install Git (to clone the munki-enroll repository), the Let's Encrypt certificate bot and the Apache webserver and required utilities:

```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y apache2 php git python-letsencrypt-apache apache2-utils libapache2-mod-php php-xml
```

Once Ubuntu is updated and the required packages are installed, run the Let's Encrypt certificate bot to setup SSL on the Apache installation. The following command automatically agrees to the Let's Encrypt Terms of Service and redirects all non-HTTPS traffic to HTTPS. Substitute in the address of the Munki server and a good administrator email:

```
sudo letsencrypt --apache -d munki.domain.com -n --email email@domain.com --agree-tos --redirect
```

Next, create the folder structure for the Munki repository:

```
sudo mkdir -p /var/www/html/munki_repo/{catalogs,manifests,pkgs,pkgsinfo}
```

Then clone munki-enroll to `/tmp` using Git. Move the munki-enroll server scripts to the correct location:

```
git clone https://github.com/edingc/munki-enroll.git /tmp/munki-enroll
sudo mv /tmp/munki-enroll/munki-enroll /var/www/html/munki_repo
```

Ensure Apache can write to the Munki repository:

```
sudo chmod -R a+rX,g+w /var/www/html/munki_repo
sudo chown -R www-data:www-data /var/www/html/munki_repo
```

Create a password file containing a user called *munki*. Enter a password when prompted:

```
sudo htpasswd -c /etc/apache2/.htpasswd munki
```

Modify the Let's Encrypt-created Apache virtual host located at `/etc/apache2/sites-enabled/000-default-le-ssl.conf`. Look for the default `DocumentRoot` configuration:

```
DocumentRoot /var/www/html
```

Change `DocumentRoot` to the root of the Munki repository:

```
DocumentRoot /var/www/html/munki_repo
```

Paste the following at the bottom of the configuration file to setup the Munki repository with Basic Authentication:

```
<Directory "/var/www/html/munki_repo">
  AuthType Basic
  AuthName "Restricted Content"
  AuthUserFile /etc/apache2/.htpasswd
  Require valid-user
</Directory>
```

Save the configuration file and restart Apache:

```
sudo service apache2 restart
```

That's it! Navigate to the repostory located at **munki.domain.com**. The web browser should redirect to https://munki.domain.com and prompt for the username and password created earlier.

## Additional Notes ##

### Let's Encrypt Certificate Renewal ###

Let's Encrypt certificates are only valid for 90 days. It would be wise to setup a cron job to automatically renew the certificates when necessary.

To setup certificate renewal, launch the root crontab in an editor:


```
sudo crontab -e
```

If you have not edited the crontab before, you may be prompted to select an editor. Per the instructions, nano is probably the easiest choice:

```
no crontab for root - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/vim.tiny

Choose 1-3 [2]: 
```

Add the following to the crontab and save:

```
00 1 * * 1 /usr/bin/letsencrypt renew >> /var/log/letsencrypt-renew.log
```

The Let's Encrypt certificate bot will now check for a renewed certificate at 1 a.m. every Monday.

### Why no SMB/CIFS/AFP access? ###

In my opinion, setting up SMB/CIFS (Samba) or AFP (Netatalk) for a simple Munki repository is more hassle than it is worth. Also, it may be preferrable to host the Munki repository with an external hosting provider depending on requirements. In that case, it would not be safe to have file sharing protocols open over the Internet.

My recommendation would be to use [SSHFS and FUSE for macOS](https://osxfuse.github.io/) to mount the Munki repository to administrative machines securely over SSH.
