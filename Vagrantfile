Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "shell", name: "update", inline: <<-SHELL
    # Actualizar e instalar paquetes
    apt-get update
    apt-get install -y nginx git vsftpd openssl

    # Configurar Nginx
    mkdir -p /var/www/mi_web/html
    git clone https://github.com/cloudacademy/static-website-example /var/www/mi_web/html
    chown -R www-data:www-data /var/www/mi_web/html
    chmod -R 755 /var/www/mi_web
    cp -v /vagrant/mi_dominio /etc/nginx/sites-available/mi_dominio
    ln -s /etc/nginx/sites-available/mi_dominio /etc/nginx/sites-enabled/
    systemctl restart nginx

    # Configurar FTPS
    mkdir -p /home/vagrant/ftp
    sudo chown vagrant:vagrant /home/vagrant/ftp
    sudo chmod 755 /home/vagrant/ftp

    # Generar certificado SSL para FTPS
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt -subj "/C=ES/ST=State/L=City/O=Organization/OU=Unit/CN=example.com"

    # Copiar configuración de FTPS
    cp -v /vagrant/vsftpd.conf /etc/vsftpd.conf

    # Reiniciar servicio FTPS
    systemctl restart vsftpd
  SHELL
end