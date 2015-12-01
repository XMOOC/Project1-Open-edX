# edx Full stack installation using Vagrant Virtualbox

- Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads) >= 4.3.12
- Install [Vagrant](https://github.com/edx/configuration/wiki/Installing-Vagrant) >= 1.5.3
- Download the Vagrantfile and create the Vagrant instance:

```
mkdir fullstack
cd fullstack
curl -L https://raw.githubusercontent.com/edx/configuration/master/vagrant/release/fullstack/Vagrantfile > Vagrantfile
vagrant plugin install vagrant-hostsupdater
vagrant up
```
- Note: The first time you create the virtual machine (VM) instance, Vagrant will download the base box, which is about 2GB. If you destroy and recreate the VM, Vagrant will re-use the box it downloaded.

Default username/password: vagrant/vagrant

Once the VM is running, you can access the LMS and Studio at these URLS:

- LMS: http://192.168.33.10/
- Studio: http://192.168.33.10:18010/

The latest version of fullstack has the demo course pre-loaded and dummy accounts, you can log in to the website as:

- staff@example.com / edx
- verified@example.com / edx
- audit@example.com / edx
- honor@example.com / edx

See [edX-Managing-the-Full-Stack](https://github.com/edx/configuration/wiki/edX-Managing-the-Full-Stack) for how to manage and update the server once it is running
