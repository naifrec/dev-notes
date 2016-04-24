## 1. Install ember

You need to install both nodejs and npm. This can be done by doing this:
```
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_5.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -sL https://deb.nodesource.com/setup_5.x | bash -
apt-get install -y nodejs
```
Updated version of this commands may be found [here]('https://github.com/nodesource/distributions#installation-instructions').

This will have installed npm and node correctly. One thing to remember now is
that when you want to install a package through npm, **never use sudo** and
**the option -g instead**.

In case you already had installed stuff as sudo through `npm`, uninstall them all
following this [stackoverflow post]('http://stackoverflow.com/questions/16151018/npm-throws-error-without-sudo').
```
sudo npm list -g --depth=0. | awk -F ' ' '{print $2}' | awk -F '@' '{print $1}' | sudo xargs npm remove -g
```

You will also need to remove the cache from Bower to use ember properly, using:
```
sudo rm -rf ~/.cache/bower
```

You can then safely run:
```
npm install ember-cli@2.5 -g
```
