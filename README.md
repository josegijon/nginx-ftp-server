# Práctica: Instalación y Configuración de Servidor Web Nginx con FTPS

## Introducción

Esta práctica consiste en la configuración de un servidor web Nginx con soporte FTPS utilizando Vagrant para la virtualización. Se establecerá un entorno de desarrollo que permite alojar múltiples sitios web y realizar transferencias seguras de archivos mediante FTPS.

## Tabla de contenidos
---

1. [Requisitos Previos](#requisitos-previos)
2. [Estructura del Proyecto](#estructura-del-proyecto)
3. [Instalación del Servidor Web Nginx](#instalación-del-servidor-web-nginx)
   - [Actualización e Instalación](#1-actualización-e-instalación)
   - [Comprobación del Servicio](#2-comprobación-del-servicio)
4. [Configuración de Sitios Web](#configuración-de-sitios-web)
   - [Creación de Carpetas](#1-creación-de-carpetas)
   - [Configuración de Permisos](#2-configuración-de-permisos)
   - [Configuración de Nginx](#3-configuración-de-nginx)
5. [Configuración del Servidor FTPS](#configuración-del-servidor-ftps)
   - [Instalación de vsftpd](#1-instalación-de-vsftpd)
   - [Configuración de SSL](#2-configuración-de-ssl)
   - [Configuración de vsftpd](#3-configuración-de-vsftpd)
6. [Verificación y Pruebas](#verificación-y-pruebas)
   - [Pruebas Web](#1-pruebas-web)
   - [Pruebas FTPS](#2-pruebas-ftps)
7. [Notas de Seguridad](#notas-de-seguridad)

## Requisitos Previos

- VirtualBox instalado
- Vagrant instalado
- Cliente FTPS (FileZilla recomendado)
- Editor de texto
- Conexión a Internet

## Estructura del Proyecto

```bash
proyecto/
├── Vagrantfile
├── config/
│   ├── mi_dominio                 # Configuración del primer sitio web
│   ├── mi_sitio_personal         # Configuración del segundo sitio web
│   └── vsftpd.conf              # Configuración del servidor FTPS
└── README.md
```

## Instalación del Servidor Web Nginx

### 1. Actualización e Instalación

```
apt-get update
apt-get install -y nginx
```
### 2. Comprobación del Servicio

```
systemctl status nginx
```

### Configuarción de Sitios Web

#### 1. Creación de Carpetas

```
mkdir -p /var/www/mi_sitio_personal/html
mkdir -p /var/www/mi_web/html
```

#### 2. Configuración de Permisos

```
chown -R vagrant:www-data /var/www/mi_sitio_personal/html
chmod -R 775 /var/www/mi_sitio_personal/html
```

#### 3. Configuración de Nginx

Archivo de configuración de Nginx para mi_sitio_personal:

```
server {
    listen 80;
    listen [::]:80;

    root /var/www/mi_sitio_personal/html;
    index index.html index.htm index.nginx-debian.html;

    server_name mi_sitio_personal.local;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Configuración del Servidor FTPS

#### 1. Instalación de vsftpd

```
apt-get install -y vsftpd
```

#### 2. Configuración de SSL

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt
```

#### 3. Configuración de vsftpd

Archivo vsftpd.conf:

```
#
listen=YES
#
listen_port=21
#
implicit_ssl=NO
#
listen_ipv6=NO
#
anonymous_enable=NO
#
local_enable=YES
#
write_enable=YES
#
local_umask=022
#
#anon_upload_enable=YES
#
#anon_mkdir_write_enable=YES
#
dirmessage_enable=YES
#
use_localtime=YES
#
xferlog_enable=YES
#
connect_from_port_20=YES
#
#chown_uploads=YES
#chown_username=whoever
#
#xferlog_file=/var/log/vsftpd.log
#
#xferlog_std_format=YES
#
#idle_session_timeout=600
#
#data_connection_timeout=120
#
#nopriv_user=ftpsecure
#
#async_abor_enable=YES
#
#ascii_upload_enable=YES
#ascii_download_enable=YES
#
#ftpd_banner=Welcome to blah FTP service.
#
#deny_email_enable=YES
#banned_email_file=/etc/vsftpd.banned_emails
#
#chroot_local_user=YES
#
#chroot_local_user=YES
#chroot_list_enable=YES
#chroot_list_file=/etc/vsftpd.chroot_list
#
#ls_recurse_enable=YES
#
secure_chroot_dir=/var/run/vsftpd/empty
#
pam_service_name=vsftpd
#
rsa_cert_file=/etc/ssl/certs/vsftpd.crt
rsa_private_key_file=/etc/ssl/private/vsftpd.key
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
local_root=/home/vagrant/ftp
```

### Verificación y Pruebas

#### 1.Pruebas Web

Accede a tu sitio web:

```
http://mi_sitio_personal.local
```

#### 2.Pruebas FTPS

Conexión mediante FileZilla:

·Host: 192.168.33.10
·Usuario: vagrant
·Contraseña: 1234
·Puerto: 21

## Notas de Seguridad

- La contraseña del usuario vagrant está en texto plano en el Vagrantfile
- Se usa un certificado SSL autofirmado para FTPS
- Los permisos de directorio están configurados para desarrollo
- En producción, se recomienda implementar medidas de seguridad adicionales