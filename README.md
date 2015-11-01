# SaltStackCheatSheet

## Installing SaltStack - Ubuntu 14.*

```
wget -O - https://repo.saltstack.com/apt/ubuntu/ubuntu14/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
echo 'deb http://repo.saltstack.com/apt/ubuntu/ubuntu14/latest trusty main' | sudo tee -a /etc/apt/sources.list
sudo apt-get update
# Master installation
apt-get install salt-master
# Minion installation
apt-get install salt-minion
# Salt ssh installation
apt-get install salt-ssh
# Salt syndic installation
apt-get install salt-syndic
```
