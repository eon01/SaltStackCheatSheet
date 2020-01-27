# SaltStack Cheat Sheet

This code is a part from "[SaltStack For DevOps](http://saltstackfordevops.com)".

## Installing SaltStack - Ubuntu 12, 14, 16 and 18

Update your key base and sources lists from [this page](https://repo.saltstack.com/#ubuntu) according to your distribution.

Example: Ubuntu 18 - Python3

```
wget -O - https://repo.saltstack.com/py3/ubuntu/18.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -

echo 'http://repo.saltstack.com/py3/ubuntu/18.04/amd64/latest bionic main' | sudo tee -a /etc/apt/sources.list

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



## Multi Platform Installation

Salt Master:

```
curl -L https://bootstrap.saltstack.com -o install_salt.sh
sudo sh install_salt.sh -P -M
```

Salt Minion:

```
curl -L https://bootstrap.saltstack.com -o install_salt.sh
sudo sh install_salt.sh -P
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

# Delete salt key and register it
salt-key -d 'minion' && yes | salt-key -a 'minion'
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



## Clearing Cache

```
# Clearing cache on the minion by removing the cache directory
salt '*' cmd.run 'rm -rf /var/cache/salt/minion/files/base/*'

# Clearing cache the Salt way (using saltutil module)
salt '*' saltutil.clear_cache

# Synchronizes custom modules, states, beacons, grains, returners, output modules, renderers, and utils.
salt '*' saltutil.sync_all
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



## SaltStack Versions

```
# Salt components report
salt --versions-report
salt --versions

# Master version
salt-master --version

# Minion version
salt-minion --version
```





## SaltStack Modules And Functions

```
# list modules
salt '*' sys.list_modules

# list functions
salt '*' sys.list_functions

# synchronising modules on the minion
salt-call saltutil.sync_modules
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



## Upgrades & Versions of System Packages

```
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
salt-run http.query https://faun.dev text=true

# Get the header of a page
salt-run http.query https://faun.dev headers=true

# Get the response code from a web server
salt-run http.query https://faun.dev status=true

# Sending a post request
salt '*' http.query http://domain.com/ method=POST params='key1=val1&key2=val2'

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



## Scheduling Features

```
# Schedule a job called "scheduled_job"
salt '*' schedule.add scheduled_job function='cmd.run' job_args="['']" seconds=10

# Enable the job
salt '*' schedule.enable_job scheduled_job

# Disable the job
salt '*' schedule.disable_job scheduled_job

# List schedules
salt '*' schedule.list
```



## Working With SLS

```
# Show SLS
salt '*' state.show_sls

# listing states modules on the minion
salt-call sys.list_state_modules
```



## Testing States

```
# Test a highstate using the highstate module
salt '*' state.highstate test=True

# Test a highstate using the sls module
salt '*' state.sls test=True

# Test a single state
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



## Salted Github Repositories

- Django with SaltStack https://github.com/wunki/django-salted
- Salt GUI pad https://github.com/tinyclues/saltpad
- Openstack automation with SaltStack https://github.com/CSSCorp/openstack-automation
- A curated collection of working salt *states* and configurations for use in your saltstack setup. https://github.com/saltops/saltmine
- These are all of the configuration files needed to built a Wordpress development environment with Vagrant, Virtual Box and SaltStack https://github.com/paulehr/saltstack-wordpress
- Java bindings for the SaltStack API https://github.com/SUSE/saltstack-netapi-client-java
- Vim snippets for SaltStack *states* files https://github.com/StephenPCG/vim-snippets-salt
- Metrics for SaltStack https://github.com/pengyao/salt-metrics
- Salt GUI https://github.com/saltstack/halite
- cmdb saltstack https://github.com/voilet/cmdb
- GUI For SaltStack https://github.com/yueyongyue/saltshaker
- A collaborative curated list of awesome SaltStack resources, tutorials and other salted stuff. https://github.com/hbokh/awesome-saltstack
- SaltStack provisioner for test-kitchen https://github.com/saltstack/kitchen-salt
- Dashboard for saltstack https://github.com/halfss/salt-dashboard
- Recipe to deploy production Kubernetes cluster https://github.com/valentin2105/Kubernetes-Saltstack
- A gui and cli to manage saltstack deployments https://github.com/Lothiraldan/saltpad
- Docker Compose setup to spin up a salt master and minions for easy testing, learning, and prototyping  https://github.com/cyface/docker-saltstack



## Interesting Reads

- [SaltStack for Flexible and Scalable Configuration Management](https://www.infoq.com/articles/saltstack-configuration-management/)
- [Lyft Replaces Puppet with SaltStack](https://www.infoq.com/news/2014/08/lyft-moves-to-saltstack/)
- [SaltStack’s “Big” Role in Google Kubernetes and Why Immutable Infrastructure Makes The Cloud a Giant Computer](https://thenewstack.io/saltstacks-big-role-in-google-kubernetes-and-why-immutable-infrastructure-makes-the-cloud-a-giant-computer/)
- [Every Day Is Monday In Operations](https://engineering.linkedin.com/blog/2016/11/every-day-is-monday-in-operations)
- [SaltStack as an Alternative to Terraform for AWS Orchestration](https://eng.lyft.com/saltstack-as-an-alternative-to-terraform-for-aws-orchestration-cd2ceb06bf8c)
- [One week of Salt: frustrations and reflections](https://stevebennett.me/2014/02/17/one-week-of-salt-frustrations-and-reflections/)
- [Overcoming AWS Complexity with SaltStack patterns](https://eng.lyft.com/overcoming-aws-complexity-with-saltstack-patterns-1472981f43c6)

