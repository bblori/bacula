# Centos 7

### Bacula Server telepítése Centos 7-re.

	# sudo yum install -y bacula-director bacula-storage bacula-console bacula-client mariadb-server

### MYSQL

	# sudo systemctl start mariadb

### Adatbázis létrehozása

	```
	# /usr/libexec/bacula/grant_mysql_privileges
	# /usr/libexec/bacula/create_mysql_database -u root
	# /usr/libexec/bacula/make_mysql_tables -u bacula
	# sudo mysql_secure_installation
	# mysql -u root -p
		
	UPDATE mysql.user SET Password=PASSWORD('jelszavad’) WHERE User='bacula';
	FLUSH PRIVILEGES;
	exit
	```

### Boot inditás

	# sudo systemctl enable mariadb

### Bacula mysql használatának a beállítása

	# sudo alternatives --config libbaccats.so

	//		1           /usr/lib64/libbaccats-mysql.so
	//		2           /usr/lib64/libbaccats-sqlite3.so
	//		*+ 3        /usr/lib64/libbaccats-postgresql.so
	// az 1-es kiválasztása

	# 1
	
### Bacula mentés és visszaállítás mappái létrehozása

 	# sudo mkdir -p /bacula/backup /bacula/restore
	# sudo chown -R bacula:bacula /bacula
	# sudo chmod -R 700 /bacula
	# systemctl start bacula-sd


### Bacula jelszavak beállítása
	
	Director jelszavak

	# DIR_PASSWORD=`date +%s | sha256sum | base64 | head -c 33`
	# sudo sed -i "s/@@DIR_PASSWORD@@/${DIR_PASSWORD}/" /etc/bacula/bacula-dir.conf
	# sudo sed -i "s/@@DIR_PASSWORD@@/${DIR_PASSWORD}/" /etc/bacula/bconsole.conf
	
	Storage Daemon jelszavak

	# SD_PASSWORD=`date +%s | sha256sum | base64 | head -c 33`
	# sudo sed -i "s/@@SD_PASSWORD@@/${SD_PASSWORD}/" /etc/bacula/bacula-sd.conf
	# sudo sed -i "s/@@SD_PASSWORD@@/${SD_PASSWORD}/" /etc/bacula/bacula-dir.conf
	
	File Daemon jelszavak

	# FD_PASSWORD=`date +%s | sha256sum | base64 | head -c 33`
	# sudo sed -i "s/@@FD_PASSWORD@@/${FD_PASSWORD}/" /etc/bacula/bacula-dir.conf
	# sudo sed -i "s/@@FD_PASSWORD@@/${FD_PASSWORD}/" /etc/bacula/bacula-fd.conf

### Bacula inditása

	# sudo systemctl start bacula-dir
	# sudo systemctl start bacula-sd
	# sudo systemctl start bacula-fd

### Inditás bootolásnál.

	# sudo systemctl enable bacula-dir
	# sudo systemctl enable bacula-sd
	# sudo systemctl enable bacula-fd

### Label készítés
	# sudo bconsole
	* label
	Enter new Volume name:
	MyVolume
	Select the Pool (1-3):
	2

### HIBA elkerülése Setenforce beállítás permissive-re

	ERR=Permission denied

	# /etc/selinux/config  csere permissive-re

//--------------------------------------------------//