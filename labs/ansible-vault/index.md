# Ansible Vault

## Scenario

There are several features unique to Ansible playbooks that provide robust functionality. This exercise explores many of these features in a practical scenario of deploying a web server. Most notably, this exercise deals with confidential data in an Ansible vault and working with tags in Ansible playbooks.

Create and enter a working directory

 ```
mkdir /home/ec2-user/lab-vault && cd $_
 ```



### Use ansible-vault to protect the confidential information

Use `ansible-vault` to encrypt a sensitive file to protect the confidential information stored within using the password "I love ansible".

Run 
```
ansible-vault encrypt /home/ec2-user/automate-cloud/labs/ansible-vault/conf/confidential
```
and supply the password "I love ansible".

### Create a playbook that deploys httpd on webservers

Create a playbook in `/home/ec2-user/lab-vault/webserver.yml` that deploys `apache2` on the webservers. It should be tagged with `base-install` and contain a handler that restarts the `apache2` daemon that is flagged by both installation and service manipulation for `apache2`.

Create the file `/home/ec2-user/lab-vault/webserver.yml` and add the following content:

```yaml
---
 - hosts: webservers
   become: yes
   vars_files:
     - /home/ec2-user/automate-cloud/labs/ansible-vault/conf/confidential
   tasks:
     - name: install apache2
       apt:
         name: apache2
         state: latest
       notify: httpd service
       tags:
         - base-install
   handlers:
     - name: Restart and enable apache2
       service:
         name: apache2
         state: restarted
         enabled: yes
       listen: httpd service
```

### Deploy the templates stored on the control node to the webservers group

Configure `webserver.yml` to deploy the templates `vhost.conf.j2` and `htpasswd.j2` to the `webservers` group. `apache2` must restart on config change. The tasks should be tagged `vhost`.

Add the following text to `webserver.yml` just **before** the handler section:

```yaml
     - name: create apache config directory
       file:
         path: "/etc/httpd/conf.d"
         state: directory
         mode: '0755'
     - name: create another directory
       file:
         path: "/etc/httpd/conf"
         state: directory
         mode: '0755'
     - name: configure virtual host
       template:
         src: "/home/ubuntu/adv-ansible/labs/ansible-vault/conf/vhost.conf.j2"
         dest: "/etc/httpd/conf.d/vhost.conf"
       notify: httpd service
       tags:
         - vhost
     - name: configure site auth
       template:
         src: "/home/ubuntu/adv-ansible/labs/ansible-vault/conf/htpasswd.j2"
         dest: "/etc/httpd/conf/htpasswd"
       notify: httpd service
       tags:
         - vhost
```

### Asynchronously execute data-job on webservers

We need to copy the `data-job.sh` script to the managed nodes. 

Add the following task to the `webserver.yml` *after* the `configure site auth` task

```yaml
     - name: copy data job to all hosts
       copy:
         src: "/home/ubuntu/adv-ansible/labs/ansible-vault/bin/data-job.sh"
         dest: /opt/data-job.sh
         owner: ubuntu
         group: ubuntu
         mode: 755
```

Configure `webserver.yml` to asynchronously execute `data-job.sh` located on the webservers with a timeout of 600 seconds and no polling. The task should be tagged with `data-job`.

Add the following text to `webserver.yml` just **before** the handler section:

```yaml
     - name: run data job
       command: /opt/data-job.sh
       async: 600
       poll: 0
       tags:
         - data-job
```

- Your complete file should look similar to the below:

```yaml
---
 - hosts: webservers
   vars:
   become: yes
   vars_files:
     - "/home/ec2-user/automate-cloud/labs/ansible-vault/conf/confidential"
   tasks:
     - name: install httpd
       apt:
         name: apache2
         state: latest
       notify: httpd service
       tags:
         - base-install
     - name: create apache config directory
       file:
         path: "/etc/httpd/conf.d"
         state: directory
         mode: '0755'
     - name: create another directory
       file:
         path: "/etc/httpd/conf"
         state: directory
         mode: '0755'
     - name: configure virtual host
       template:
         src: "/home/ec2-user/automate-cloud/labs/ansible-vault/conf/vhost.conf.j2"
         dest: "/etc/httpd/conf.d/vhost.conf"
       notify: httpd service
       tags:
         - vhost
     - name: configure site auth
       template:
         src: "/home/ec2-user/automate-cloud/labs/ansible-vault/conf/htpasswd.j2"
         dest: "/etc/httpd/conf/htpasswd"
       notify: httpd service
       tags:
         - vhost
     - name: copy data job to all hosts
       copy:
         src: "/home/ec2-user/automate-cloud/labs/ansible-vault/bin/data-job.sh"
         dest: /opt/data-job.sh
         owner: ubuntu
         group: ubuntu
         mode: 755
     - name: run data job
       command: /opt/data-job.sh
       async: 600
       poll: 0
       tags:
         - data-job
   handlers:
     - name: Restart and enable httpd
       service:
         name: apache2
         state: restarted
         enabled: yes
       listen: httpd service
```

Use what you've learned in previous labs to create an inventory including a `webservers` group with both managed nodes.

### Execute playbook to verify your playbook works correctly

Execute playbook `webserver.yml` to verify your playbook works correctly.

Run `ansible-playbook -i inventory --ask-vault-pass webserver.yml` from the control node and provide the vault password "I love ansible".

## Conclusion

Congratulations, you've completed this hands-on lab!

## Test
Confirm the following: 
* Playbook execution prompts for vault password
* You can use tags to run specific tasks
* Playbook completes successfully
* `/opt/data-job.sh` is running on both nodes

## Congrats!

