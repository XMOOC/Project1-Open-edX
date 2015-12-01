# Using an Amazon Web Services pre-installed image, on a single server

[Installation Guide](https://github.com/XMOOC/Install/blob/master/edx%20Full%20stack%20installation%20using%20Amazon%20Machine%20Image.md)

After launching the server, ssh to it.
Then you need to update the codebase.
```
sudo /edx/bin/update configuration release
sudo /edx/bin/update edx-platform release
```
On `sudo /edx/bin/update edx-platform release` I get one following error.
```
failed: [localhost] => {"failed": true, "item": ""}
msg: Failed to download remote objects and refs

FATAL: all hosts have already failed -- aborting
```
I could not fix it yet..
