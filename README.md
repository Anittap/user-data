#### Install site from github and set ssh clientaliveinterval
```
#!/bin/bash




echo "ClientAliveInterval 60" >> /etc/ssh/sshd_config
systemctl restart sshd.service


yum install httpd php git -y




git clone https://github.com/Fujikomalan/aws-elb-site.git /var/website/
cp -r  /var/website/* /var/www/html/
chown -R apache:apache /var/www/html/*


systemctl restart httpd.service php-fpm.service
systemctl enable httpd.service php-fpm.service

```

#### Stress
```
#!/bin/bash

yum install stress cronie -y

systemctl start crond.service
systemctl enable crond.service

cat << 'EOF' > /etc/loadstress.sh
#!/bin/bash

sleep 3
date >> /tmp/load.log
stress --timeout 50 -c 1
EOF
chmod +x /etc/loadstress.sh

(crontab -l 2>/dev/null; echo "* * * * * /etc/loadstress.sh") | crontab -


```

#### simple website with health.html and custom site body
```
#!/bin/bash

echo "ClientAliveInterval 60" >> /etc/ssh/sshd_config
systemctl restart sshd.service

hostnamectl set-hostname taxi1-home.ap-south-1.compute.internal
yum install httpd php -y

echo "testing" > /var/www/html/health.html 

cat <<EOF > /var/www/html/index.php
<?php
\$output = shell_exec('echo $HOSTNAME');
echo "<h1><center><pre>\$output</pre></center></h1>";
echo "<h1><center>taxi.anitta.cloud</center></h1>";
?>
EOF

systemctl restart httpd.service php-fpm.service
systemctl enable httpd.service php-fpm.service
```

