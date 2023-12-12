
# Setup Ansible
1. Install ansibe on Ubuntu 22.04 
   ```sh 
   sudo apt update
   sudo apt install software-properties-common
   sudo add-apt-repository --yes --update ppa:ansible/ansible
   sudo apt install ansible
   ```

2. Add Jenkins master and slave as hosts 
Add jenkins master and slave private IPs in the inventory file 
in this case, we are using /opt is our working directory for Ansible. 
   ``` 
 [jenkinsmaster]
 41.111.155.174   
 [jenkinsmaster:vars]
 ansible_user=adminuser

 [jenkinsslave]
 166.199.238.17
 [jenkinsslave:vars]
 ansible_user=adminuser
   ```

1. Test the connection  
   ```sh
   ansible -i hosts all -m ping 
   ```