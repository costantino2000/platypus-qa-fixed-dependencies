Platypus QA - fixed dependencies fork
===========

This is a fork of the Platypus Question Answering System. The original has not been updated since the 5th of July 2017, and because of this the installation and server running processes are "broken", which means they search for pip dependencies that, in their actual version, require a python version greater than 3.6, the highest supported version of the service.

Since I had to use the program for a University project (after that I stopped because it didn't support what I needed), I had this problem, and I began searching in https://pypi.org/ the working versions of the pip packages. After lots of fails I finally settled for the last versions of the packages located in the file "requirements.txt" that were released just before the date of the last commit (5 July 2017). I decided to fork the repo and write this guide to help people that like me might face the same problems, so that they might find this guide and maybe fix the issue.

Disclaimer: I am not an expert. There are probably better ways to fix this problem, I just wanted to share the solution I found to help people with my same problem. If this doesn't work for you or you know a better solution feel free to let me know in the issues or discussion tabs, just keep in mind that I'm not an expert in Question Answering services and Python, but I'll try to help when I can.<br /><br />
<b>While the web interface of the API works, making a query will cause the system to crash, so it's still not fully functional.</b> If anyone is able to find the cause is welcomed to make a pull request, this is where I stopped.

## Install and run

Tested on 17 May 2022 on Ubuntu 20.04.4 WSL (Windows Subsystem for Linux).

First, extract the folder with the source code where you want, then open the terminal inside it (shift -> right mouse click inside the folder -> open linux shell here). You will need to have installed python 3.6 with the dev and distutil packages, virtualenv, gcc and g++. These are the commands needed from a clean installation of wsl (add -y at the end of the lines if you want bypass the confirmation prompt):

<b>2 September 2022 Update</b>: After trying to replicate the steps in a virtual machine with Linux Mint 21, I found out that the deadsnakes repository method doesn't work, scroll down until before the original readme for the pyenv guide.

```
sudo apt update &&
sudo apt upgrade &&
sudo apt install software-properties-common &&
sudo add-apt-repository ppa:deadsnakes/ppa &&
sudo apt install python3.6 python3.6-dev python3.6-distutils virtualenv gcc g++
```
Then create a virtual environment and activate it:
```
python3 -m virtualenv -p=/usr/bin/python3.6 env &&
source env/bin/activate
```
Now you will need to manually install the dependencies with pip. I discovered that by using the default setup script some of the dependencies of the dependencies won't install (but the setup will still end, bringing problems later), and that installing them manually solved the problem (sympy is a dependency of calchas and werkzeug is a dependency of flask, they must be installed before them or it will download incompatible versions):
```
pip install spacy==1.8.2 &&
pip install requests==2.18.1 &&
pip install dateparser==0.6.0 &&
pip install langdetect==1.0.7 &&
pip install PyLD==0.7.2 &&
pip install werkzeug==0.12.2 &&
pip install Flask==0.12.2 &&
pip install flask-swaggerui==0.0.1 &&
pip install flask-cors==3.0.3 &&
pip install sympy==1.0 &&
pip install calchas==0.2 &&
pip install editdistance==0.3.1 &&
pip install pygeoif==0.7
```
Finally you can run the installation script and the server:
```
python3 setup.py install &&
python3 flask_server.py
```
This should make the web interface work on localhost:8080, let me know if I made any typos, the commands don't work or if you want to add a guide for other environments beside WSL.
<br /><br />

<b>Pyenv method</b> (if the deadsnakes repository method does not work):
```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl &&
curl https://pyenv.run | bash
```
Open the file .bashrc in your home directory and append at the end:
```
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```
Close and reopen your shell and install Python 3.6:
```
env CFLAGS=-O2 pyenv install 3.6.15 &&
env CFLAGS=-O2 pyenv install 3.6-dev
```
You can now create the virtual environment from inside the Platypus folder:
```
pyenv virtualenv 3.6.15 env-platypus &&
source ~/.pyenv/versions/env-platypus/bin/activate
```


Original README
===========

[![Build Status](https://travis-ci.org/askplatypus/platypus-qa.svg?branch=master)](https://travis-ci.org/askplatypus/platypus-qa)

Platypus QA is the question answering service of Platypus.
You could see it live at [qa.askplatyp.us](http://qa.askplatyp.us)

## Install and run

Platypus QA relies on python 3.5+ and packages available using pip. It also uses
other Platypus services. The source code is setup in order to use production
version of these services automatically.

To install run:
```
python3 setup.py install
```

To run the Platypus QA server on port 8000:
```
python3 flask_server.py
```

To build the docker file:
```
docker build . -t platypus-qa
```

It creates a docker image named `platypus-qa` listening on port 8000.


## Quick introduction

Platypus QA is built from:

* Natural Language Processing tools available in the `nlp` package. It
provides a common interface (`model` file) and wrapper for CoreNLP and
SyntaxNet.
* A model for representing knowledge defined in `database.formula` and
based on RDF and OWL specifications (`database.owl` file).
* Wrapper for knowledge base allowing to retrieve entities and relations and
execute queries. Currently only Wikidata is supported
(`database.wikidata` file).
* A grammatical analyzer for questions, mainly implemented in
`analyzer.grammatical_anlyzer`.
* `request_handler` that puts all pieces together.

If you want to use it as a library, the only stable interfaces are the ones directly accessible from the `platypus_qa` module
(and not from submodules and files).
