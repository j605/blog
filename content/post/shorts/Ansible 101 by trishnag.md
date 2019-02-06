---
title: "Ansible 101 by trishnag"
date: 2019-02-06T20:15:11+01:00
tags:
  - ansible
  - linux
categories:
  - dgplug
---

1. Learned about hosts file to create an inventory, https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#hosts-and-groups

2. Different connections (ssh and local for now). I had also tested it against a server running CentOS.

3. We then went on to create an `ansible.cfg` file in the demo directory which takes precedence over the global configuration.

4. Learned to write a basic playbook which is a YAML file.

    - /bin/echo using shell module

    - ping using the ping module
