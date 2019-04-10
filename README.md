# Python Tutorial

Disclaimer: I have simplified and overlooked details in this guide.

This tutorial assumes you have read the [python101 slides](https://docs.google.com/presentation/d/1WdBEU1BSDfEKHkLxY2IksiaAqXJBSKPUc_0Q4UJAtIY/edit):

* Python 3 is properly installed
* You don't have other tools interferring with your setup

Limitations of the tutorial:

* Because on how python is setup, you will be limited to running one python 2 version and one python 3 version.

  * After you have done the tutorial, if you need to have several python 3 versions running, I recommend you have a look on [Pyenv](https://github.com/pyenv/pyenv)

## FAQ: Why would I want to read this?

This guide will explain you how python works as an execution environments, where its files sit, and what are good ways of working in python.

The idea is that this knowledge will allow you to be confident that you won't be breaking your computer, and that you will understand how the different commands you run affect your installation.

## Understanding the python path

`import sys` will trigger a lookup in the so called Python Path, looking for a package or module with that name to import.

Our ability to import modules greatly depends on this feature. The Python path is similar to the shell PATH variable.

In all the systems, the PATH variable marks where to look for binaries. Therefore, running `base64`, will look on all those places, and the first match will be the one executed.

In the case of python, same thing happens with modules. Doing `import sys` will trigger the same behaviour of traversing accross the different paths looking for the `sys` package or module.

## Understanding the global environment

If you have the default python installation, it should be clean from any kind of external packages. The only packages I recommend to put in the global namespace are the ones to create virtual environments. More specifically, I use `pipenv`, therefore the following output would be in the global environment because I ran `pip3 install pipenv`

```text
root@6c4b4dd258dd:/$ pip freeze
certifi==2019.3.9
pipenv==2018.11.26
virtualenv==16.4.3
virtualenv-clone==0.5.2
```

By default, `python` will use the global environment, which is installed along python. When speaking about environment, I refer to the `site-packages` folder.

Python installation can be divided in 2:

* Packages that are part of the core of python (such as `os`, `sys`, `time`)
* Packages that are external to the python language project (such as `pipenv`, `virtualenv`, `numpy`)

When choosing installations, the core of python will be setup in the python version folder, where as the other packages will be setup in the `site-packages` folder.

Example output of the environment:

```text
root@6c4b4dd258dd:/# python -c 'import sys;print("\n".join(sys.path))'

/usr/local/lib/python37.zip
/usr/local/lib/python3.7
/usr/local/lib/python3.7/lib-dynload
/usr/local/lib/python3.7/site-packages
```

As you can see in the printed paths, there are quite a few folders and files there. Let's have a look on them.

Although I am going to explain you what the four of them contain, I have sorted them so that you can skip 2 of them.

### Script directory or current directory

It may be difficult to realise, but that empty line at the beginning of the python paths means current directory. Python will add the location of the python script you execute to the python path automatically. This is quite confusing when using the path of an script inside a package.

Have a look at [https://github.com/txomon/python-paths](https://github.com/txomon/python-paths) for some examples

### Core python installation

In this case `/usr/local/lib/python3.7` is the place where all python is installed:

```
root@6c4b4dd258dd:/# ls /usr/local/lib/python3.7/ | tail
datetime.py
...
os.py
...
sys.py
```

As you can see, the typical first level import modules are present there.

### Site packages

Site packages is the name of the folder where we store all python packages and modules that are not developed and distributed by the python organization.

In this case `/usr/local/lib/python3.7/site-packages` is the system site-packages folder.

As you can see, all the packages that were installed for pipenv are available here:

```text
root@6c4b4dd258dd:/# ls /usr/local/lib/python3.7/site-packages/
README.txt                   pkg_resources
__pycache__                  setuptools
certifi                      setuptools-40.8.0.dist-info
certifi-2019.3.9.dist-info   virtualenv-16.4.3.dist-info
clonevirtualenv.py           virtualenv.py
easy_install.py              virtualenv_clone-0.5.2.dist-info
pip                          virtualenv_support
pip-19.0.3.dist-info         wheel
pipenv                       wheel-0.33.1.dist-info
pipenv-2018.11.26.dist-info
```

Later we will probably dive-in into what are all those files there that don't look ly python modules. But for now let's just keep in mind that these files e

### (Advanced) Compiled modules

When we speak about compiled modules in python, we usally can refer to python byte-code or to dynamic libraries. In this specific case, we are referring to the later.

One of the ways to improve the performance of your Python programs is through the implementation in a low level language of the parts of your routines that require performance. One of the most common ones are C and C++.

Python has an API to create your own python modules coded in C, so that you can import those modules from normal python scripts, however running in the low level implementation you coded.

```text
root@6c4b4dd258dd:/# ls /usr/local/lib/python3.7/lib-dynload/ | head
_asyncio.cpython-37m-x86_64-linux-gnu.so
_bisect.cpython-37m-x86_64-linux-gnu.so
_blake2.cpython-37m-x86_64-linux-gnu.so
_bz2.cpython-37m-x86_64-linux-gnu.so
_codecs_cn.cpython-37m-x86_64-linux-gnu.so
_codecs_hk.cpython-37m-x86_64-linux-gnu.so
_codecs_iso2022.cpython-37m-x86_64-linux-gnu.so
_codecs_jp.cpython-37m-x86_64-linux-gnu.so
_codecs_kr.cpython-37m-x86_64-linux-gnu.so
_codecs_tw.cpython-37m-x86_64-linux-gnu.so
```

Here, you can see in the list asyncio module's low level implementation.

### (Advanced) Zip python core installation

Python supports distributing the sourcecode in zipfiles. This is something that usually leads to easier distribution, but it's really easy to get wrong.

In general there are few occasions where you want to do so, so I will not cover them, but feel free to read about it at$$ [PEP-273](https://www.python.org/dev/peps/pep-0273/)

## Understanding a virtual environment

We have explained how python looks up packages and modules, and we have seen how the global python installation is organized.

This may not be an obvious thing, but in software development, dependencies are many times a big headache. There are many ways in which the different languages sort out and use dependencies. In python, we try to separate each application to run with its own environment. These is achieved making use of the python path and virtual environments.

A virtual environment to put it simple is just a new location that is inserted earlier in the path than the global system installation discussed earlier.

### Installing pipenv

Although I have said it implicitly, from this part on we will need to have pipenv setup. To do so, with no virtualenv activated, run with admin priviledges `pip install pipenv`.

If using linux, you probably have it packaged as `python-pipenv` or something similar. In OSX, I would discourage installing python packages through brew, it works somewhat weirdly, and we just want one package in the global `site-packages` + a binary in the bin folder.

### Creating a virtual environment with pipenv

The main creation tool to create just virtual environments is `venv` or `virtualenv`. However, in our case we want to have a few commodities over those two tools.

Traditionally, on top of those two, we can find `virtualenv-wrapper`, which takes care of the location. In our case, we are going to use `pipenv` because besides the aforementioned functionalities, it give a few nice extras when managing dependencies.

At the beginning of the guide I showed how I only have globally installed a few packages. These are all `pipenv` and its dependencies. I do not recommend you install pipenv from brew.

Additionally, this guide will only use `pipenv`, not show how to use it. The full documentation is available in [the official documentation page](https://pipenv.readthedocs.io/en/latest/)

We will create virtual environments using `pipenv shell`, which will launch a new shell with the environment activated.

```text
root@c88f7f192fde:~# mkdir test-project
root@c88f7f192fde:~# cd test-project/
root@c88f7f192fde:~/test-project# pipenv shell
Creating a virtualenv for this project…
Pipfile: /root/test-project/Pipfile
Using /usr/local/bin/python (3.7.3) to create virtualenv…
⠧ Creating virtual environment...Already using interpreter /usr/local/bin/python
Using base prefix '/usr/local'
New python executable in /root/.local/share/virtualenvs/test-project-Ph4xz48u/bin/python
Installing setuptools, pip, wheel...
done.

✔ Successfully created virtual environment!
Virtualenv location: /root/.local/share/virtualenvs/test-project-Ph4xz48u
Creating a Pipfile for this project…
Launching subshell in virtual environment…
in/activate192fde:~/test-project#  . /root/.local/share/virtualenvs/test-project-Ph4xz48u/bi
(test-project) root@c88f7f192fde:~/test-project#
```

### Virtualenv paths

You can know you have a virtual environment activated because there are those parenthesis at the beginning of the shell line `(test-project)`.

Also, if we run the same command as before, we will see that the python path has been modified

```text
(test-project) root@c88f7f192fde:~/test-project# python -c 'import sys;print("\n".join(sys.path))'

/root/.local/share/virtualenvs/test-project-Ph4xz48u/lib/python37.zip
/root/.local/share/virtualenvs/test-project-Ph4xz48u/lib/python3.7
/root/.local/share/virtualenvs/test-project-Ph4xz48u/lib/python3.7/lib-dynload
/usr/local/lib/python3.7
/root/.local/share/virtualenvs/test-project-Ph4xz48u/lib/python3.7/site-packages
```

As before, we have the current directory, and you can see that besides the global python installation, the global site-packages directory has dissappeared, and has been replaced by the one inside the virtualenv.

This way, you can have several virtual environments, with different packages and versions, where each virtual environment is installing the packages in its own `site-packages` directory.

Feel free to install new packages using pipenv, and check how the `site-packages` directory gets populated with new packages and modules.

## About directory structures

Before starting off with a new python project, let's go quickly through the different directories that you will be managing.

When creating a new python project,
