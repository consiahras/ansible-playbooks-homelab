# ansible-playbooks-homelab
Playbooks that i use to automate my Linux Host + 3 VMs for Docker swarm - K8s

#CREATE USERS siachras + deploy
1. Create with ansible-vault file : user_passwords.yml      (i use password in a file in clear text for now in the same directory ./vault_password_users)
2. Create inventory file
3. Create user_lists.yml
4. Run the localhost_users.yml  
sudo ansible-playbook  --vault-password-file=./vault_password_users localhost_users.yml


#INSTALL DEPENDENCIES FOR VIRT-MANAGER etc
1. Run playbook - For QEMU to connect with virt-manager,  most likely needs a reboot after , socket to virt-manager couldnt connect before reboot
sudo ansible-playbook  localhost_libvirt.yml

If you want to allo other users to be able to user virt-manager and connect to libvirtd , you can edit manually /etc/libvirt/libvirt.conf  and put
unix_sock_ro_perms = "0770"




#VIRTUAL MACHINES MANAGEMENT
To Manage Virtual Machines , will use this collection : https://docs.ansible.com/ansible/latest/collections/community/libvirt/virt_module.html
1. ansible-galaxy collection install community.libvirt







