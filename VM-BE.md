## Backend ..> NodeJS

### Setup BE:

1. Install NodeJS ..> V20

    ```
    [ root@ip-172-31-8-88 ~ ]# dnf module list  nodejs
    Name       Stream    Profiles                                 Summary
    nodejs     18        common [d], development, minimal, s2i    Javascript runtime
    nodejs     20        common [d], development, minimal, s2i    Javascript runtime
    ```
    ```
    dnf module disable nodejs:18 -y
    ```
    ```
    dnf module enable nodejs:20 -y
    ```
    ```
    dnf install nodejs -y
    ```
2. Check NodeJS Version
    ```
    [ root@ip-172-31-8-88 ~ ]# node -v
    v20.16.0
    ```
3. Configure Application User
- create dedicated user to run application
- when we are running applications we configure application users, to avoid errors
    ```
    useradd expense
    ```
    ```
    [ root@ip-172-31-8-88 ~ ]# id expense
    uid=1002(expense) gid=1002(expense) groups=1002(expense)
    ```
4. Keep Application code in standard location
    ```
    mkdir /app
    ```
5. Download Code to /tmp folder
    ```
    curl -o /tmp/backend.zip https://expense-builds.s3.us-east-1.amazonaws.com/expense-backend-v2.zip
    ```
6. Extract code to /app
    ```
    cd /app
    unzip /tmp/backend.zip
    ```
    ```
    [ root@ip-172-31-8-88 /app ]# ls
    DbConfig.js  TransactionService.js  index.js  package.json  schema
    ```
7. Download  Application Dependencies
    ```
    npm install
    ```
    ```
    [ root@ip-172-31-8-88 /app ]# ls
    DbConfig.js            index.js      package-lock.json  schema
    TransactionService.js  node_modules  package.json
    ```
8. Create Systemd Service 
- dnf install nginx -y 
- systemctl start nginx //start nginx with this command bcs we downloaded from package manager
- Here we need to manually configure service
- node index.js //it will start application
- /etc/systemd/system ...> place all service files
- extension ..> *.service
    ```
    vim /etc/systemd/system/backend.service
    ```
    ```
    [Unit]
    Description = Backend Service

    [Service]
    User=expense
    Environment=DB_HOST="3.237.172.249"
    ExecStart=/bin/node /app/index.js
    SyslogIdentifier=backend

    [Install]
    WantedBy=multi-user.target
    ```
9. Load Service
    ```
    systemctl daemon-reload
    ```
10. Start Backend
    ```
    systemctl start backend
    ```
11. Enable Backend
    ```
    systemctl enable backend
    ```
    ```
    [ root@ip-172-31-8-88 /app ]# netstat -lntp
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1254/sshd: /usr/sbi
    tcp6       0      0 :::22                   :::*                    LISTEN      1254/sshd: /usr/sbi
    ```
12. Check Logs
    ```
    [ root@ip-172-31-8-88 /app ]# cat /var/log/messages
    ```
13. For this application to work fully functional we need to load schema to the Database.
- application will not start
- expense user unable to connect to DB
- We need to load the schema. To load schema we need to install mysql client.
    ```
    dnf install mysql -y
    ```
14. Load Schema 
    ```
    mysql -h 3.237.172.249 -uroot -pExpenseApp@1 < /app/schema/backend.sql
    ```
15. Start Backend
    ```
    [ root@ip-172-31-8-88 /app ]# systemctl start backend
    ```
16. Check N/W Statics
    ```
    [ root@ip-172-31-8-88 /app ]# netstat -lntp
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1254/sshd: /usr/sbi
    tcp6       0      0 :::22                   :::*                    LISTEN      1254/sshd: /usr/sbi
    tcp6       0      0 :::8080                 :::*                    LISTEN      15335/node
    ```
17. Login to Database 
    ```
    mysql -h 3.237.172.249 -uroot -pExpenseApp@1
    ```
    ```
    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | sys                |
    | transactions       |
    +--------------------+
    5 rows in set (0.00 sec)
    ```

### BE ..> DB Running or Not
1. Ping to DB
    ```
    [ root@ip-172-31-8-88 /app ]# ping -c 4 3.237.172.249
    PING 3.237.172.249 (3.237.172.249) 56(84) bytes of data.
    64 bytes from 3.237.172.249: icmp_seq=1 ttl=63 time=0.291 ms
    64 bytes from 3.237.172.249: icmp_seq=2 ttl=63 time=0.986 ms
    64 bytes from 3.237.172.249: icmp_seq=3 ttl=63 time=0.331 ms
    64 bytes from 3.237.172.249: icmp_seq=4 ttl=63 time=0.823 ms

    --- 3.237.172.249 ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3102ms
    rtt min/avg/max/mdev = 0.291/0.607/0.986/0.302 ms
    ```
2. Check able to connect to DB
    ```
    [ root@ip-172-31-8-88 /app ]# telnet 3.237.172.249 3306
    Trying 3.237.172.249...
    Connected to 3.237.172.249.
    Escape character is '^]'.
    J
    8.0.36g&sN1.A~▒1(U=?
    KtAcaching_sha2_passwordExpenseApp@1
    !#08S01Got packets out of orderConnection closed by foreign host.
    ```