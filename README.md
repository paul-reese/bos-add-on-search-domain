# bosh-add-on-search-domain

## install bosh-lite

Bring bosh-lite VM up with Vagrant:
```
$ vagrant init cloudfoundry/bosh-lite

$ vagrant up --provider=virtualbox

Bringing machine 'default' up with 'virtualbox' provider...
```

## install bosh cli
BOSH CLI is a command line interface to the _Director_. It is distributed as a Ruby gem.
```
$ gem install bosh_cli
```

Let CLI know about bosh-lite Director (use admin/admin to log in):
```
$ bosh target 192.168.50.4

Target set to 192.168.50.4
```

## bosh manifest
Let's build a bosh manifest, that we will use as runtime config and introduce as an add-on
 
