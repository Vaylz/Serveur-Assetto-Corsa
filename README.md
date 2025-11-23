# Serveur-Assetto-Corsa
# 🏎️ Installation d'un Serveur Assetto Corsa + Server Manager

## Pré-requis

``` bash
sudo apt update && sudo apt upgrade
sudo apt install lib32gcc-s1 lib32stdc++6 sudo unzip
```

------------------------------------------------------------------------

# 1️⃣ Installation du serveur Assetto Corsa

## Créer un dossier pour SteamCMD

``` bash
mkdir ~/steam
cd ~/steam
```

## Télécharger SteamCMD

``` bash
wget http://media.steampowered.com/installer/steamcmd_linux.tar.gz
tar -xvzf steamcmd_linux.tar.gz -C ~/steam
```

## Lancer SteamCMD

``` bash
sudo ./steamcmd.sh
```

## Dans SteamCMD

    force_install_dir /home/nathan/ac-serveur
    login "username"
    app_update 302550 validate
    quit

------------------------------------------------------------------------

# 2️⃣ Installation de l'interface graphique : Server Manager

``` bash
mkdir web
cd /web
wget https://github.com/JustaPenguin/assetto-server-manager/releases/download/v1.7.9/server-manager_v1.7.9.zip
unzip server-manager_v1.7.9.zip -d /home/nathan/web
cd ~/web/linux
sudo nano config.yml
```

Ajouter :

``` yaml
install_path: "/home/nathan/ac-serveur"
```

------------------------------------------------------------------------

# 3️⃣ Création du service systemd

Créer :

    sudo nano /etc/systemd/system/server-manager.service

Contenu :

    [Unit]
    Description=Lancement automatique du server-manager
    After=network.target

    [Service]
    Type=simple
    User=nathan
    WorkingDirectory=/home/nathan/web/linux
    ExecStart=/home/nathan/web/linux/monscript.sh
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

Activer :

``` bash
sudo systemctl daemon-reload
sudo systemctl enable server-manager.service
sudo systemctl start server-manager.service
systemctl status server-manager.service
```

------------------------------------------------------------------------

# 🌐 Accès

-   URL : http://192.168.1.105:8772\
-   Login : admin\
-   Mot de passe : servermanager

------------------------------------------------------------------------

# Ouverture des ports pour Assetto Corsa

Pour faire fonctionner correctement un serveur Assetto Corsa avec les serveurs Kunos, vous devez
ouvrir les ports suivants sur Linux et votre box internet :

-   **UDP 9600** -- Communication du serveur\
-   **TCP 9599** -- Retour d'informations\
-   **TCP 8081** -- Interface HTTP / API du Server Manager

------------------------------------------------------------------------

## Avec nftables

Modifier la configuration :

``` bash
sudo nano /etc/nftables.conf
```

Ajouter dans `table inet filter` :

``` nft
chain input {
    type filter hook input priority 0;

    # Ports Assetto Corsa
    udp dport 9600 accept
    tcp dport 9599 accept

    # Server Manager HTTP
    tcp dport 8081 accept
}
```

activer nftables au démarrage de Linux :

``` bash
sudo systemctl start nftables
```
