## ssh configuration

---

## server part

1. install openssh-server

   ```shell
   sudo apt-get install openssh-server
   ```

   

2. print SSH server status

   ```shell
   sudo systemctl status ssh
   ```

3. open SSH port

   ```shell
   sudo ufw allow ssh
   ```

   

## client part

