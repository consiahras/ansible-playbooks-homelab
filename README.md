# ansible-playbooks-homelab
Playbooks that i use to automate my Linux Host + 3 VMs for Docker swarm - K8s

#CREATE USERS siachras + deploy
1. Create with ansible-vault file : user_passwords.yml      (i use password in a file in clear text for now in the same directory ./vault_password_users)
2. Create inventory file
3. Create user_lists.yml
4. Run the localhost_users.yml  
sudo ansible-playbook  --vault-password-file=./vault_password_users localhost_users.yml


#INSTALL DEPENDENCIES FOR VIRT-MANAGER etc
1. Run playbook 
sudo ansible-playbook  localhost_libvirt.yml



#VIRTUAL MACHINES MANAGEMENT
To Manage Virtual Machines , will use this collection : https://docs.ansible.com/ansible/latest/collections/community/libvirt/virt_module.html
1. ansible-galaxy collection install community.libvirt







