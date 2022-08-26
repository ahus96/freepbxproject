# FreePBXAWS
FreePBX project on AWS running on Ubuntu
FreePBX is an open-source community GUI that manages Asterisk VoIP PBX. In this project I installed and setup Freepbx 
onto an EC2 instance as well as setting up MYSQL and SMTP alongside this.

# Introduction/creating the keypair
Firstly I subscribed to Freepbx via aws marketplace, then I created a key pair, this is needed in order to access
the freepbx instance to connect to it via ssh. I did this by going to EC2 > key pairs > create key pair using key pair 
type RSA and private file key format as .pem

# Creating the EC2 instance 
The second step was to create the ec2 instance, this was done by going to ec2 > instances > launch instances. I then went to AWS marketplace 
within the instance and searched for freepbx. I chose t2.micro as the instance type created a new security groupe allowing traffic inbound from all IP/ports 
in order to ssh into the ec2 instance without any disruptions. 

# Acessing instance using ssh 
The next step was to access the ec2 instance using cmd, I ran cmd as administrator and then ran the following commands.
- ssh -i "key6.pem" ubuntu@ec2-44-205-247-19.compute-1.amazonaws.com
This command allowed me to find the keypair I created earlier which was on my own system to access the ec2 instance, the second part of the command
is the ipv4 dns to also access the instance.
Once I accessed the instance using the command, it was time to setup the FreePBX

# Setting up MySQL, SMTP and FreePBX
The following command/steps were then used to set up MYSQL, SMTP and FreePBX.
'sudo -i' (this command was used to access the root)

'cat <<EOF > /etc/odbcinst.ini                  
[MySQL]
Description = ODBC for MySQL (MariaDB)
Driver = /usr/local/lib/libmaodbc.so
FileUsage = 1
EOF'
  
'cat <<EOF > /etc/odbc.ini
[MySQL-asteriskcdrdb]
Description = MySQL connection to 'asteriskcdrdb' database
Driver = MySQL
Server = localhost
Database = asteriskcdrdb
Port = 3306
Socket = /var/run/mysqld/mysqld.sock
EOF'
(The above commands were then used to setup the MYSQL database connection)

# Setting up FreePBX
The following commands were then run to setup the FreePBX

'cd /usr/src
wget http://mirror.freepbx.org/modules/packages/freepbx/freepbx-15.0-latest.tgz
tar vxfz freepbx-15.0-latest.tgz
rm -f freepbx-15.0-latest.tgz
touch /etc/asterisk/{modules,cdr}.conf
cd freepbx
./start_asterisk start
./install -n'

# Setting up SMTP
Now it was time to setup our SMTP and postfix for SMTP alerts.
  
rm /var/lib/dpkg/lock*
rm /var/cache/apt/archives/lock
rm /var/lib/apt/lists/lock
 
apt-get install postfix -y (postfix)
  
Once this was finsihed running I needed to go to the configuration file of the postfix to finish the config.
  
'nano /etc/postfix/main.cf'
  
this opened a file and I was required to edit this file with the following.

'relayhost = [smptp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_secutiy_ptions = noanonymous
smtp_use_tls = yes'
 
Now it was time to go to the password file
  
'nano /etc/postfix/sasl_passwd'

This opened another file where I wrote my domain domain and email address whcih was used to send notfication/email
[smtp.gmail.com]:587 example@gmai.com

It was then time to add this file into the postmap file the command for this was.
postmap hash:/etc/postfix/sasl_passwd

Then to reload the postfix

'sudo postfix reload'
  
Normally an error will occur whcih I also ran into, this was because port 25 was used by another service. This was freed by running.

'lsof -i :25
kill -9 1928'
 


  

  

  

  
  



