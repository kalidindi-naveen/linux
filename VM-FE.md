## FrontEnd ..> Nginx

### Setup FE:
1. Install Nginx
    ```
    dnf install nginx -y 
    ```
2. Enable Nginx 
    ```
    systemctl enable nginx
    ```
3. Start Nginx
    ```
    systemctl start nginx
    ```
4. Remove the default content that web server is serving.
    ```
    rm -rf /usr/share/nginx/html/*
    ```
5. Download the frontend content
    ```
    curl -o /tmp/frontend.zip https://expense-builds.s3.us-east-1.amazonaws.com/expense-frontend-v2.zip
    ```
6. Extract the frontend content.
    ```
    cd /usr/share/nginx/html
    ```
    ```
    unzip /tmp/frontend.zip
    ```
7. FE need to know where is BE
    ```
    vim /etc/nginx/default.d/expense.conf
    ```
    ```
    proxy_http_version 1.1;

    location /api/ { proxy_pass http://3.239.80.111:8080/; }

    location /health {
        stub_status on;
        access_log off;
    }
    ```
8. Restart Nginx
    ```
    systemctl restart nginx
    ```