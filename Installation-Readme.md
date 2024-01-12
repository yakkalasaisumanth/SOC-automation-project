## Installing the wazuh and the hive.

1. Installing Wazuh.

  - now open any one of two Ubuntu machines and login.

  - make sure you have updated and upgraded the machine.

  - if not please input the following command
  ```
  sudo apt update && upgrade -y
  ```

  - this will update and upgrade the vm.

  - install wazuh 4.7
  ```
  sudo curl -sO HTTPS://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
  ```

  - just copy the above command and paste it in terminal it will install the Wazuh.


  - after installing the Wazuh it throws a username and password on the screen make sure to copy them which we will use later to login.

  - Extract Wazuh Credentials

  - sudo tar -xvf wazuh-install-files.tar

  - if in case you forgot to store the username and password this will solve that problem.

  - That's it for the wazuh.


  2. Installing The Hive.

  - open the another Ubuntu instance vm and open the terminal.

  - please copy and paste the commands as it is in the terminal.

  - make sure to change into root because if you try to install in the sudo user some issues might occur it is recommended to install the packages as root.

  - to changing into the root press the following command
  ```
  sudo su
  ```

  - now type the user password and now you are root.

  - installing the dependencies.
  ```
  apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl  software-properties-common python3-pip lsb-release
  ```

  - installing java
 ```
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```

- ![installing java](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/33.png)

  - installing cassandra
  ```
wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra
```

  - installing elastic search
  ```
 wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

  - installing the hive
  ```
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
sudo apt-get update
sudo apt-get install -y thehive
```
  - Default Credentials on port 9000

  - credentials are 'admin@thehive.local' with a password of 'secret'

  - now we successfully installed the hive.