![](/logos/ansible-basics-teal-1280.png)
# Ansible Basics

## Built With

[![tools](https://skillicons.dev/icons?i=ansible,vscode,vim,python,bash,md,css,html,linux,git,github)](https://theologic.us/contact-me/)

# Source

Using the step-by-step at Ansible Docs: [ansible.com](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

# Installing

## My work laptop

```zsh
 jlouthan@siege  ~  python --version
zsh: no such file or directory: /usr/local/bin/python3
 ✘ jlouthan@siege  ~  python -v
zsh: no such file or directory: /usr/local/bin/python3
 ✘ jlouthan@siege  ~  python
zsh: no such file or directory: /usr/local/bin/python3
 ✘ jlouthan@siege  ~  pip install python
zsh: no such file or directory: /usr/local/bin/pip3
```

I found it super odd that I did not have python installed? Now I remember that I wiped/reinstalled macOS. 

```zsh
 ✘ jlouthan@siege  ~  brew install python3
Running `brew update --auto-update`...
~
```

> **Note**
> I use the tilde **~** to denote additional output that would be too much to copypasta here in this little doc.

Then checking my installs because I am a good lil' SysAdmin 😊

```zsh
 jlouthan@siege  ~  which pip
pip: aliased to /usr/local/bin/pip3
 jlouthan@siege  ~  python3 -m pip -V
pip 22.3.1 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)
```

Now for the real magic: FINALLY installing Ansible!

```zsh
  jlouthan@siege  ~  python3 -m pip install --user ansible
Collecting ansible
  Downloading ansible-7.1.0-py3-none-any.whl (42.4 MB)
       ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 42.4/42.4 MB 2.2 MB/s eta 0:00:00
~
```

```zsh

```

```zsh

```
