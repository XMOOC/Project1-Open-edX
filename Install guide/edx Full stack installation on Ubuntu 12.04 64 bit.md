# edX Ubuntu 12.04 64 bit Installation

### Hardware requirements

The following server requirements will be fine for supporting hundreds of registered students on a single server.

_Note: This will runs mysql, memcache, mongo, and all of the edX services (lms, studio, forums, ora/discern) on a single server. In production configurations we recommend that these services run on different servers and that a load balancer is added for redundancy. Setting up production configurations is beyond the scope of this wiki page._

- __Ubuntu 12.04 amd64__ (oraclejdk required)
- __Minimum 2GB of memory, 4GB recommended for production servers__ (with only 2GB some swap space is required, at least during installation)
- __At least one 2.00GHz CPU or EC2 compute unit__
- __Minimum 25GB of free disk, 50GB recommended for production servers__

For hosting in Amazon we recommend an m3.medium server, see https://aws.amazon.com/ec2/pricing

Community Ubuntu AMIs have 8GB on the root directory, make sure to expand it before installing.

### Installation instructions

___WARNING___:

- These instructions will potentially __destroy the server__ they are run on, you should only do them on a freshly installed virtual machine. But if you still want to have a try to re-install the Open edX stack on the same server, please see [this article](https://github.com/edx/configuration/wiki/Re-install-Open-edX-in-Ubuntu-12.04) for some issues you may face and how to fix them.

- __By default ssh will only allow key based authentication.__ Please setup key based SSH logins or modify the configuration repo to allow for password based SSH logins before running ansible.

___NOTES___:

- If you are running your services behind a proxy, please see [here](https://github.com/edx/configuration/wiki/edX-proxy-instructions)

___LET'S START___:

- Launch an Ubuntu 12.04 64-bit server and login to it as a user that has full sudo privileges. If you use EC2, use at least m1.large type with EBS and allocate ~50 GB to the root user.

- Update your Ubuntu package sources
```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo reboot
```
- One step installation

Please read the contents of the script before running this to ensure you are aware of everything it will do; it is quite extensive. The script requires that the running user can run commands as root via sudo.
```
wget https://raw.githubusercontent.com/edx/configuration/master/util/install/sandbox.sh -O - | bash
```
If you want to install an [Open edX release](https://openedx.atlassian.net/wiki/display/DOC/Open+edX+Releases), such as Cypress, just set the `OPENEDX_RELEASE` variable before running the script. For example:
```
export OPENEDX_RELEASE=named-release/cypress
wget https://raw.githubusercontent.com/edx/configuration/$OPENEDX_RELEASE/util/install/sandbox.sh -O - | bash
```
- Manual installation (does what the script does)

Perform the steps below
```
sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev libfreetype6-dev python-pip python-apt python-dev libxmlsec1-dev swig
sudo pip install --upgrade pip
sudo pip install --upgrade virtualenv
```
On the new server, clone the configuration repo:
```
cd /var/tmp
git clone https://github.com/edx/configuration
```
To allow password based SSH authentication, edit the common role inside of `configuration/playbooks/roles/common/defaults/main.yml` and set `COMMON_SSH_PASSWORD_AUTH` to `"yes"`

Install the ansible requirements
```
cd /var/tmp/configuration
sudo pip install -r requirements.txt
```
Run the edx_sandbox.yml playbook in the configuration/playbooks directory
```
cd /var/tmp/configuration/playbooks && sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"
```
__Note: If you are running on an older version of the configuration repo, use the variable 'edx_platform_commit' instead.__

- Note: If you get npm Error: failed to fetch from registry: coffee-script, put
```
registry = "http://registry.npmjs.org/"
```
to _/usr/share/npm/npmrc_ and retry the installation.

- If ansible stops at some point, look at log files. See locations of log files in the FAQ page.

After installation connect your web browser to the following ports:

- LMS - 80
- Studio - 18010

### Bad Suggestions (Arbitrary Upgrades)

Some parts of Open edX are outdated. If you see a message suggesting that you update something manually, __don't do it__ -- Open edX is probably relying on the outdated software remaining at that older version. Specifically:

- Ubuntu may alert you that a newer version of Ubuntu available when you SSH in to your server, and may suggest that you run `do-release-upgrade` to upgrade to that newer version. __Don't do it__.
- Pip may alert you that there is a newer version of pip available, and may suggest that you run `pip install --upgrade pip` to install it. __Don't do it__.

If you arbitrarily upgrade software in Open edX, _things will break_. Instead, you should submit a pull request to change the line in the Open edX project where that specific version of the software is defined. All pull requests need to be reviewed before they can be merged, and part of the review process will consist of testing Open edX with the updated software, identifying any breakages, and fixing them as part of the pull request.

### Overriding default web ports

You may want to override the default ports for Studio and lms-preview if you are setting up subdomains to connect to your sandbox. By default nginx will forward studio.* to the studio gunicorn process.

Example where preview.example.com, example.com and studio.example.com all point to the same server and you want to deploy the master branch of the edx-platform repo.

```
cd /var/tmp/configuration/playbooks/edx-east

sudo ansible-playbook -c local --limit "localhost:127.0.0.1" ../edx_sandbox.yml \
-i "localhost," -e 'EDXAPP_PREVIEW_LMS_BASE=preview.example.com  EDXAPP_LMS_BASE=example.com  EDXAPP_LMS_PREVIEW_NGINX_PORT=80 EDXAPP_CMS_NGINX_PORT=80 EDXAPP_LMS_NGINX_PORT=80 edx_platform_version=master '
```

See [edX-Managing-the-Full-Stack](https://github.com/edx/configuration/wiki/edX-Managing-the-Full-Stack) for how to manage and update the server once it is running
