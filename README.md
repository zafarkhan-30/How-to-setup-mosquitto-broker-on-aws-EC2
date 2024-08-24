# How-to-setup-mosquitto-broker-on-aws-EC2

============== Create Ubuntu Ec2 =====================

Create from Ec2, choose latest version of Ubuntu available

Open ports in SG: 22, 80, 443, 8883, 1883

============== Configure DNS =====================

Point the domain example.domian_name.in to IP of Ec2

============== Install and Configure Mosquitto on Ec2 =====================

$ sudo apt-get update

$ sudo apt-get install mosquitto mosquitto-clients -y

$ mosquitto_sub -h localhost -t test

$ sudo ufw allow 1883 

$ sudo ufw allow 22

$ sudo ufw enable

$ sudo systemctl restart mosquitto

$ sudo add-apt-repository ppa:certbot/certbot

$ sudo apt-get update

$ sudo apt-get install certbot -y

$ sudo ufw allow http

$ sudo ufw enable


$ sudo certbot certonly --standalone --preferred-challenges http-01 -d example.domian_name.in


$ sudo crontab -e

15 3 * * * certbot renew --noninteractive --post-hook "systemctl restart mosquitto"

$ sudo mosquitto_passwd -c /etc/mosquitto/passwd username

$ sudo vi /etc/mosquitto/conf.d/default.conf

allow_anonymous false

password_file /etc/mosquitto/passwd

$ sudo systemctl restart mosquitto

$ mosquitto_sub -h localhost -t test -u "username" -P "secret_password"

$ mosquitto_pub -h localhost -t test -m "hello world" -u "username" -P "secret_password"


$ sudo vi /etc/mosquitto/conf.d/default.conf

listener 1883 localhost

listener 8883

certfile /etc/letsencrypt/live/example.domian_name.in/cert.pem

cafile /etc/letsencrypt/live/example.domian_name.in/chain.pem

keyfile /etc/letsencrypt/live/example.domian_name.in/privkey.pem

$ sudo systemctl restart mosquitto

$ sudo ufw allow 8883

$ sudo ufw enable

$ sudo cat /etc/letsencrypt/live/example.domian_name.in/cert.pem

============== PUBLISH MESSAGE FROM mqtt.fx/Paho Library =====================

Broker Address: example.domian_name.in

Broker Port: 8883

Client ID: test

Username: username

Password: secret_password

TLS/SSL: tls1.2

CA Cert File: .../cert.pem


============== PUBLISH/SUBSCRIBE FROM TERMINAL =====================

Run on EC2

----------

$ mosquitto_pub -h example.domian_name.in -p 8883 --tls-version tlsv1.2 -i "test" --capath /etc/ssl/certs/ -t "test" -m "hello world" -u "thesuavedeveloper" -P "secret_password" -d

$ mosquitto_sub -h example.domian_name.in --capath /etc/ssl/certs/ -t test -u "thesuavedeveloper" -P "secret_password"

Run on Mac - You can stop if you want. 

----------

1. Copy certs files from ec2 and download

$ cd 

$ mkdir certs 

$ cd /etc/ssl/certs/ 

$ cp -r . /home/ubuntu/certs

$ cd 

$ zip -r certs.zip certs/

2. After Downlaoding zip to mac and unzipping.

$ cd certs

$ sudo cp -r . /etc/ssl/certs

3. Run the same command

$ mosquitto_pub -h example.domian_name.in -p 8883 --tls-version tlsv1.2 -i "test" --capath /etc/ssl/certs/ -t "test" -m "hello world" -u "username" -P "secret_password" -d

$ mosquitto_sub -h example.domian_name.in --capath /etc/ssl/certs/ -t test -u "username" -P "secret_password"
