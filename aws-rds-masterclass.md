# AWS RDS Masterclass Commands

## Databases on EC2 Instance - Demo
### Begin Configuration :
```bash
sudo su -
yum -y install mariadb-server wget
systemctl enable mariadb
systemctl start mariadb
yum -y update
```
### Set Environmental Variables
```bash
DBName=ec2db
DBPassword=admin123456
DBRootPassword=admin123456
DBUser=ec2dbuser
```
### Database Setup on EC2 Instance:
```bash
echo "CREATE DATABASE ${DBName};" >> /tmp/db.setup
echo "CREATE USER '${DBUser}' IDENTIFIED BY '${DBPassword}';" >> /tmp/db.setup
echo "GRANT ALL PRIVILEGES ON *.* TO '${DBUser}'@'%';" >> /tmp/db.setup
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
mysqladmin -u root password "${DBRootPassword}"
mysql -u root --password="${DBRootPassword}" < /tmp/db.setup
rm /tmp/db.setup
```
### Adding some dummy data to the Database inside EC2 Instance:
```bash
mysql -u root --password="${DBRootPassword}"
USE ec2db;
CREATE TABLE table1 (id INT, name VARCHAR(45));
INSERT INTO table1 VALUES(1, 'Virat'), (2, 'Sachin'), (3, 'Dhoni'), (4, 'ABD');
SELECT * FROM table1;
```
### Migration of Database in EC2 Instance to RDS Database:
```bash
mysqldump -u root -p ec2db > ec2db.sql
mysql -h <replace-rds-end-point-here> -P 3306 -u rdsuser -p rdsdb < ec2db.sql
mysql -h <replace-rds-end-point-here> -P 3306 -u rdsuser -p
USE rdsdb
SELECT * FROM table1;
```



