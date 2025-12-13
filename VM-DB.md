## DataBase ..> MySQL

### Setup DB:

1. Install DB

    ```
    dnf install mysql-server -y
    ```
2. Start DB
    ```
    systemctl start mysqld
    ```
3. Enable DB
    ```
    systemctl enable mysqld
    ```
4. We need to change the default root password in order to start using the database service
   ```
   mysql_secure_installation --set-root-pass ExpenseApp@1
   ```

### Verify Installation

1. netstat -lntp

    ```
    [ root@ip-172-31-0-141 ~ ]# netstat -lntp
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1253/sshd: /usr/sbi
    tcp6       0      0 :::33060                :::*                    LISTEN      14013/mysqld
    tcp6       0      0 :::22                   :::*                    LISTEN      1253/sshd: /usr/sbi
    tcp6       0      0 :::3306                 :::*                    LISTEN      14013/mysqld
    ```
2. ps -aux | grep mysql 
    ```
    [ root@ip-172-31-0-141 ~ ]# ps -aux | grep mysql
    mysql      14013  1.4 54.0 1776704 392680 ?      Ssl  06:00   0:00 /usr/libexec/mysqld --basedir=/usr
    root       14117  0.0  0.3   3932  2200 pts/0    S+   06:01   0:00 grep --color=auto mysql
    ```
3. Connect to DB ..> `mysql -h <host-address> -u root -p<password>`
    ```
    mysql -h 3.237.172.249 -u root -pExpenseApp@1
    ```
    But if your client and server both are in a single server, you can simply issue
    ```
    mysql
    ```
4. Check databases
    ```
    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | sys                |
    +--------------------+
    4 rows in set (0.00 sec)    
    ```