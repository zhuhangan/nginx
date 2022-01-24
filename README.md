## https://www.digitalocean.com/

0.安装nginx 

   sudo yum install epel-release
   sudo yum install nginx

备份
    tar -czvf nginx_$(date +'%F_%H-%M-%S').tar.gz nginx.conf sites-available/ sites-enabled/ nginxconfig.io/

 1、 上传文件 到你的服务器的/etc/nginx 目录.


2、SSL初始化

2.1 在您的服务器上运行此命令生成Diffie-Hellman keys:

     openssl dhparam -out /etc/nginx/dhparam.pem 2048 

2.2 创建一个通用的ACME-challenge目录(用于 Let's Encrypt):
     
       useradd nginx -s /sbin/nologin -M

      mkdir -p /var/www/_letsencrypt
      chown nginx /var/www/_letsencrypt

3.Cerbot
 3.1 注释掉配置中的SSL相关指令:

    sed -i -r 's/(listen .*443)/\1; #/g; s/(ssl_(certificate|certificate_key|trusted_certificate) )/#;#\1/g; s/(server \{)/\1\n    ssl off;/g' /etc/nginx/sites-enabled/api.yyuan.wang.conf /etc/nginx/sites-enabled/baby.yyuan.wang.conf

  3.2 重新加载你的NGINX服务器:

         sudo nginx -t && sudo systemctl reload nginx
  3.3 使用Certbot从 Let's Encrypt 获得SSL证书:

         sudo yum install epel-release
        sudo yum install certbot
        certbot certonly --webroot -d baby.yyuan.wang --email 807279070@qq.com -w /var/www/_letsencrypt -n --agree-tos --force-renewal
       certbot certonly --webroot -d api.yyuan.wang --email 807279070@qq.com -w /var/www/_letsencrypt -n --agree-tos --force-renewal

3.4 在配置中取消注释SSL相关指令:

       sed -i -r -z 's/#?; ?#//g; s/(server \{)\n    ssl off;/\1/g' /etc/nginx/sites-enabled/api.yyuan.wang.conf /etc/nginx/sites-enabled/baby.yyuan.wang.conf


   3.5   重新加载你的NGINX服务器:

        sudo nginx -t && sudo systemctl reload nginx

   3.6 配置Certbot，当NGINX成功更新证书时重新加载:

       echo -e '#!/bin/bash\nnginx -t && systemctl reload nginx' | sudo tee /etc/letsencrypt/renewal-hooks/post/nginx-reload.sh
        sudo chmod a+x /etc/letsencrypt/renewal-hooks/post/nginx-reload.sh

4.上线

    sudo nginx -t && sudo systemctl reload nginx

