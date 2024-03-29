![](/logos/ansible-basics-teal-1280.png)
# Ansible Basics

## Built With

[![tools](https://skillicons.dev/icons?i=ansible,vscode,vim,python,bash,md,css,html,linux,git,github)](https://theologic.us/contact-me/)

## Source

Using the step-by-step at Ansible Docs: [docs.ansible.com](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

## Installing Ansible

### My laptop as the control node

So your control node is the node (think laptop, workstation, server, container, etc..) is where you want to run Ansible command line from. Now, keeping in mind, this control node must:

- be able to have a command line (either Terminal in  Linux, macOS, or UNIX or [WSL for Windows](https://learn.microsoft.com/en-us/windows/wsl/install))
- and have at least Python 3.9 installed
- and be able to reach out and connect to your devices over TCP port 22 with the SSH protocol (there can be exceptions to this--but those are headaches and I am not trying to reinvent the wheel here.)

For the sake of my personal setup, my Mac laptop is perfect for a control node because I hold the keys to the kingdom. That is, my private ssh keys that I use to passwordless SSH into my servers are on this laptop.

### Install Python

```zsh
python --version
```

I found it super odd that I did not have python installed? Now I remember that I wiped/reinstalled macOS. Ah ha!

```zsh
brew install python3
```

**Output:**

```zsh
Running brew update --auto-update...
~
```

> **Note**
> I use the tilde **~** to denote additional output that would be too much to copypasta here in this little doc.

Then checking my installs because I am a good lil' SysAdmin 😊

```zsh
which pip
```

**Output:**

```zsh
pip: aliased to /opt/homebrew/bin/pip3
```

### Install Ansible Core

Now for the real magic: FINALLY installing Ansible!

```zsh
python3 -m pip install --user ansible
```

**Output:**

```zsh
Collecting ansible
  Downloading ansible-8.0.0-py3-none-any.whl (42.2 MB)
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
> export PATH="$HOME/bin:$HOME/pilot:/usr/local/bin:`pwd`/flutter/bin:$HOME/Library/Python/3.11/bin"
> ```

Now checking if it is all kosher:

```zsh
ansible --version
```

**Output:**

```zsh
ansible [core 2.15.0]
  config file = None
  configured module search path = ['/Users/jlouthan/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /Users/jlouthan/Library/Python/3.9/lib/python/site-packages/ansible
  ansible collection location = /Users/jlouthan/.ansible/collections:/usr/share/ansible/collections
  executable location = /Users/jlouthan/Library/Python/3.9/bin/ansible
  python version = 3.9.6 (default, Mar 10 2023, 20:16:38) [Clang 14.0.3 (clang-1403.0.22.14.1)] (/Library/Developer/CommandLineTools/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
```

And now, checking the version of Ansible installed:

```zsh
python3 -m pip show ansible
```

**Output:**

```zsh
Name: ansible
Version: 8.0.0
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

> **Note**
> I saw that ansible-code upgraded to `2.15.0` and I was wondering, "How do I upgrade?"
> `pip install ansible`
> I know. Confusing because you want to use `pip upgrade` or `pip update` but with `pip` it doesn't work like that.

Now I am not going to run `devel` of Ansible. So I get to skip that.

#### Installing additional components

But something that did catch my eye: [Adding Ansible command shell completion](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#adding-ansible-command-shell-completion)

This I have not seen since I last touched Ansible `2.10.x`. (If it was incorporated back in `2.9` or `2.10`, I didn't have a clue.)

First, install `argcomplete`.

```zsh
python3 -m pip install --user argcomplete
```

**Output:**

```zsh
argcomplete
Collecting argcomplete
  Downloading argcomplete-3.0.8-py3-none-any.whl (40 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 40.0/40.0 kB 221.1 kB/s eta 0:00:00
Installing collected packages: argcomplete
Successfully installed argcomplete-3.0.8
```

Okie smokes. Now what?

Now, we have to configure `argcomplete`.

I am going to be super lazy and do the global configuration method. Use this if you use `bash`:

```zsh
activate-global-python-argcomplete --user
```

**Output:**

```zsh
WARNING: zsh has no standard user completions directory. To use global completion with zsh, please run this command using sudo, or run "echo $fpath" and install the completion module into one of the listed directories using "activate-global-python-argcomplete --dest=- > completions-dir/_python-argcomplete".
Installing /Users/jlouthan/Library/Python/3.11/lib/python/site-packages/argcomplete/bash_completion.d/python-argcomplete to /Users/jlouthan/.bash_completion...
Installed.
Please restart your shell or source the installed file to activate it.
```

I, myself, am a man of culture and refined tastes and use `zsh` for my shell. To which, follow these commands:

```zsh
autoload -U bashcompinit
bashcompinit
eval "$(register-python-argcomplete my-awesome-script)"
```

## Configuring Ansible `ansible.cfg`

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
ansible-config init --disabled -t all >  ~/.ansible/ansible.cfg
```

Bon appetite!

Now you can stick `ansible.cfg` anywhere you want as long as you stick it in one of the following directories, which Ansible looks in, first to last:

- ANSIBLE_CONFIG (environment variable if set)
- `ansible.cfg` (in the current directory)
- `~/.ansible.cfg` (in the home directory)
- `/etc/ansible/ansible.cfg`

Now, when Ansible finds it, it only uses that one and ignores the rest of them. For more information, please check out **The Configuration File** over at [Ansible Docs](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#the-configuration-file).

> **Warning**
> What you cannot do is keep the `ansible.cfg` file in the same folder as your working Ansible setup. In other words, you cannot expose your `ansible.cfg` to the outside world--whether it be the file itself or the directory that holds `ansible.cfg`. So, `/etc` would be key. Your `$HOME/.ansible` is also ideal (so long as you don't `chown 777` it).

> **Note**
> To that end, to help stop bad habits before they start, I am giving you an [example `ansible.cfg` called `EXAMPLE-ansible.cfg`](EXAMPLE-ansible.cfg) in the root of this repo. But in order to make Ansible look in the current directory, you have to specify it. And I am not going to tell you how.

## Inventory `inventory.yml`

I could write the inventory into two file types: `ini` or `yml`. But since all of playbooks are in yaml, I think I want to keep it simple and just write it in yaml.

```yml
---
all:
  hosts:
    barracks:
    reactor:
  children:
    web:
      hosts:
        barracks:
    db:
      hosts:
        reactor:
```

> **Note**
> Most up-to-date is location [here](inventory.yml)

> **Note**
> I know for a fact from my control node (my laptop) can hit `barracks` server via hostname alone because it is in my `$HOME/.ssh/config` file. Let us see if Ansible uses this config.

TODO: Test initial run