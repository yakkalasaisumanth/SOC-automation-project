## Configuring The Wazuh & The Hive.

  1. Configuring the hive.

  - open the virtual machine were you installed the hive and open a terminal with root privileges.

  - now we are going to make changes to the configuration files.

  - but before doing that lets type ```ifconfig```in the terminal and note down the ip address of the machine.

  - now in terminal type
  ```
  nano /etc/cassandra/cassandra.yaml
  ```

- ![cassandra config](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/34.png)

  - now change the listening address by pressing ctrl+w which will open a search field now type listen and hit enter.

  - ![listen ip](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/35.png)

  - scroll down to the default host and change the localhost to the machine ip address which we have seen when running the ifconfig command like 192.168.1.1

  - now change the rpc address from localhost to the machine ip.

  -![rpc ip](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/36.png)

  - by pressing the ctrl+w and search rpc and scroll down to rpc client and change the address.

  - now press ctrl+w and search seed_provider and scroll down and change the seeds ip with the machine ip.

  - ![seed ip](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/37.png)

  - now press ctrl+x and press 'y' and hit enter.

  - now type the following.
  ```
  systemctl stop cassandra.service
  ```
  and then
  ```
  rm -rf /var/bin/cassandra/*
  ```
  now
  ```
  systemctl start cassandra.service
  systemctl enable cassandra.service
  ```
  and lastly
  ```
  systemctl status cassandra.service
  ```
  - ![some commands](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/38.png)

  - now you should see cassandra **active(running)** hit q to exit

  - now lets change the elastic search config.
  ```
  nano /etc/elasticsearch/elasticsearch.yml
  ```

   - ![elastic conf](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/39.png)


   - now we need to change the un comment the cluster name and change it to Test cluster.

   - ![elastic conf1](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/40.png)


   - now uncomment the node name and keep it as node-1

   - ![elastic cnonf2](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/41.png)

   - now uncomment the network host and put the machine ip 

   - now uncomment the default the port

   - now uncomment the cluster.initial_master_nodes:["node 1"] and also set it as it.

   - now save the file.

   - now follow the command
   ```
   systemctl start elasticsearch.service
   systemctl enable ealsticsearch.service
   ```

   - ![status running](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/42.png)

   - now lets check the status
   ```
   systemctl status elasticsearch.service
   ```

   - now you can see the service active and running and press the 'q' to quit

   - now lets also check the cassandra
   ```
   systemctl status cassandra.service
   ```

   - now we have to do the following
   ```
   ls -la /opt/thp
   ```

   - this shows that root has access to that directory but we need to change that
   ```
   chown -R thehive:thehive /opt/thp
   ```

   - ![thehive per](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/43.png)

   - now lets configure the hive file.
   ```
   nano /etc/thehive/application.conf
   ```

   - now scroll down little bit we notice the data base configuration.

   - change the host name to the machine ip

   - now give the cluster name as Test cluster

   - now scroll down and you can see hoist name as 127 remove that give the machine ip.

   - now scroll down we can see application base url remove the 127 ip and change that with the machine ip

   - now all the configurations are made please save and exit the file.

   - now follow the commands
   ```
   systemctl start thehive
   systemctl enable thehive
   ```

   - now lets check all the services
   ```
   systemctl status cassandra.service
   ssytemctl status elasticsearch.service
   systemctl status thehive
   ```

   - ![status run](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/44.png)

   - please check each of the services they should be active(running)

   - now open the web browser and in the url give your machine ip:9200

   - now you can see the hive login page

   - ![thehive login](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/45.png)

   - default credentials
   ```
   username:admin@thehive.local
   password:secret
   ```

   - ![thehive loginsucess](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/46.png)
   
   2. configuring the Wazuh

   - now open the wazuh vm and go to browser type the wazuh machineip

   - now you see the wazuh login page

   - now login with the username and password provided by the wazuh at the installation time.

   - now if you forgot it you can see it by the following command
   ```
   cd wazuh-install-files
   cat wazuh-passwords.txt
   ```
   - now you can see the all the passwords now find the root or admin password which is the login credential.

   - now setting up a agent

   - click on add agent select windows and scroll down and server address as wazuh public ip.

   - now just copy the wazuh agent command.

   - now open the windows 10 vm and open the power shell with admin privileges and pate the command.

   - now start services by 
   ```
   net start wazuhsvc
   ```

   - now go to the wazuh dash board

   - please wait for two minutes to show changes

   - now your agent is successfully set up.