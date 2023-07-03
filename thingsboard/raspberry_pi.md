# Installing ThingsBoard on Raspberry Pi

## Third-party components installation

Install Java 11 (OpenJDK)
```bash
sudo apt update
sudo apt install openjdk-11-jdk
```

ThingsBoard service installation
```
wget https://github.com/thingsboard/thingsboard/releases/download/v3.5.1/thingsboard-3.5.1.deb
sudo dpkg -i thingsboard-3.5.1.deb
```

Configure ThingsBoard database
```bash
# install **wget** if not already installed:
sudo apt install -y wget

# import the repository signing key:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# add repository contents to your system:
RELEASE=$(lsb_release -cs)
echo "deb http://apt.postgresql.org/pub/repos/apt/ ${RELEASE}"-pgdg main | sudo tee  /etc/apt/sources.list.d/pgdg.list

# install and launch the postgresql service:
sudo apt update
sudo apt -y install postgresql
sudo service postgresql start
```

Once PostgreSQL is installed you may want to create a new user or set the password for the the main user. The instructions below will help to set the password for main postgresql user
```bash
sudo su - postgres
psql
\password
\q
```

Then, press “Ctrl+D” to return to main user console and connect to the database to create thingsboard DB:
```bash
psql -U postgres -d postgres -h 127.0.0.1 -W
CREATE DATABASE thingsboard;
\q
```

Edit ThingsBoard configuration file
```bash
sudo nano /etc/thingsboard/conf/thingsboard.conf
```

Add the following lines to the configuration file. Don’t forget to replace “PUT_YOUR_POSTGRESQL_PASSWORD_HERE” with your real postgres user password:
```
# DB Configuration
export DATABASE_TS_TYPE=sql
export SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/thingsboard
export SPRING_DATASOURCE_USERNAME=postgres
export SPRING_DATASOURCE_PASSWORD=PUT_YOUR_POSTGRESQL_PASSWORD_HERE
# Specify partitioning size for timestamp key-value storage. Allowed values: DAYS, MONTHS, YEARS, INDEFINITE.
export SQL_POSTGRES_TS_KV_PARTITIONING=MONTHS
```

Memory update for slow machines (1GB of RAM). Edit ThingsBoard configuration file
```bash
sudo nano /etc/thingsboard/conf/thingsboard.conf
```

Run installation script
```bash
# --loadDemo option will load demo data: users, devices, assets, rules, widgets.
sudo /usr/share/thingsboard/bin/install/install.sh --loadDemo
```

Start ThingsBoard service
```bash
sudo service thingsboard start
```

Once started, you will be able to open Web UI using the following link:
```
http://localhost:8080/
```

Default user credentials
```
System Administrator: sysadmin@thingsboard.org / sysadmin
```

Error logs
```bash
cat /var/log/thingsboard/thingsboard.log | grep ERROR
```
