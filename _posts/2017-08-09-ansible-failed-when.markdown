---
layout: post
title: "Ansible failed_when"
date:   2017-08-09
categories: ansible error handling
---

When is a failure not a failure?

For me it is when an thing is run _expecting_ it to fail. That's a successful failure!

Unfortunately Ansible doesn't always see eye to eye with me on that. Here is a quick way to let the Ansible run accept [return codes](http://tldp.org/LDP/abs/html/exitcodes.html) that normally would be considered an error.
<br>

-----
Here is my setup for this test:

My working directory:
```
jhefner:test_failure jhefner$ ls
hosts       site.yml
```

`hosts` file contents:
```
localhost ansible_connection=local
```

`site.yml` file contents:
```
---
- name: Testing failure
  hosts: all
  gather_facts: no
  tasks:
    - name: This does not fail  # Name of the task
      shell: "ls -lah"          # Command being run on the command prompt
      register: a_success       # Variable being set for the output of command

    - name: This will fail      # <same as above>
      shell: "ls -xyz"          # Intentionally using invalid options for the ls command
      register: a_failure       #
```
{: .language-bash}

Run this playbook and you will get a `"rc": 0` for the first task `This does not fail` and a `"rc": 1` for the second task `This will fail`. We are expecting to fail the second task as we are specifying invalid options for the command to use.
```
hefner:test_failure jhefner$ ansible-playbook -i hosts site.yml -v
Using /Users/jhefner/.ansible.cfg as config file

PLAY [Testing failure] *********************************************************************************************************************************************

TASK [This does not fail] ******************************************************************************************************************************************
changed: [localhost] => {"changed": true, "cmd": "ls -lah", "delta": "0:00:00.008188", "end": "2017-08-09 15:13:48.647607", "rc": 0, "start": "2017-08-09 15:13:48.639419", "stderr": "", "stderr_lines": [], "stdout": "total 32\ndrwxr-xr-x  6 jhefner  staff   204B Aug  9 15:08 .\ndrwxr-xr-x  4 jhefner  staff   136B Aug  9 14:09 ..\n-rw-r--r--  1 jhefner  staff     7B Aug  9 14:14 .python-version\n-rw-r--r--  1 jhefner  staff    35B Aug  9 15:01 hosts\n-rw-r--r--  1 jhefner  staff    10B Aug  9 15:13 site.retry\n-rw-r--r--  1 jhefner  staff   225B Aug  9 14:58 site.yml", "stdout_lines": ["total 32", "drwxr-xr-x  6 jhefner  staff   204B Aug  9 15:08 .", "drwxr-xr-x  4 jhefner  staff   136B Aug  9 14:09 ..", "-rw-r--r--  1 jhefner  staff     7B Aug  9 14:14 .python-version", "-rw-r--r--  1 jhefner  staff    35B Aug  9 15:01 hosts", "-rw-r--r--  1 jhefner  staff    10B Aug  9 15:13 site.retry", "-rw-r--r--  1 jhefner  staff   225B Aug  9 14:58 site.yml"]}

TASK [This will fail] **********************************************************************************************************************************************
fatal: [localhost]: FAILED! => {"changed": true, "cmd": "ls -xyz", "delta": "0:00:00.006374", "end": "2017-08-09 15:13:48.838034", "failed": true, "rc": 1, "start": "2017-08-09 15:13:48.831660", "stderr": "ls: illegal option -- y\nusage: ls [-ABCFGHLOPRSTUWabcdefghiklmnopqrstuwx1] [file ...]", "stderr_lines": ["ls: illegal option -- y", "usage: ls [-ABCFGHLOPRSTUWabcdefghiklmnopqrstuwx1] [file ...]"], "stdout": "", "stdout_lines": []}
    to retry, use: --limit @/Users/jhefner/ansible_dev/test_failure/site.retry

PLAY RECAP *********************************************************************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=1
```
{: .language-bash}

<br>
To alter what Ansible is expecting as an OK return code we can add the `failed_when` option to our task:
```
---
- name: Testing failure
  hosts: all
  gather_facts: no
  tasks:
    - name: This does not fail  
      shell: "ls -lah"          
      register: a_success       

    - name: This will fail      
      shell: "ls -xyz"          
      failed_when: (a_failure.rc != 0 and a_failure.rc != 1)
      register: a_failure       
```
{: .language-bash}

With the above code we are telling Ansible to accept the return code `0` and return code `1` as a success. When we run the playbook again with this change our output will now accept that return code as a success and not return an error.
```
jhefner:test_failure jhefner$ ansible-playbook -i hosts site.yml -v
Using /Users/jhefner/.ansible.cfg as config file

PLAY [Testing failure] *********************************************************************************************************************************************

TASK [This does not fail] ******************************************************************************************************************************************
changed: [localhost] => {"changed": true, "cmd": "ls -lah", "delta": "0:00:00.008046", "end": "2017-08-09 15:48:05.064986", "rc": 0, "start": "2017-08-09 15:48:05.056940", "stderr": "", "stderr_lines": [], "stdout": "total 32\ndrwxr-xr-x  6 jhefner  staff   204B Aug  9 15:48 .\ndrwxr-xr-x  4 jhefner  staff   136B Aug  9 14:09 ..\n-rw-r--r--  1 jhefner  staff     7B Aug  9 14:14 .python-version\n-rw-r--r--  1 jhefner  staff    35B Aug  9 15:01 hosts\n-rw-r--r--  1 jhefner  staff    10B Aug  9 15:47 site.retry\n-rw-r--r--  1 jhefner  staff   286B Aug  9 15:48 site.yml", "stdout_lines": ["total 32", "drwxr-xr-x  6 jhefner  staff   204B Aug  9 15:48 .", "drwxr-xr-x  4 jhefner  staff   136B Aug  9 14:09 ..", "-rw-r--r--  1 jhefner  staff     7B Aug  9 14:14 .python-version", "-rw-r--r--  1 jhefner  staff    35B Aug  9 15:01 hosts", "-rw-r--r--  1 jhefner  staff    10B Aug  9 15:47 site.retry", "-rw-r--r--  1 jhefner  staff   286B Aug  9 15:48 site.yml"]}

TASK [This will fail] **********************************************************************************************************************************************
changed: [localhost] => {"changed": true, "cmd": "ls -xyz", "delta": "0:00:00.006212", "end": "2017-08-09 15:48:05.242717", "failed": false, "failed_when_result": false, "rc": 1, "start": "2017-08-09 15:48:05.236505", "stderr": "ls: illegal option -- y\nusage: ls [-ABCFGHLOPRSTUWabcdefghiklmnopqrstuwx1] [file ...]", "stderr_lines": ["ls: illegal option -- y", "usage: ls [-ABCFGHLOPRSTUWabcdefghiklmnopqrstuwx1] [file ...]"], "stdout": "", "stdout_lines": []}

PLAY RECAP *********************************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0
```
{: .language-bash}

<br>
~ggwp~
