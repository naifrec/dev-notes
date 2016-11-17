# Install matplotlib in a virtualenv

To install matplotlib in a virtualenv and still be able to use Qt4 backend follow:

```bash
# Install qt4 backend
sudo apt install python-qt4
# assumes you are virtualenv is activated
pip install matplotlib
cd <virtualenv_path>/lib/python2.7/site-packages

ln -s /usr/lib/python2.7/dist-packages/PyQt4/ .
# Check in /usr/lib/python2.7/dist-packages/ how sip.so is called for you
ln -s /usr/lib/python2.7/dist-packages/sip.x86_64-linux-gnu.so .

```

Now this simple example in ipython should work:
```python
import matplotlib.pyplot as plt

plt.figure()
plt.show()
```
