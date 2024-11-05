# Vagrantfile para crear 3 máquinas virtuales
Vagrant.configure("2") do |config|

    # Configuración de la máquina Apache1
    config.vm.define "apache1" do |apache1|
      apache1.vm.box = "ubuntu/bionic64"
      apache1.vm.hostname = "apache1"
      apache1.vm.network "private_network", ip: "192.168.33.10"
      apache1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Instalación de Apache y configuración del directorio compartido
      apache1.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y apache2
        ufw allow in "Apache"
        systemctl enable apache2
        systemctl start apache2
      SHELL
      apache1.vm.synced_folder ".", "/var/www/html", type: "rsync"
    end
  
    # Configuración de la máquina Apache2
    config.vm.define "apache2" do |apache2|
      apache2.vm.box = "ubuntu/bionic64"
      apache2.vm.hostname = "apache2"
      apache2.vm.network "private_network", ip: "192.168.33.11"
      apache2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Instalación de Apache y configuración del directorio compartido
      apache2.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y apache2
        ufw allow in "Apache"
        systemctl enable apache2
        systemctl start apache2
      SHELL
      apache2.vm.synced_folder ".", "/var/www/html", type: "rsync"
    end
  
    # Configuración de la máquina NGINX (Load Balancer)
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.hostname = "nginx"
      nginx.vm.network "private_network", ip: "192.168.33.12"
      nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      # Instalación de NGINX y configuración para balanceo de carga
      nginx.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        ufw allow 'Nginx Full'
  
        # Configuración del balanceo de carga
        echo "
        upstream backend {
            server 192.168.33.10;
            server 192.168.33.11;
        }
  
        server {
            listen 80;
  
            location / {
                proxy_pass http://backend;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
            }
        }
        " > /etc/nginx/sites-available/default
  
        systemctl enable nginx
        systemctl restart nginx
      SHELL
    end
  
  end
  