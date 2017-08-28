Use homebrew python3.6 as default
---------------------------------

First remove Python 2.7 framework following this [stack overflow answer](https://stackoverflow.com/a/3819829)

```sh
sudo rm -rf /Library/Frameworks/Python.framework/Versions/2.7
sudo rm -rf "/Applications/Python 2.7"
cd /usr/local/bin/
ls -l /usr/local/bin | grep '../Library/Frameworks/Python.framework/Versions/2.7' | awk '{print $9}' | tr -d @ | xargs rm
```

Then install python3.6:

```sh
brew install python3
```

Make sure `/usr/local/bin` appears on top of `/usr/bin` in `/etc/paths`,
then create a symlink to python3 using the command `ln -s /usr/local/bin/python3.6 /usr/local/bin/python`.
Do not forget to do the same trick for `pip` to default to `pip3`.
