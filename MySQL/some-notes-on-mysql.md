# Some notes on MySQL

1. A temporary password is generated for root@localhost (i.e. _oZWQlrx,s3Xp_).

2. Check **System Preferences > MySQL** to start and stop MySQL Server and set whether or not it has to automatically start whenever Mac starts up.

3. OS X uses launch daemons to automatically start, stop, and manage processes and applications such as MySQL. Current values for some parameters as WorkingDirectory, RunAtLoad and others can be checked at MySQL daemon plist file which can be found at `/Library/LaunchDaemons/com.oracle.oss.mysql.mysqld.plist`.

4. All programs (**mysql**, **mysqld**, **mysqladmin**, **mysqldump**...) are located at `/usr/local/mysql/bin`.

5. **mysql** in `/usr/local` is actually a symbolic link to specific installed version (i.e. `mysql-5.7.19-macos10.12-x86_64`).

6. Some useful aliases:

```bash
alias mysql=/usr/local/mysql/bin/mysql
alias mysqladmin=/usr/local/mysql/bin/mysqladmin
```

7. To open a session:

```bash
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'oZWQlrx,s3Xp';
```
