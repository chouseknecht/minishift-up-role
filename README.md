# minishift-up-role

[![Build Status](https://travis-ci.org/chouseknecht/cluster-up-role.svg?branch=master)](https://travis-ci.org/chouseknecht/minishift-up-role)

Install minishift on OSX, Red Hat Linux, and Debian.

Created to support the demo and testing needs of [Ansible Container ](https://github.com/ansible/ansible-container) by automating the tasks in the [Install and Congigure OpenShift guide](http://docs.ansible.com/ansible-container/configure_openshift.html). 

Specifically, it performs the following tasks:

- Downloads and installs the latest minishift binary
- Installs the Docker Machine driver for either KVM or Xhyve, depending on the OS
- Creates a minishift instance 
- Grants cluster admin to the *developer* account
- Adds a persistent volume

## Supported platforms and testing

To date this role has been tested on OSX and Fedora 25 Workstation. So with that in mind, if you're attempting to use this on a different platform, you may discover a bug or two. Please open an issue, or submit a PR, for any bugs, and help us keep the role up to date. 

## Prerequisites 

### OSX

- [homebrew](https://brew.sh) 
- [Ansible 2.2+](https://docs.ansible.com)


### Linux

- KVM installed and working. The role installs the Docker Machine driver for KVM, but it assumes KVM is already alive and funtioning.
- CPU Virtualization enabled in the system bios
- Ansible 2.2+


### Fedora

- python2-dnf, and libselinux-python packages


## Defaults

minishift_repo: minishift/minishift 
> Repo where the minishift binary can be found

minishift_github_url: https://api.github.com/repos
> GitHub API 

minishift_release_tag_name: "v1.0.0-beta.1"
> The minishift release to install.

minishift_dest: /usr/local/bin  
> Where to install the minishift binary.

minishift_force_install: yes
> Overwrite any existing minishift binary found at {{ minishift_dest }}

minishift_volume:
  name: pv0001
  path: /home/docker/pv0001/
  size: 5Gi
> The name, path, and size of the Persistent Volume to create on the new minishift instance

minishift_recreate: yes
> Stop and delete an existing minishift instance, along with ~/.minishift. All existing containers and images will be deleted. All certificates found in ~/.minishift will be deleted. Otherwise, if an instance exists, the role will stop with an error.

minishift_start_options:
  - insecure-registry 172.30.0.0/16
  - insecure-registry minishift
  - iso-url https://github.com/minishift/minishift-centos-iso/releases/download/v1.0.0-alpha.1/minishift-centos.iso
> Any commandline options to pass to `minishift start`.

## Dependencies

None

## Example Playbook

When you run the role, be sure to leave gather_facts set to a truthy value. Without facts, the role cannot determine your OS family. 

Below is a sample playbook that includes all of the default parameters. You'll find this exact example in the *files* folder, called *minishift-up.yml*. Copy, and adjust it to fit your environment.

When you run the playbook, be sure to pass the ``--ask-sudo-pass`` option.

```
---
- name: Install minishift
  hosts: localhost
  connection: local
  gather_facts: yes
  roles:
    - role: minishift-up-role
      minishift_repo: minishift/minishift 
      minishift_github_url: https://api.github.com/repos
      minishit_release_tag_name: "v1.0.0-beta.1"
      minishift_dest: /usr/local/bin  
      minishift_force_install: yes
      minishift_volume:
        name: pv0001
        path: /data/pv0001/
        size: 5Gi
      minishift_recreate: yes 
      minishift_start_options:
      - insecure-registry 172.30.0.0/16
      - insecure-registry minishift
      - iso-url https://github.com/minishift/minishift-centos-iso/releases/download/v1.0.0-alpha.1/minishift-centos.iso
```

After you install the role, copy *file/minishift-up.yml* to your project directory, and execute it with the `--ask-sudo-pass` option. Here's an example of what that might look like:

```
# Install the role 
$ ansible-galaxy install chouseknecht.minishift-up-role

# Copy the playbook from your roles path to the current working directory 
$ cp ${ANSIBLE_ROLES_PATH}/chouseknecht.minishift-up-role/files/minishift-up.yml .

# Create a localhost inventory file
$ echo "localhost">./inventory

# Run the playbook
$ ansible-container -i inventory --ask-sudo-pass minishift-up.yml
```

## License

Apache v2

## Author 

[@chouseknecht](https://github.com/chouseknecht)
