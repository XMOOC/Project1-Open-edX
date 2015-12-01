# Using Vagrant and Virtualbox

Following are the commands you need to use.
```
mkdir fullstack
cd fullstack
curl -L https://raw.githubusercontent.com/edx/configuration/master/vagrant/ release/fullstack/Vagrantfile > Vagrantfile
vagrant plugin install vagrant-hostsupdater
vagrant up
```

### Linux Commands
`curl` is linux command. If you're using Windows, you should use git bash.

### Administrator Mode
`vagrant up` must be done in Administrator mode.

### In AWS workspace
Some say you can not use Virtualbox in AWS workspace since AWS does not provide virtualization process yet.

However, there's `[Vagrant AWS Provider](https://github.com/mitchellh/vagrant-aws)`. This is a Vagrant 1.2+ plugin that adds an AWS provider to Vagrant, allowing Vagrant to control and provision machines in EC2 and VPC. The following command lets you use vagrant in Virtualbox.
```
vagrant plugin install vagrant-aws
```
It's quite complicated. I succeed in installing fullstack and devstack here, but failed to run those machines.

I could not turn on the machines using `vagrant up`.
