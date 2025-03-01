+++
Categories = ["Quick Notes"]
Description = "Mount a Wasabi storage bucket through /etc/fstab usign s3fs on Ubuntu."
Tags = ["Linux","Ubuntu","s3fs","Wasabi"]
date = "2022-05-03T08:34:21-06:00"
title = "Mount Wasabi storage bucket using s3fs and /etc/fstab on Ubuntu"
+++

Wasabi's [official documentation on using s3fs to mount a storage bucket](https://wasabi-support.zendesk.com/hc/en-us/articles/115001744651-How-do-I-use-S3FS-with-Wasabi-) is lacking an example of using `/etc/fstab` to persist the bucket mount following a reboot.

To use `/etc/fstab` to permanently mount a Wasabi bucket using s3fs on Ubuntu, first install s3fs:

```
sudo apt-get install s3fs
```

Create a filesystem location to mount the bucket:

```
sudo mkdir /mnt/wasabi_bucket
sudo chmod 777 /mnt/wasabi_bucket
```

Create the entry in /etc/fstab:

```
wasabi_bucket:/ /mnt/wasabi_bucket fuse.s3fs _netdev,allow_other,use_path_request_style,url=https://s3.wasabisys.com,use_cache=/tmp 0 0
```

Mount the bucket:

```
sudo mount -a
```