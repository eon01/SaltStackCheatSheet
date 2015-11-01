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

 ----------------------------------------------------------------------------------------------------------
  Letter                               Match Type Example                           [<span>Alt
                                                                                    Delimiter?</span>]
  ------------------------------------ ---------- --------------------------------- ------------------------
  G                                    Grains     `G@os:Ubuntu`{.docutils .literal} Yes
                                       glob                                         

  E                                    PCRE       `E@web\d+\.(dev|qa|prod)\.loc`{.d No
                                       Minion ID  ocutils                           
                                                  .literal}                         

  P                                    Grains     `P@os:(RedHat|Fedora|CentOS)`{.do Yes
                                       PCRE       cutils                            
                                                  .literal}                         

  L                                    List of    `L@minion1.example.com,minion3.do No
                                       minions    main.com or bl*.domain.com`{.docu 
                                                  tils                              
                                                  .literal}                         

  I                                    Pillar     `I@pdata:foobar`{.docutils        Yes
                                       glob       .literal}                         

  J                                    Pillar     `J@pdata:^(foo|bar)$`{.docutils   Yes
                                       PCRE       .literal}                         

  S                                    Subnet/IP  `S@192.168.1.0/24`{.docutils      No
                                       address    .literal} or                      
                                                  `S@192.168.1.100`{.docutils       
                                                  .literal}                         

  R                                    Range      `R@%foo.bar`{.docutils .literal}  No
                                       cluster                                      
  ----------------------------------------------------------------------------------------------------------

  [<span>Alt Delimiter?</span>]: #target-alt-delimiters

Other examples: 

```
# Examples taken from: https://docs.saltstack.com/en/latest/topics/targeting/compound.html

# Joining
salt -C 'webserv* and G@os:Debian or E@web-dc1-srv.*' test.ping
salt -C '( ms-1 or G@id:ms-3 ) and G@id:ms-3' test.ping

# Excluding
salt -C 'not web-dc1-srv' test.ping
```
