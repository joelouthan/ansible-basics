![](/logos/ansible-basics-teal-1280.png)
# Ansible Basics

## Built With

[![tools](https://skillicons.dev/icons?i=ansible,vscode,vim,python,bash,md,css,html,linux,git,github)](https://theologic.us/contact-me/)

## Source

Using the step-by-step at Ansible Docs: [ansible.com](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

## Installing

### My laptop as the control node

So your control node is the node (think laptop, workstation, server, container, etc..) is where you want to run Ansible command line from. Now, keeping in mind, this control node must:

- be able to have a command line (WSL for Windows)
- and have at least Python 3.9 installed
- and be able to reach out and connect to your devices over TCP port 22 with the SSH protocol (there can be exceptions to this--but those are headaches and I am not trying to reinvent the wheel)

For the sake of my personal setup, my Mac laptop is perfect for a control node because I hold the keys to the kingdom. That is, my private ssh keys that I use to passwordless SSH into my servers are on this laptop.

```zsh
python --version
zsh: no such file or directory: /usr/local/bin/python3
python -v
zsh: no such file or directory: /usr/local/bin/python3
python
zsh: no such file or directory: /usr/local/bin/python3
pip install python
zsh: no such file or directory: /usr/local/bin/pip3
```

I found it super odd that I did not have python installed? Now I remember that I wiped/reinstalled macOS. Ah ha!

```zsh
brew install python3
Running `brew update --auto-update`...
~
```

> **Note**
> I use the tilde **~** to denote additional output that would be too much to copypasta here in this little doc.

Then checking my installs because I am a good lil' SysAdmin 😊

```zswhich pip
pip: aliased to /usr/local/bin/pippython3 -m pip -V
pip 22.3.1 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)
```

Now for the real magic: FINALLY installing Ansible!

```zsh
python3 -m pip install --user ansible
Collecting ansible
  Downloading ansible-7.1.0-py3-none-any.whl (42.4 MB)
       ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 42.4/42.4 MB 2.2 MB/s eta 0:00:00
~
```

> **Warning**
> Something to pay attention to:
> ```
> WARNING: The scripts ansible, ansible-config, ansible-connection, ansible-console, ansible-doc, ansible-galaxy, ansible-inventory, ansible-playbook, ansible-pull and ansible-vault are installed in '/Users/jlouthan/Library/Python/3.9/bin' which is not on PATH.
>  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
>  WARNING: The script ansible-community is installed in '/Users/jlouthan/Library/Python/3.9/bin' which is not on PATH.
>  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
>```
> Which makes me want to put this path in my .zshrc
> ```
> export PATH="$HOME/bin:$HOME/pilot:/usr/local/bin:`pwd`/flutter/bin:$HOME/Library/Python/3.9/bin"
> ```

Now checking if it is all kosher:

```zsh
ansible --version
ansible [core 2.14.1]
  config file = None
  configured module search path = ['/Users/jlouthan/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /Users/jlouthan/Library/Python/3.9/lib/python/site-packages/ansible
  ansible collection location = /Users/jlouthan/.ansible/collections:/usr/share/ansible/collections
  executable location = /Users/jlouthan/Library/Python/3.9/bin/ansible
  python version = 3.9.16 (main, Dec  7 2022, 10:16:11) [Clang 14.0.0 (clang-1400.0.29.202)] (/usr/local/opt/python@3.9/bin/python3.9)
  jinja version = 3.1.2
  libyaml = True
```

And now, checking the version of Ansible installed:

```zsh
python3 -m pip show ansible
Name: ansible
Version: 7.1.0
Summary: Radically simple IT automation
Home-page: https://ansible.com/
Author: Ansible, Inc.
Author-email: info@ansible.com
License: GPLv3+
Location: /Users/jlouthan/Library/Python/3.9/lib/python/site-packages
Requires: ansible-core
Required-by: 
```

Noice!

Now I am not going to run `devel` of Ansible. So I get to skip that.

## Installing additional components

But something that did catch my eye: [Adding Ansible command shell completion](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#adding-ansible-command-shell-completion)

This I have not seen since I last touched Ansible `2.10.x`. (If it was incorporated back in `2.9` or `2.10`, I didn't have a clue.)

First, install `argcomplete`.

```zsh
python3 -m pip install --user argcomplete
Collecting argcomplete
  Downloading argcomplete-2.0.0-py2.py3-none-any.whl (37 kB)
Installing collected packages: argcomplete
Successfully installed argcomplete-2.0.0
```

Okie smokes. Now what?

Now, we have to configure `argcomplete`.

I am going to be super lazy and do the global configuration method. Use this if you use `bash`:

```
activate-global-python-argcomplete --user
Installing bash completion script /Users/jlouthan/.bash_completion.d/python-argcomplete
```

I, myself, am a man of culture and refined tastes and use `zsh` for my shell. To which, follow these commands:

```zsh
autoload -U bashcompinit
bashcompinit
eval "$(register-python-argcomplete my-awesome-script)"
```

## Configuring

Now we must touch the almighty `ansible.cfg` file. As you might suspect, this controls how you Ansible works on your control node. If you are suspecting that this is important, you would be correct.

Let us see if it is chilling in my `/etc` folder or nah:

```zsh
l /etc
lrwxr-xr-x 1 root wheel 11 Dec  2 06:37 /etc -> private/etc
```

Apparently, I get nothing and like it.

So let me invoke this `ansible-config` which I thought was going to tell me what's what but nothing doing.

But Google reveals that there are two locations for it in either macOS or Linux: `/etc` (which you can just stick it there for the entire machine no matter which user you wanna use) or `$HOME/.ansible/` which is just the current user (that would be you, bucko).

Since this is a laptop, I am the only person who can use it, I am going to stick my `ansible.cfg` in my `$HOME/.ansible/` and let the good times roll.

Now check this snazzy feature from [ansible/examples/ansible.cfg](https://github.com/ansible/ansible/blob/devel/examples/ansible.cfg):

```zsh
# Since Ansible 2.12 (core):
# To generate an example config file (a "disabled" one with all default settings, commented out):
#               $ ansible-config init --disabled > ansible.cfg
#
# Also you can now have a more complete file by including existing plugins:
# ansible-config init --disabled -t all > ansible.cfg

# For previous versions of Ansible you can check for examples in the 'stable' branches of each version
# Note that this file was always incomplete  and lagging changes to configuration settings

# for example, for 2.9: https://github.com/ansible/ansible/blob/stable-2.9/examples/ansible.cfg
```

That is what we call, as the kids like to say nowadays, dope.

Now I don't have any plugins installed per se. But maybe I do. Lets do the second command and see what I get in return:

```zsh
ansible-config init --disabled -t all > ansible.cfg
```

When you do that, you will have a `ansible.cfg` in the directory you are in right now.

So I am going to move that bad boy over to my personal `.ansible` directory:

`mv ansible.cfg ~/.ansible`

Bon appetite!

Now you can stick `ansible.cfg` anywhere you want as long as you stick it in one of the following directories, which Ansibles looks in, first to last:

- ANSIBLE_CONFIG (environment variable if set)
- `ansible.cfg` (in the current directory)
- `~/.ansible.cfg` (in the home directory)
- `/etc/ansible/ansible.cfg`

Now, when Ansible finds it, it only uses that one and ignores the rest of them. For more information, please check out **The Configuration File** over at [Ansible Docs](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#the-configuration-file).

TODO:


