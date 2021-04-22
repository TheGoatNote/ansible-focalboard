# ansible-focalboard
## To Begin

The purpose of this repo is to be able to install Focalboard with ansible in a simply way 

### Prerequisite

- One debian personal server with root access (Buster)

### Installation

- First of all we will need root access throught ansible, so we will push our key on our server
```
ssh-copy-id -i ~/.ssh/mykey.pub root@yourhost
```

- Ansible need to know which user he has to use so we will create a file "hosts" with our host and our user
```
[focalboard]
myhost ansible_user=root
```

- We have some sensitive data (postgresql pass) that we want to "protect", so we will use ansible_vault
- First we will update ansible.cfg to be able to decode our vault encrypt string
```
[defaults]
vault_password_file = ./.vault_pass
```
- Create a file .vault_pass with your own password (don't forget to push that file on .gitignore)
```
mybeautifulpassinclear
```
-  Encrypt your postgresql pass using vault and update roles/postgresql/vars/main.yml with the value
```
ansible-vault encrypt_string 'foobar' --name 'postgresql_app_pass'
postgresql_app_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          35343462363737323566333066343162383831373261373333366239326362656461323030376462
          6630616535643234383736653939353234643965366562380a616636383936343630626365303761
          36343835346336663336616139623036623561376332653663363134316563633935636366316536
          6230323462316261620a303732623062373433346131663037626130383233646535653464316536
          3964
```
- Now we are ready to launch or playbook
## Run

You can run your playbook like this :
```
ansible-playbook -i playbooks/hosts playbooks/focalboard_install.yml -e '{"domain_name": "yourdomainname"}'
```

But it will be useful to change some data like "postgresql_app_user" and to use a specific version of focalboard

```
ansible-playbook -i playbooks/hosts playbooks/focalboard_install.yml -e '{"domain_name": "yourdomainname", "postgresql_app_user": "yournewpgsql_app_user", "focalboard_version": "v0.6.5-rc3"}'
```
