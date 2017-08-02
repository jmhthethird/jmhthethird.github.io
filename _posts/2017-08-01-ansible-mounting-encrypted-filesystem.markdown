---
layout: post
title: "ansible mounting encrypted filesystem"
date:   2017-08-01
categories: ansible dmcrypt luks
---
What we want to do:
Setup an encrypted partition in an existing OS install. Put a filesystem in it so we can do stuff.

Basic steps:
1. Install all necessary packages
2. Create a keyfile to encrypt/decrypt the partition with.
3. Create volume mount point and data file.
4. Encrypt data file and then decrypt it for formatting.
5. Format data file with a filesystem so we can use it.
6. Mount that filesystem so we can write stuff to it.
7. (optional but highly recommended) remove keyfile.

What that looks like in Ansible:
```
- name: install packages
    apt: name={{ item }}
    with_items:
        - cryptsetup
        - util-linux
    tags: [dmcrypt, packages] #You should get in the habit of tagging your tasks!


# I'd recommend generating your keyfile and keeping it in an ansible-vault encrypted volume. Having it shown in code here is just for reference.
- name: create keyfile
    blockinfile:
        path: /dev/shm/.dmcrypt.kf
        create: yes
        block:  |
            thisisyourkeyfile
    tags: [dmcrypt]


- name: allocate data file for encrypted volume
    shell: fallocate -l 10G /useful_directory/encrypted_file01
    tags: [dmcrypt]


- name: encrypt the data file we just created
    shell: cryptsetup -q -d /dev/shm/.dmcrypt.kf luksFormat /useful_directory/encrypted_file01
    tags: [dmcrypt]


- name: decrypt file so we can format and use it
    shell: cryptsetup -q -d /dev/shm/.dmcrypt.kf luksOpen /useful_directory/encrypted_file01
    tags: [dmcrypt]


- name: format the file with a filesystem so we use it
    filesystem: fstype=ext4 dev=/dev/mapper/encrypted_vol01
    tags: [dmcrypt]


- name: mount the formatted filesystem as a volume so we can write information into it
    shell: mount /dev/mapper/encrypted_vol01 /useful_directory/encrypted_file01
    tags: [dmcrypt]
```

~glhf~