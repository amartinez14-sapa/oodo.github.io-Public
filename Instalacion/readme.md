# Com instal·lar Odoo 17 a Debian 12
## Índex
1. [Introducció](#introducció)
2. [Prerequisites](#prerequisites)
3. [Pas 1: Actualitzar el sistema](#pas-1-actualitzar-el-sistema)
4. [Pas 2: Instal·lar les dependències necessàries](#pas-2-installar-les-depend%C3%A8ncies-necess%C3%A0ries)
5. [Pas 3: Descarregar Odoo 17](#pas-3-descarregar-odoo-17)
6. [Pas 4: Configurar l'entorn virtual i instal·lar Odoo](#pas-4-configurar-lentorn-virtual-i-installar-odoo)
7. [Pas 5: Configurar Odoo](#pas-5-configurar-odoo)
8. [Pas 6: Crear un servei systemd per a Odoo](#pas-6-crear-un-servei-systemd-per-a-odoo)
9. [Pas 7: Iniciar i habilitar el servei Odoo](#pas-7-iniciar-i-habilitar-el-servei-odoo)
10. [Pas 8: Configurar un firewall](#pas-8-configurar-un-firewall)
11. [Pas 9: Accedir a Odoo](#pas-9-accedir-a-odoo)
12. [Conclusió](#conclusió)
    
    
## Introducció
Odoo és una plataforma de codi obert per a la gestió empresarial que ofereix una gran varietat de funcionalitats, com ara CRM, inventari, comptabilitat, etc. En aquesta guia, aprendrem com instal·lar Odoo 17 en un servidor Debian 12.

## Prerequisites
- Un servidor Debian 12 amb accés SSH.
- Accés a una compte de superusuari o a un usuari amb privilegis sudo.

## Pas 1: Actualitzar el sistema
Primer de tot, assegurem-nos que el nostre sistema està actualitzat executant les següents ordres:

```bash
sudo apt update
sudo apt upgrade
```

## Pas 2: Instal·lar les dependències necessàries
Odoo necessita algunes dependencies que hem d'instal·lar abans de continuar amb la seva instal·lació. Utilitzem apt per instal·lar-les:

```bash
apt install build-essential wget git python3-pip python3-dev python3-venv \
    python3-wheel libfreetype6-dev libxml2-dev libzip-dev libsasl2-dev \
    python3-setuptools node-less libjpeg-dev zlib1g-dev libpq-dev \
    libxslt1-dev libldap2-dev libtiff5-dev libopenjp2-7-dev libcap-dev \            postgresql
```

## Pas 3: Descarregar Odoo 17
Anem al directori /opt i descarreguem el codi font d'Odoo 17 mitjançant Git:

```bash
cd /opt
sudo git clone https://www.github.com/odoo/odoo --depth 1 --branch 17.0 --single-branch
```

## Pas 4: Configurar l'entorn virtual i instal·lar Odoo
Creem un entorn virtual per a Odoo i instal·lem les seves dependencies Python:

```bash
cd /opt/odoo
python3 -m venv odoo17-venv
source odoo17-venv/bin/activate
sudo /opt/odoo/odoo-venv/bin/pip3 install wheel
sudo /opt/odoo/odoo-venv/bin/pip3 install -r requirements.txt
```

## Pas 5: Configurar Odoo
Creem un fitxer de configuració per a Odoo:

```bash
sudo cp /opt/odoo/debian/odoo.conf /etc/odoo.conf
sudo nano /etc/odoo.conf
```

Edita les opcions següents segons les teves preferències:

```plaintext
"Esta es la contraseña que permite las operaciones en la base de datos:"
[options]
admin_passwd = m0d1fyth15
db_host = False
db_port = False
db_user = odoo17
db_password = False
addons_path = /opt/odoo17/odoo17/addons,/opt/odoo17/odoo17/custom-addons
```

## Pas 6: Crear un servei systemd per a Odoo
Creem un fitxer de servei systemd per gestionar el procés d'Odoo:

```bash
sudo nano /etc/systemd/system/odoo.service
```

Afegim el següent contingut:

```plaintext
[Unit]
Description=Odoo
# Requires=postgresql
# After=network.target postgresql.service

[Service]
Type=simple
SyslogIdentifier=odoo
PermissionsStartOnly=true
User=root
Group=root
ExecStart=/opt/odoo/odoo-venv/bin/python3 /opt/odoo/odoo-bin -c /etc/odoo.conf
StandardOutput=journal+console

[Install]
WantedBy=default.target
```

Guarda i tanca el fitxer. A continuació, recarreguem els serveis systemd:

```bash
sudo systemctl daemon-reload
```

## Pas 7: Iniciar i habilitar el servei Odoo
Iniciem el servei Odoo i l'habilitem perquè s'iniciï automàticament al reiniciar el sistema:

```bash
sudo systemctl start odoo
sudo systemctl enable odoo
```

## Pas 8: Configurar un firewall
Si utilitzes un firewall, assegura't de permetre el tràfic al port 8069, que és el port predeterminat d'Odoo:

```bash
sudo ufw allow 8069/tcp
```

## Pas 9: Accedir a Odoo
Ara que Odoo està en marxa, obre el teu navegador web i accedeix a la següent adreça:

```
http://<server_IP>:8069
```

Inicia sessió amb l'usuari 'admin' i la contrasenya que has configurat al fitxer de configuració.

## Conclusió
Ara has instal·lat Odoo 17 al teu servidor Debian 12 i pots començar a utilitzar-lo per a la gestió empresarial.
