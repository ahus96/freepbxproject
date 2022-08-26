# FreePBX project on AWS running on Ubuntu
FreePBX is an open-source community GUI that manages Asterisk VoIP PBX. In this project I installed and setup FreePBX onto an EC2 instance as well as setting up MYSQL and SMTP alongside this.

## 1.Introduction/creating the keypair
Firstly I subscribed to FreePBX via aws marketplace, then I created a key pair, this is needed in order to access the FreePBX instance to connect to it via ssh. I did this by going to EC2 > key pairs > create key pair using key pair type RSA and private file key format as .pem
 
![image](https://user-images.githubusercontent.com/36034401/186896766-13868f46-3aa6-43b2-a811-43cd1f06c6c9.png)

## 2.Creating the EC2 instance 
The second step was to create the ec2 instance, this was done by going to ec2 > instances > launch instances. I then went to AWS marketplace within the instance and searched for FreePBX. I chose t2.micro as the instance type created a new security group allowing traffic inbound from all IP/ports in order to ssh into the ec2 instance without any disruptions. 
 


# 3.Accessing the instance using ssh 
The next step was to access the ec2 instance using cmd, I ran cmd as administrator and then ran the following commands.
‘ssh -i "key6.pem" ubuntu@ec2-44-205-247-19.compute-1.amazonaws.com’
This command allowed me to find the keypair I created earlier which was on my own system to access the ec2 instance, the second part of the command is the ipv4 DNS to also access the instance. Once I accessed the instance using the command, it was time to setup the FreePBX
# 4.Setting up MySQL, SMTP and FreePBX
The following command/steps were then used to set up MYSQL, SMTP and FreePBX.
Firstly, I accessed the root user
'sudo -i' 

'cat <<EOF > /etc/odbcinst.ini                  
[MySQL]
Description = ODBC for MySQL (MariaDB)
Driver = /usr/local/lib/libmaodbc.so
FileUsage = 1
EOF'
  
Then it was time to to setup the MYSQL database connection
  
'cat <<EOF > /etc/odbc.ini
[MySQL-asteriskcdrdb]
Description = MySQL connection to 'asteriskcdrdb' database
Driver = MySQL
Server = localhost
Database = asteriskcdrdb
Port = 3306
Socket = /var/run/mysqld/mysqld.sock
EOF'
 

# 5.Setting up FreePBX
The following commands were then run to setup the FreePBX
'cd /usr/src
wget http://mirror.freepbx.org/modules/packages/freepbx/freepbx-15.0-latest.tgz
tar vxfz freepbx-15.0-latest.tgz
rm -f freepbx-15.0-latest.tgz
touch /etc/asterisk/{modules,cdr}.conf
cd freepbx
./start_asterisk start
./install -n'









## Setting up SMTP
Now it was time to setup our SMTP and postfix for SMTP alerts.
  
rm /var/lib/dpkg/lock*
rm /var/cache/apt/archives/lock
rm /var/lib/apt/lists/lock
 
apt-get install postfix -y (postfix)
  
Once this was finished running I needed to go to the configuration file of the postfix to finish the config.
  
'nano /etc/postfix/main.cf'
 
  
this opened a file, and I was required to edit this file with the following.

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
 Normally an error will occur which I also ran into, this was because port 25 was used by another service. This was freed by running.
'lsof -i :25
kill -9 1928
Once the process was freed, I then installed the mail utils and downloaded these files I also started the post fix.
‘apt-get install mailutils’
‘sudo postfix start’
‘nano /etc/postfix/generic’
‘root example@example.com
root@localhost example@example.com
root@localhost.localdomain example@example.com
root@freepbx example@example.com
root@freepbx.localdomain example@example.com
asterisk example@example.com
asterisk@localhost example@example.com
asterisk@localhost.localdomain example@example.com
asterisk@freepbx example@example.com
asterisk@freepbx.localdomain example@example.com
vm@asterisk example@example.com’
‘nano /etc/postfix/main.cf
I then setup the smtp generic maps and post map the file
‘smtp_generic_maps =hash:/etc/postfix/generic’
‘postmap /etc/postfix/generic’
Finally it was time to restart the service
‘service postfix restart’
















## 6. Accessing FreePBX GUI
To access the FreePBX GUI I firstly I took the public IPV4 address of my instance, and this then opened the FreePBX page.

When coming to the first page I created my user details such as username and password.

 

This then allowed me to access the dashboard and extensions pages to provision sip trunks and hosted services to start making and receiving VOIP calls.

 






  

  

  

  
  



