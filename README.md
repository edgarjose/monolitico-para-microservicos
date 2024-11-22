# monolitico-para-microservicos
A migração de uma arquitetura monolítica para microserviços

Resumo das Etapas para Backup e Restauração de um Website WordPress com Base de Dados em RDS e EC2

## 1 - Backup da Base de Dados
Faz o dump (cópia) da base de dados WordPress:

mysqldump -u admin -p wordpressdb > wordpressdb.sql

## 2 - Transferir o Dump da Base de Dados para um Bucket S3
Carrega o ficheiro dump para o bucket S3:

aws s3 cp wordpressdb.sql s3://bucketparabackup001/database_backup/

## 3 - Backup do Website (Arquivos)
Faz backup dos arquivos do site WordPress (diretório html):

sudo tar czf wordpress.tar.gz html

## 4 - Transferir o Arquivo do Website para o Bucket S3
Carrega o arquivo do website para o bucket S3:

aws s3 cp wordpresswebsite.tar.gz s3://ec2-backup-bucket01/website-backup/

## 5 - Criar uma Nova Instância EC2 com Amazon Linux 2
Atualiza os pacotes na nova instância EC2 (Amazon Linux 2):

sudo yum update -y

## Instalar o Apache:
sudo yum install httpd -y

sudo systemctl start httpd

sudo systemctl enable httpd

## Instala o PHP 7.4:
sudo amazon-linux-extras install -y php7.4

sudo systemctl restart httpd

Instala o cliente de base de dados MariaDB:

sudo yum install mariadb

## 7 - Transferir os Arquivos da base de dados do S3 para EC2
Faz o download dos arquivos do website a partir do S3 para a instância EC2:

aws s3 sync s3://ec2-backup-bucket01/wordpressdb.sql/ /home/ec2-user

## 8 - Transferir os Arquivos do Website do S3 para EC2
Faz o download dos arquivos do website a partir do S3 para a instância EC2:

aws s3 sync s3://ec2-backup-bucket01/website-backup/ /home/ec2-user

## 9 - Transferir o Dump da Base de Dados para o RDS
mysql -h demo-database001.crhrnvptqdno.us-east-1.rds.amazonaws.com -u admin -p wordpredb < wordpressdb.sql

## 10 - Descompactar os Arquivos do Website e Mover para o Diretório Apache
tar xvf community_images.tar.gz

cd html

sudo vi wp-config.php

sudo mv html/* /var/www/html/

