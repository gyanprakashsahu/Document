## Full Ansible Environment Configuration Sample For Multiple Servers.

### Add EPEL Repository From Internet and follow below steps:

```
yum update

yum install ansible

cd /etc/ansible/

rm hosts.yml 

rm ansible.cfg

```
### Now Copy My Playbook directory and ansible.cfg file in your /etc/ansible/.

### I explained directives used in ansible.cfg in itself.

### Read below steps carefully to understand the process of playbooks-

* inventory - You defines your hosts file here as I defined it.

* service-handlers - You can make multiple directories for multiple hostgroups and then handlers in it. I made two : "db" and "web" in my demo and then service handlers in it.

* I made "web.yml" and "db.yml" for two hostgroups. You can make as many as you have hostgroups.

* roles - This is very main part of ansible infrastructure. Put it's structure as it is as i did. In "roles" directory you can make multiple directories for multiple hostgroups. As I made "db" and "web". You can make multiple directories for multiple services in particular hostgroup directory. I made only "nginx" directory in "web". You can add more web services which is required by web server. Same I did for "db" for database and add mysql directory in it. Now in each service directory, there are two directory "tasks" and "templates". In templates directory you add all your configuration files for that particular service as i did for nginx. In "tasks" Directory You will have to make three files called "main.yml", "install.yml" and "configure.yml" for that particular service. "YOU UNDERSTAND PROPERLY WHEN YOU WILL SEE ANSIBLE STRUCTURE WHICH I PUT HERE. 

* you should know how to write yml file and how you can use various directive for running an ansible playbook. For this purpose go to ansible documentation.

* Now If you want to install various services on web servers group. Run "web.yml" file as given below but your public keys should be on servers.

```
ansible-playbook -i path/of/inventory/file -u "remote-user" --identity-file=/path/of/you/private/key playbook.yml 

ansible-playbook -i inventory/hosts -u "remote-user" --identity-file=/path/of/you/private/key web.yml

```

*  You can give path of your private key and remote user name in ansible configuration but If you are working in a team you can not give one person private key and user name for all. So use above syntax.

