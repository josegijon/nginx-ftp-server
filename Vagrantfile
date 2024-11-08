Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "shell", name: "update", inline: <<-SHELL
    apt-get update
    apt-get install -y nginx git vsftpd openssl
    systemctl status nginx

    cp -v /vagrant/mi_dominio /etc/nginx/sites-available/mi_dominio
    ln -s /etc/nginx/sites-available/mi_dominio /etc/nginx/sites-enabled/
    systemctl restart nginx

    # apt-get install -y vsftpd
  SHELL

  # Provision the VM with Nginx and a static website
  config.vm.provision "shell", name: "nginx" inline: <<-SHELL, run: "once"

    # Create a directory for the web server
    mkdir -p /var/www/mi_web/html

    # Clone the static website example
    git clone https://github.com/cloudacademy/static-website-example /var/www/mi_web/html

    # Change the ownership of the directory to www-data
    chown -R www-data:www-data /var/www/mi_web/html
    chmod -R 755 /var/www/mi_web

    touch /etc/nginx/sites-available/mi_dominio
    cp -v /etc/nginx/sites-available/mi_dominio /vagrant

    systemctl restart nginx
  SHELL

  config.vm.provision "shell", name: "ftp" inline: <<-SHELL, run: "once"
    # Create a directory for the FTP server
    mkdir -p /home/ftpuser/ftp
  SHELL
end