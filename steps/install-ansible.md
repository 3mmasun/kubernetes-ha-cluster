## Install different python versions using pyenv
[Install pyenv on M1 chip](https://medium.com/@laict/install-python-on-macos-11-m1-apple-silicon-using-pyenv-12e0729427a9)

### Setup virtualenv for ansible installation
```bash
pyenv versions
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
brew install pyenv-virtualenv
pyenv virtualenv 3.9.4 <name>
pyenv activate <name>
pyenv deactivate
```

### Install ansible
Simply type `brew install ansible` in the terminal

Check current version installed: `ansible --version`
```
ansible [core 2.12.1]
  config file = None
  ...
  ...
  python version = 3.10.1 (main, Dec  6 2021, 22:18:13) [Clang 13.0.0 (clang-1300.0.29.3)]
  jinja version = 3.0.3
  libyaml = True
```

Test simple ansible command
```bash
# install sshpass unofficial package
curl -L https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb > sshpass.rb && brew install sshpass.rb && rm sshpass.rb
# create ansible working directory, with a simple inventory file inside
mkdir ansible && cd ansible && touch inventory
# add host entry, use double-quotes for passwords with special charactor
master-1 ansible_host=<ip address> ansible_user=<user> ansible_password=<password>
# test ansible
ansible all -m ping -i inventory
# ===== expected output =====
# master-1 | SUCCESS => {
#     "ansible_facts": {
#         "discovered_interpreter_python": "/usr/bin/python"
#     },
#     "changed": false,
#     "ping": "pong"
# }
```
