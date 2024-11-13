Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "shell", name: "update", inline: <<-SHELL
    # Añadir contraseña para usuario vagrant
    echo "vagrant:1234" | chpasswd

    # Actualizar e instalar paquetes
    apt-get update
    apt-get install -y nginx git vsftpd openssl

    # Configurar Nginx para el sitio web existente
    mkdir -p /var/www/mi_web/html
    git clone https://github.com/cloudacademy/static-website-example /var/www/mi_web/html
    chown -R www-data:www-data /var/www/mi_web/html
    chmod -R 755 /var/www/mi_web

    # Crear directorio para el nuevo sitio web
    mkdir -p /var/www/mi_sitio_personal/html
    chown -R vagrant:www-data /var/www/mi_sitio_personal/html 
    chmod -R 755 /var/www/mi_sitio_personal/html

    # Configurar Nginx
    cp -v /vagrant/config/mi_dominio /etc/nginx/sites-available/mi_dominio
    cp -v /vagrant/config/mi_sitio_personal /etc/nginx/sites-available/
    ln -s /etc/nginx/sites-available/mi_dominio /etc/nginx/sites-enabled/
    ln -s /etc/nginx/sites-available/mi_sitio_personal /etc/nginx/sites-enabled/

    # Configurar FTPS
    mkdir -p /home/vagrant/ftp
    chown vagrant:vagrant /home/vagrant/ftp
    chmod 755 /home/vagrant/ftp

    # Generar certificado SSL para FTPS
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt -subj "/C=ES/ST=State/L=City/O=Organization/OU=Unit/CN=example.com"

    # Copiar configuración de FTPS
    cp -v /vagrant/config/vsftpd.conf /etc/vsftpd.conf

    # Reiniciar servicios
    systemctl restart nginx
    systemctl restart vsftpd
  SHELL
end