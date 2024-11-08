# Vagrantfile para configurar dos servidores Apache y un balanceador Nginx
Vagrant.configure("2") do |config|
  
    # Configuración de los servidores Apache
    (1..2).each do |i|
      config.vm.define "apache#{i}" do |apache|
        apache.vm.box = "ubuntu/bionic64"
        apache.vm.hostname = "apache#{i}"
        apache.vm.network "private_network", ip: "192.168.33.1#{i}"
        apache.vm.provider "virtualbox" do |vb|
          vb.memory = "512"
          vb.cpus = 1
        end
        apache.vm.provision "shell", inline: <<-SHELL
          apt-get update
          apt-get install -y apache2
          echo "Servidor Apache#{i}" > /var/www/html/index.html
        SHELL
      end
    end
  
    # Configuración del balanceador de carga Nginx
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.hostname = "nginx"
      nginx.vm.network "private_network", ip: "192.168.33.30"
      nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      nginx.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        cat <<EOL > /etc/nginx/conf.d/load_balancer.conf
        upstream backend {
          server 192.168.33.11;
          server 192.168.33.12;
        }
  
        server {
          listen 80;
  
          location / {
            proxy_pass http://backend;
          }
        }
        EOL
        systemctl restart nginx
      SHELL
    end
  end
