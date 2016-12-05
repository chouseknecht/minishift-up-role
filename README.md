cluster-up-role
===============

Autmotes the steps for installing a local OpenShift cluster. Created specifically to support the demo and testing needs of the [Ansible Container project](http://docs.ansible.com/ansible-container/configure_openshift.html), it performs the following tasks:

- Downloads and installs the oc client
- Installs `socat`, if running on OSX
- Adds a hostname associated with your public IP to /etc/hosts
- Starts the cluster
- Grants cluster admin to the *developer* account
- Creates a route to expose the local registry
- Creates a persistent volume

Requirements
------------

- Docker or Docker fro Mac
- root access - required to update /etc/hosts and install the oc client to something in your PATH (i.e. /usr/local/bin)

Role Variables
--------------
openshift_github_user: openshift
> The owner of the GitHub repo where oc client download targets can be found

openshift_github_name: origin
> The name of the GitHub repo  

openshift_github_url: https://api.github.com/repos
> The GitHub API endpoint to use.

openshift_release_tag_name: "v1.3.1"
> The desired relase of `oc` to download.

openshift_client_dest: /usr/local/bin  
> The directory where `oc` will be installed. Needs to be something in your PATH.

openshift_force_client_install: no
> If the `oc` binary already exists, should it be overwritten?

openshift_volume_name: project-data
> Name of the volume.

openshift_volume_path: "{{ lookup('env','HOME') }}/volumes/project/data"
> A local path where space will be allocated for the new volume. 

openshift_hostname: local.openshift
> The hostname you'll use to reference the local registry when you're ready to push images.

openshift_recreate: no
> If a cluster is already running, should it be killed and recreated? 

Dependencies
------------

None

Example Playbook
----------------
When you run the role, be sure to leave gather_facts set to a truthy value. Without facts, the role cannot determine your default IP address or OS family. 

```
    ---
    - hosts: localhost
      remote_user: root
      connection: local
      gather_facts: yes
      roles:
        - role: cluster-up-role
```

License
-------

Apache v2

Author 
------

[@chouseknecht](https://github.com/chouseknecht)
