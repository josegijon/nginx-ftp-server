Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "shell", name: "update", inline: <<-SHELL
    apt-get update
    apt-get install -y nginx git vsftpd openssl

    # Configure Nginx
    # Create a directory for the web server
    mkdir -p /var/www/mi_web/html

    # Clone the static website example
    git clone https://github.com/cloudacademy/static-website-example /var/www/mi_web/html

    # Change the ownership of the directory to www-data
    chown -R www-data:www-data /var/www/mi_web/html
    chmod -R 755 /var/www/mi_web

    cp -v /vagrant/mi_dominio /etc/nginx/sites-available/mi_dominio
    ln -s /etc/nginx/sites-available/mi_dominio /etc/nginx/sites-enabled/

    systemctl restart nginx

    # Configure the FTP server
    mkdir -p /home/vagrant/ftp
    sudo chown vagrant:vagrant /home/vagrant/ftp
    sudo chmod 755 /home/vagrant/ftp

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt -subj "/C=ES/ST=State/L=City/O=Organization/OU=Unit/CN=example.com"

    # cp -v /etc/vsftpd.conf /vagrant
    cp -v /vagrant/vsftpd.conf /etc/vsftpd.conf

    systemctl restart vsftpd
  SHELL
end