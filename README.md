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

## SaltStack Documentation

```
# Viewing all the documentation
salt '*' sys.doc

# Viewing a module documentation
salt '*' sys.doc module_name

#Examples:
salt '*' sys.doc status
salt '*' sys.doc pkg     
salt '*' sys.doc network 
salt '*' sys.doc system
salt '*' sys.doc cloud

# Viewing a function documentation
salt '*' sys.doc module_name function_name

# Examples:
salt '*' sys.doc  auth django
salt '*' sys.doc sdb sqlite3
```

# SaltStack Modules And Functions:

```
salt '*' sys.list_modules
salt '*' sys.list_functions
```

# Compound matchers:

| Letter | Match Type | Example | Alt Delimiter?] |
| --- | --- | --- | --- |
| G | Grains glob | G@os:Ubuntu | Yes |
| E | PCRE Minion ID | E@web\d+\.(dev\|qa\|prod)\.loc | No |
| P | Grains PCRE | P@os:(RedHat\|Fedora\|CentOS) | Yes |
| L | List of minions | L@minion1.example.com,minion3.domain.com or bl*.domain.com | No |
| I | Pillar glob | I@pdata:foobar | Yes |
| J | Pillar PCRE | J@pdata:^(foo\|bar)$ | Yes |
| S | Subnet/IP address | S@192.168.1.0/24 or S@192.168.1.100 | No |
| R | Range cluster | R@%foo.bar | No |

Other examples: 

```
# Examples taken from: https://docs.saltstack.com/en/latest/topics/targeting/compound.html

# Joining
salt -C 'webserv* and G@os:Debian or E@web-dc1-srv.*' test.ping
salt -C '( ms-1 or G@id:ms-3 ) and G@id:ms-3' test.ping

# Excluding
salt -C 'not web-dc1-srv' test.ping
```
