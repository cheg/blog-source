---
title: "Macos Install Soft"
date: 2018-01-03T16:51:14+08:00
draft: true
---

## 安装pip3

<!--more-->

```bash
mac:bin $ pip
-bash: pip: command not found
mac:~ $ easy_install --version
setuptools 18.5 from /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python (Python 2.7)
mac:bin $ easy_install<Tab><Tab>
easy_install      easy_install-2.7  easy_install-3.6
mac:~ $ sudo easy_install-3.6 pip
mac:~ $ pip --version
-bash: pip: command not found
mac:~ $ pip3 --version
pip 9.0.1 from /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages (python 3.6)
mac:~ $ which pip3
/usr/local/bin/pip3
mac:~ $ cd /usr/local/bin/
mac:bin $ ls -l pip*
lrwxrwxr-x  1 mac  admin  66 Dec  8 19:19 pip3 -> ../../../Library/Frameworks/Python.framework/Versions/3.6/bin/pip3
lrwxrwxr-x  1 mac  admin  68 Dec  8 19:19 pip3.6 -> ../../../Library/Frameworks/Python.framework/Versions/3.6/bin/pip3.6
```
## 安装aiohttp

```bash
mac:~ $ pip3 install aiohttp
Collecting aiohttp
  Downloading aiohttp-2.3.7-cp36-cp36m-macosx_10_12_x86_64.whl (376kB)
    100% |████████████████████████████████| 378kB 31kB/s
Collecting yarl>=0.11 (from aiohttp)
  Downloading yarl-0.17.0.tar.gz (135kB)
    100% |████████████████████████████████| 143kB 15kB/s
Collecting async-timeout>=1.2.0 (from aiohttp)
  Downloading async_timeout-2.0.0-py3-none-any.whl
Collecting multidict>=3.0.0 (from aiohttp)
  Downloading multidict-3.3.2-cp36-cp36m-macosx_10_12_x86_64.whl (193kB)
    100% |████████████████████████████████| 194kB 8.0kB/s
Requirement already satisfied: chardet in /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages (from aiohttp)
Requirement already satisfied: idna>=2.0 in /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages (from yarl>=0.11->aiohttp)
Installing collected packages: multidict, yarl, async-timeout, aiohttp
  Running setup.py install for yarl ... done
Successfully installed aiohttp-2.3.7 async-timeout-2.0.0 multidict-3.3.2 yarl-0.17.0
mac:~ $
```
