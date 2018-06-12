+++
title = "Cleaning your Python dev environment"
date = "2018-05-13"
meta = "true"
tags = [
    "python",
    "homebrew",
    "environment",
    "setup",
]
categories = [
    "Tutorials",
]
+++

Is your development environment getting too cluttered? Time for some spring cleaning!

## First step: Uninstall everything

Uninstall packages depending on Homebrew's Python. For example:

```bash
$ brew uninstall --force httpie
$ brew uninstall --force pyenv-virtualenv
$ brew uninstall --force pyenv-virtualenvwrapper
$ brew uninstall --force pyenv
$ brew uninstall --force pipenv
$ brew uninstall --force mongodb
```

Uninstall all Python versions installed via Homebrew:

```bash
$ brew uninstall --force python2
$ brew uninstall --force python3
```

Uninstall pip packages:

```bash
$ pip freeze | xargs pip uninstall -y
```

We recommend running the commands multiple times to ensure everything was uninstalled successfully. Don't forget to run `source ~/.bash_profile` again after uninstalling or restart your shell session.

Uninstall pip:

```bash
$ python -m pip uninstall pip setuptools
```

Sanity check, this should show your system's default python:

```bash
$ python --version  # Python 2.7.10 for macOS High Sierra
```

Remove artifacts from the previous installs:

```bash
$ rm -r ~/.conda
$ rm -r ~/.continuum
$ rm -r ~/.jupyter
$ rm -r ~/.matplotlib
$ rm -r ~/.pyenv
$ rm -r ~/.local/venvs
$ rm -r ~/.virtualenvs
```

### Other packages

You may also want to check if there are other site-packages installed in your system. Only delete what you need:

```bash
$ mdfind kind:folder "site-packages"
```

Restart your terminal session (for good measure) before continuing.

## What we want to install

* [homebrew](https://brew.sh/)
* [pyenv](https://github.com/pyenv/pyenv)
* [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)
* [pipenv](https://github.com/pypa/pipenv)

## Clean Install

### install the gcc compiler from xcode

```bash
$ xcode-select --install
```

### install homebrew

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew update
```

### install python 2 and 3 from homebrew

This should install `python2`, `pip2`, `python3`, and `pip3`. The homebrew version uses the updated versions of openssl, readline, etc.

```bash
$ brew install python@2  # the latest 2.Y release
$ brew install python3   # the latest 3.Y release
```

You should have something like this:

```bash
$ ls -liash $(which python2)  # from homebrew's python2
$ ls -liash $(which python3)  # from homebrew's python3
$ pip2 --version              # from homebrew's python2
$ pip3 --version              # from homebrew's python3
```

Homebrew uses the following directory structure for installing site-packages. Using `pip2` or `pip3` will install the packages here:

* `/usr/local/lib/python<X.Y>/site-packages`

### install pipenv

Install `pipenv` using homebrew's pip3:

```bash
$ pip3 install --user pipenv
```

This should make `pipenv` available user-wide, and will be using homebrew's python3 by default.

### install pyenv and pyenv-virtualenv

We can then install `pyenv` and `pyenv-virtualenv`, to create virtualenvs with specific versions of python.

```bash
$ brew update
$ brew install pyenv
$ brew install pyenv-virtualenv
```

Add the following to your `.bash_profile`:

```bash
# pyenv
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
if which pyenv-virtualenv-init > /dev/null; then
  eval "$(pyenv virtualenv-init -)"
fi
```

Install the latest Python versions and set the global version:

```bash
$ pyenv install --list
$ pyenv install 2.7.15
$ pyenv install 3.6.5
$ pyenv global system
```

*Note*: Setting `pyenv global` to a different version will change the output of `which python`, which can be confusing at times. We recommend using `pyenv local <version>` on specific project directories and use `python2` or `python3` to be more explicit on what versions you want.

*Note 2*: When using `pipenv run python` it will be using the python in your virtualenv, and not the system or global python. This also applies to `pip`, `python3`, etc. Basically the <env>/bin executables take priority.

## Sample `.bash_profile`

```bash
#!/usr/bin/env bash

# explicitly set the standard localization format
export LC_ALL='en_US.UTF-8'
export LANG='en_US.UTF-8'


# homebrew
# ensure user-installed binaries take precedence
export PATH="/usr/local/bin:$PATH"
export PATH="/usr/local/sbin:$PATH"


# export local executables
export PATH="~/.local/bin:$PATH"

# add homebrew's python 3 user executables to PATH
export PATH="~/Library/Python/3.6/bin:$PATH"


# pyenv and pyenv-virtualenv
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
if which pyenv-virtualenv-init > /dev/null; then
  eval "$(pyenv virtualenv-init -)"
fi


# pipenv
eval "$(pipenv --completion)"
if command -v pipenv 1>/dev/null 2>&1; then
  eval "$(pipenv --completion)"
fi


# other useful stuff
gpip2() {
   PIP_REQUIRE_VIRTUALENV="" pip2 "$@"
}
gpip3() {
   PIP_REQUIRE_VIRTUALENV="" pip3 "$@"
}
alias perp="pipenv run python"
alias pyclean="find . -name '*.py[c|o]' -o -name __pycache__ -exec rm -rf {} +"
```

## Sample pip.conf

```bash
$ mkdir -p ~/Library/Application\ Support/pip/
$ vim ~/Library/Application\ Support/pip/pip.conf
```

Add the following to restrict installing to the global pip packages
```
[install]
require-virtualenv = true

[uninstall]
require-virtualenv = true
```

## Further readings

* https://gist.github.com/adamscharf/85a363f0bab254db00ccb3ea9ee45c72
* https://gist.github.com/beck/a79d54f5bda49b0766a43366c603bf5d
* https://jacobian.org/writing/python-environment-2018/
* http://www.dougalmatthews.com/2016/Nov/12/create-an-excellent-python-dev-env/
* https://wilsonmar.github.io/python-install/
* https://gist.github.com/datagrok/2199506
* https://gist.github.com/eliangcs/43a51f5c95dd9b848ddc
* https://chriswarrick.com/blog/2017/07/03/setting-up-a-python-development-environment/
* https://hackercodex.com/guide/python-development-environment-on-mac-osx/
