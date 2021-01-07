# nq-server-simple
nodequery-server nodejs version

具体部署方法(以centos7为例)：
初始化账号密码admin/admin
第一步、安装nodejs,并更新到最新稳定版  
yum -y install nodejs
npm install -g n  #安装n版本管理工具 yarn管理工具和pm2进程守护工具  
n stable  
npm install -g yarn pm2  


第二步、克隆代码，安装依赖  
yum -y install git  #安装git  
git clone https://github.com/cnly1987/nq-server-simple.git  #克隆代码  
cd nq-server-simple  
yarn      #安装依赖  
node ace build  


第三步：配置并运行  
在.env文件夹按照 格式填写mongodb连接地址。【如何安装mongodb这里就不介绍了】,记得修改下APIKEY  
然后把tmp文件夹和.env 拷贝到build文件夹。  
.env 文件可以设置运行的端口，自己自行设置。  
执行pm2 start build/server.js  
然后pm2 list 查看下有没有问题，状态running表示OK了。  默认端口是2323， 地址是127.0.0.1:2323  


第四步：配置nginx:  
nq-server.conf:  

server {  
    listen 80;  
    server_name  monitorx.xyz www.monitorx.xyz;  
    client_max_body_size 200M;  
    client_body_buffer_size 200M;  
    client_header_buffer_size 2048k;  
    large_client_header_buffers 4 2048k;  
    fastcgi_buffer_size 128k;  
    fastcgi_buffers 32 32k;  

    location / {  
        proxy_set_header        Host $host;  
        proxy_set_header        X-Real-IP $remote_addr;  
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;  
        proxy_set_header        X-Forwarded-Proto $scheme;  
        proxy_pass http://127.0.0.1:2323;  
   
            proxy_temp_file_write_size 64k;  
            proxy_connect_timeout 10080s;  
            proxy_send_timeout 10080;  
            proxy_read_timeout 10080;  
            proxy_buffer_size 64k;  
            proxy_buffers 16 32k;  
            proxy_busy_buffers_size 64k;  
            proxy_redirect off;  
            proxy_request_buffering off;  
            proxy_buffering off;  
        }  

            error_page   500 502 503 504  /50x.html;  
            location = /50x.html {  
                root   html;  
            }  
}
