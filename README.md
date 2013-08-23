Jacc - Just Another Cloud in the Cloud
======================================

Lighweight linux containers are used to build a private cloud. Booting a new container is easy
and is done in a few seconds. Create a tar archive named webapp.tar containing the code and a
Dockerfile in the root and then run `/path_to_jacc/jacc.js --cmd push --name=www.mydomain.com --port=80`.

Jacc manages routing to the right webapp using the name provided. Your DNS needs to point
to the Jacc server. For development and testing purposes can this be solved by updating the local
hosts file.

I've added this to the hosts file on my development laptop: 

```
127.0.0.1       app1.jacc.local
127.0.0.1       app2.jacc.local
127.0.0.1       app3.jacc.local
```

After the installation, test that everything works by deploying the webapp in tests folder:

```
cd tests
../jacc.js --cmd push --name=app1.jacc.local --port=8080
```

hipache and redis-dns needs to be running. For ubuntu, copy the init scripts in `etc/init` to
`/etc/init` and do `sudo service hipahce start` and `sudo service redis-dns start`.


Installation
------------

Pre-requisites:

 * Virtualbox to run locally
 * AWS account to launch a server in Amazons cloud 

The repo comes with a Vagrantfile. Installation of a test/development environment is done running
`vagrant up vb`. Then reboot the machine with `vagrant halt vb` followed by ``vagrant up vb`. 
Rebooting is needed since a kernel upgrade is performed.

It is also be possible to run `bootstrap.sh` followed by ``bootstrap2.sh` in any Ubuntu machine.

If you already have docker, then just do `sudo npm install --production -g`

Then goto the folder where npm installs the modules, on ubuntu it's in: `/usr/lib/node_modules/jacc/`

 * Copy jacc_config.json.template to jacc_config.json and edit the settings if necesary.
 * Copy hipache_config.json.template to hipache_config.json and edit the settings if necesary.
 * Goto node_modules/redis-dns and copy redis-dns.json.template redis-dns.json


## AWS EC2

You could just launch a 64-bit precise server, clone this repo and then run `bootstrap.sh`.

Using vagrant, do:

```
# Install AWS plusin
vagrant plugin install vagrant-aws
vagrant plugin list

# A dummy box is needed
vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box

# Set this variable to use AWS, it should bot be set to use a local Virtualbox instead
export VAGRANT_AWS='Yes'

# These environment variables need to be set, put in bashrc/bach_profile env 
# NOTE: Only the region us-east-1 seams to work at the moment.
export AWS_API_KEY=...
export AWS_API_SECRET=...
export AWS_PRIVATE_KEY_PATH=...
export AWS_KEYAIR_NAME=...
export AWS_REGION=...

vagrant up aws
```


Tips and tricks
--------------

Capture the current IP adress (for interface eth0): `IP_ADDR=$(ifconfig|grep -a1 eth0|grep inet|cut -c 21-29)`

Then define an alias: `alias docker='docker -H=tcp://$IP_ADDR'`

Now you can run: `docker ps` instead of `docker -H=tcp://xxx.x.x.x ps`




Troubleshooting
---------------

Q: hipache can't conntect to redis database

A: hipache is configured through a redis database. Make sure that redis is running (assuming 
it already is installed): `sudo service redis-server status`


Q: Installation has not been completed with `vagrant up vb`

A: When using vagrant, run the bootstrap.sh script manually after the machine has been created.
The kernel upgrade stops the execution of the script so the rest need`s to be executed once
more.


Q: Can't find the jacc folder after reboot

A: Clone the repo manually:
```
cd ~
git clone https://github.com/colmsjo/jacc.git
cd jacc && sudo npm install --production -g
```

