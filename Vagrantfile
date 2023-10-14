Vagrant.configure("2") do |config|
    config.vm.define "Master" do |subconfig|
      subconfig.vm.hostname = "Master"
      subconfig.vm.box = "ubuntu/focal64"
      subconfig.vm.network "private_network", ip: "192.168.33.10"
      subconfig.vm.provider "virtualbox" do |vb|
     # Customize the amount of memory on the VM:
          vb.memory = "512"
          vb.cpus = "2"
      end
      subconfig.vm.provision "shell", inline: <<-SHELL

      # Create the user with a home directory
       sudo useradd -m -s /bin/bash altschool
       echo "$new_username:$new_password" | chpasswd

       #Grant altschool user root privileges
       sudo usermod -aG sudo altschool
       echo "User '$new_username' has been created and granted root privileges."

       # Enable SSH key-based authentication between the master and slave nodes
       sudo -u altschool ssh-keygen -t rsa -N "" -f /home/altschool/.ssh/id_rsa.pub

        # Restart the SSH service to apply the changes
       sudo systemctl restart ssh

       #copy the public key to host machine
       sudo cp /home/altschool/.ssh/id_rsa.pub /vagrant

       # Copy the contents of /mnt/altschool on the master node to /mnt/altschool/slave on the slave node
        sudo  /home/altschool/ "rsync -av /mnt/altschool /home/altschool/"


      
      SHELL
    end  
    config.vm.define "slave" do |subconfig|
      subconfig.vm.hostname = "slave"
      subconfig.vm.box = "ubuntu/focal64"
      subconfig.vm.network "private_network", ip: "192.168.33.11"
      subconfig.vm.provider "virtualbox" do |vb|
     # Customize the amount of memory on the VM:
          vb.memory = "512"
          vb.cpus = "2"
      end
      subconfig.vm.provision "shell", inline: <<-SHELL
        sudo useradd -m -s /bin/bash altschool
       #Grant altschool user root privileges
        sudo usermod -aG sudo altschool
        sudo -u altschool mkdir -p /home/altschool/.ssh
        #Copy id_rsa from master node and copy it into slave node's .ssh folder
        cat /vagrant/id_rsa.pub >> /home/altschool/.ssh/authorized_keys

        
      SHELL
    end  
   config.vm.provision "shell", inline: <<-SHELL
     # Update package lists and upgrade existing packages
    sudo apt-get update
    sudo apt-get upgrade -y

    echo "Update and upgrade done"

    # Install Apache web server
    sudo apt-get install apache2 -y

    # Install MySQL 
    sudo apt-get install mysql-server -y

    # Install PHP and required extensions
    sudo apt-get install php libapache2-mod-php php-mysql -y

    # Restart Apache to apply changes
    sudo systemctl restart apache2

    # Display a message indicating the LAMP stack is installed
     echo "LAMP stack (Apache, MySQL, PHP) has been successfully installed."

  
   SHELL

 end