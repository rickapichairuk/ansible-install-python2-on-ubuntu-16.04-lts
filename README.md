# NAME

ansible-install-python2-on-ubuntu-16.04-lts

# SYNOPSIS

A playbook to install python2 on Ubuntu 16.04 LTS

# INTRODUCTION

Ubuntu 16.04 LTS doesn't come with Python 2 installed. Ansible needs Python 2
installed on target systems. This playbook installes the correct python needed
by ansible.

# INSTRUCTIONS

Create a "wrapper" playbook that includes other playbooks. This can be as
minimal as including the playbook provided by this project and your playbook.

Include this playbook first. This is because this playbook needs to run prior to
gathering facts which relies on having Python 2 installed.

Just include in your playbook like so:

```
# wrapper_playbook.yml
# This playbook includes other playbooks.
- include: /path/to/ansible-install-python2-on-ubuntu-16.04-lts/playbook.yml
- include: basic_ubuntu_server_setup.yml
```

# EXPLANATION

This playbook has `gather_facts` set to 'no' because `gather_facts` relies on
Python 2 being installed. The playbook runs `sudo apt-get update` and then does
a test for `/usr/bin/python` (which is binary location for python2). If it does
not exist, then the playbook will install `python-simplejson` package which in
turn depends on python 2. Lastly, it will run `setup` which is effectively
`gather_facts` and thus making facts available to the remaining playbooks and
tasks.

The following is the source code of the playbook:

```
---
- name: "Install python 2 on Ubuntu 16.04 LTS"
  hosts: all
  become: true
  gather_facts: no
  pre_tasks:
    - raw: sudo apt-get update
    - raw: test -e /usr/bin/python || sudo apt-get -y install python-simplejson
    - setup: # aka gather facts
```

# AUTHOR

Rick Apichairuk

# LICENSE

BSD 3-Clause License

Copyright (c) 2017, Rick Apichairuk
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of the copyright holder nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
