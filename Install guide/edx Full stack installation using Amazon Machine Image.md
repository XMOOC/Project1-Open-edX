 # Single AWS server installation using Amazon Machine Image

- The public AMIs for the Kifli configuration release are available in the following regions:
    - us-east-1: ami-1af35972
    - us-west-1: ami-ad161ee8
    - eu-west-1: ami-aa76d0dd
    - ap-southeast-1: ami-3486a266
- We recommend selecting an t2.medium or larger instance.

- After launching the server, ssh to it (user: ubuntu)

 `chmod 400 {path-to-keypair}`

 `ssh -i {path-to-keypair} ubuntu@{public-ip}`

- And update the codebase by issuing a `sudo /edx/bin/update configuration release` and then a `sudo /edx/bin/update edx-platform release`. if you get 'unable to resolve host {whateverip}' error, in /etc/hosts file, add

 `127.0.1.1 {whateverip}`

- If you see an error in the "edx-platform release" command that reads: "Failed to download remote objects and refs" do the following and rerun: (Source: [forum](https://groups.google.com/forum/#!topic/edx-code/Hthq-KBc8K4))

 `cd /edx/app/edxapp/edx-platform`

 `sudo -u edxapp git remote prune origin`

- If you see an error in the "edx-platform release" command that reads: "Using cached dm.xmlsec.binding-1.3.2.tar.gz Error: cannot get XMLSec1 pre-processor and compiler flags; do you have the `libxmlsec1` development package installed?" [possibly related to M2Crypto disappearing from github?] do the following and rerun:

 `sudo apt-get install libxmlsec1-dev`

 `sudo apt-get install swig`

- Once the update is complete, you can connect to the LMS on __port 80__ and Studio on __port 18010__

- If you see a 502 error, try running the mongo ansible role again (see [forum](https://groups.google.com/forum/#!topic/edx-code/ICe6MRFeiXg))

 `cd /edx/app/edx_ansible/edx_ansible/playbooks && sudo /edx/app/edx_ansible/venvs/edx_ansible/bin/ansible-playbook -i localhost, -c local run_role.yml -e 'role=mongo' -e 'mongo_create_users=True'`

- Basic auth for the site is username: edx password: edx
- The default login is user: staff@example.com password: edx

If you see a 500 error with a message like so "THERE HAS BEEN A 500 ERROR ON THE YOUR PLATFORM NAME HERE SERVERS", do the following [forum](https://groups.google.com/forum/#!topic/openedx-ops/u3Z7ybwDxaY)

migrate lms: `cd /edx/app/edxapp/edx-platform && sudo -u www-data /edx/bin/python.edxapp manage.py lms syncdb --migrate --settings aws`

migrate cms: `cd /edx/app/edxapp/edx-platform && sudo -u www-data /edx/bin/python.edxapp manage.py cms syncdb --migrate --settings aws`

_Once the server is launched see [edX-Managing-the-Full-Stack](https://github.com/edx/configuration/wiki/edX-Managing-the-Full-Stack) for how to manage and update the server once it is running_

_If you want to adjust the look of your edX instance, follow the steps here: [Apply Stanford Theming](https://github.com/edx/configuration/wiki/Launching-the-Stanford-Online-theme)_

- note: you will have to set the server's hostname before anyone can start to signup (someone should advise), otherwise, they will get "localhost" inside their confirmation emails.
