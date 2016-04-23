# SaltStackCheatSheet

This code is a part from "SaltStack For DevOps" Book : https://leanpub.com/saltstackfordevops/

Official website: http://saltstackfordevops.com

[![SaltStack For DevOps](https://s3.amazonaws.com/titlepages.leanpub.com/saltstackfordevops/large?1446505556)](https://leanpub.com/saltstackfordevops/)

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

# Salt API installation
apt-get install salt-api
```

## Bootstrapping Salt Minion

```
curl -L https://bootstrap.saltstack.com -o install_salt.sh && sudo sh install_salt.sh
```

## Salt Key Management

``` 
# Listing Salt requests
salt-key -L

# Accepting all requests
salt-key -A

# Accepting a single request (from myNode)
salt-key -a myNode

# Removing the key of a Salt 'myNode' Minion
salt-key -d minion_id
```

## Debugging

```
# Debugging the master
salt-master -l debug

# Debugging the minion
salt-minion -l debug

# Restarting the minion without cache
stop master/minion
rm -rf /var/cache/salt
start master/minion
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

## SaltStack Modules And Functions

```
salt '*' sys.list_modules
salt '*' sys.list_functions
```

## Compound Matchers

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

## Upgrades & Versions

```
#
# Listing upgrades
salt '*' pkg.list_upgrades

# Upgrading
salt '*' pkg.upgrade

# List the packages currently installed as a dict
salt '*' pkg.list_pkgs versions_as_list=True

# Refresh the pkgutil repo database
salt '*' pkgutil.refresh_db

# Check the version of a package
salt '*' pkgutil.version mongodb
```

## Packages Manipulation

```
# Installation
salt '*' pkg.install apache2

# Latest version installation
salt '*' pkgutil.latest_version mysql-common

# Removing package(s)
salt '*' pkg.remove vim

# Purging package(s)
salt '*' pkg.purge apache2 mysql-server


```

## Reboot & Uptime

```
# Reboot
salt '*' system.reboot

#Uptime
salt '*' status.uptime
```

## Using Grains

```
# Syncing grains
salt '*' saltutil.sync_grains

# Available grains can be listed by using the ‘grains.ls’ module:
salt '*' grains.ls

# Grains data can be listed by using the ‘grains.items’ module:
salt '*' grains.items

# Grains have values that could be called via ‘grains.get <grain_name>’ (path is the name of a grain)
salt '*' grains.get path
```

## Syncing Data

```
# Syncing grains
salt '*' saltutil.sync_grains

# Syncing everything from grains to modules, outputters, renderers, returners, states and utils.
salt '*' saltutil.sync_all
```

## Running System Commands

```
salt "*" cmd.run "ls -lrth /data"
salt "*" cmd.run "df -kh /data"
salt "*" cmd.run "du -sh /data"
```

## Working With Services

```
# Apache example

# Checking if service is available
salt '*' service.available apache2

# Manipulating Apache2 service
salt '*' service.status apache2
salt '*' service.start apache2
salt '*' service.restart apache2
salt '*' service.stop apache2
```

## Network Management

```
# Get IP of your minion
salt '*' network.ip_addrs          

# Ping a host from your minion
salt '*' network.ping localhost   

# Traceroute a host from your minion
salt '*' network.traceroute localhost   

# Get hostname
salt '*' network.get_hostname      

# Modify hostname to 'myNode'
salt '*' network.mod_hostname myNode 

# Information on all of the running TCP connections
salt '*' network.active_tcp

# Return the arp table from the minion
salt '*' network.arp

# Test connectivity
salt '*' network.connect google-public-dns-a.google.com port=53 proto=udp timeout=3

# Get default route
salt '*' network.default_route

# Execute dig
salt '*' network.dig eon01.com

# Get the MAC addres of eth0 interface
salt '*' network.hw_addr eth0

# Get the inet addres of eth1 interface
salt '*' network.interface eth1

# Get the IP addres of tun interface 
salt '*' network.interface_ip tun
```

## Working With HTTP Requests

```
# Get the html source code of a page
salt-run http.query http://eon01.com text=true

# Get the header of a page
salt-run http.query http://eon01.com headers=true

# Get the response code from a web server
salt-run http.query http://eon01.com status=true

# Sending a post request
salt '*' http.query http://domain.com/ method=POST params='key1=val1&key2=val2'

#
```

## Job Management

```
# List active jobs
salt-run jobs.active

# List all jobs with the id and other information
salt-run jobs.list_jobs

# List multiple information about the job with the id:20151101225221651308 like the result output
salt-run jobs.lookup_jid 20151101225221651308

# Kill the job with the id:20151101225221651308
salt 'server' saltutil.kill_job 20151101225221651308
```

## Scheduling Feature

```
# Schedule a job called "scheduled_job"
salt '*' schedule.add scheduled_job function='cmd.run' job_args="['']" seconds=10

# Enable the job
salt '*' schedule.enable_job scheduled_job

# Disable the job
salt '*' schedule.disable_job scheduled_job
```

## Working With SLS

```
salt '*' state.show_sls
```

## Testing States

```
salt '*' state.highstate test=True
salt '*' state.sls test=True
salt '*' state.single test=True
```

## Load testing

```
# Starting 20 minions
wget https://raw.githubusercontent.com/saltstack/salt/develop/tests/minionswarm.py; python minionswarm.py -m 20 --master salt-master;
```

## State Declaration Structure

```
# Source: https://docs.saltstack.com/en/latest/ref/states/highstate.html#state-declaration

# Standard declaration
<ID Declaration>:
  <State Module>:
    - <Function>
    - <Function Arg>
    - <Function Arg>
    - <Function Arg>
    - <Name>: <name>
    - <Requisite Declaration>:
      - <Requisite Reference>
      - <Requisite Reference>


# Inline function and names
<ID Declaration>:
  <State Module>.<Function>:
    - <Function Arg>
    - <Function Arg>
    - <Function Arg>
    - <Names>:
      - <name>
      - <name>
      - <name>
    - <Requisite Declaration>:
      - <Requisite Reference>
      - <Requisite Reference>


# Multiple states for single id
<ID Declaration>:
  <State Module>:
    - <Function>
    - <Function Arg>
    - <Name>: <name>
    - <Requisite Declaration>:
      - <Requisite Reference>
  <State Module>:
    - <Function>
    - <Function Arg>
    - <Names>:
      - <name>
      - <name>
    - <Requisite Declaration>:
      - <Requisite Reference>
```

## SaltStack Github Repositories

- *Django* with SaltStack https://github.com/wunki/django-salted
- Salt GUI pad https://github.com/tinyclues/saltpad
- *Openstack* automation with SaltStack https://github.com/CSSCorp/openstack-automation
- A curated collection of working salt *states* and configurations for use in your saltstack setup. https://github.com/saltops/saltmine
- These are all of the configuration files needed to built a *Wordpress* development environment with *Vagrant*, *Virtual Box* and SaltStack https://github.com/paulehr/saltstack-wordpress
- *Java* bindings for the SaltStack API https://github.com/SUSE/saltstack-netapi-client-java
- *Vim* snippets for SaltStack *states* files https://github.com/StephenPCG/vim-snippets-salt
- Metrics for SaltStack https://github.com/pengyao/salt-metrics
- Salt GUI https://github.com/saltstack/halite

# Lisence and contribution
This part is lisenced under CC BY-SA 4.0.
Share — copy and redistribute the material in any medium or format
Adapt — remix, transform, and build upon the material for any purpose, even commercially.
The licensor cannot revoke these freedoms as long as you follow the license terms.

Feel free to contribute.
