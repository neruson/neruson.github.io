---
layout: post
title: My Ubuntu Setup, version 14.04
---
I installed Ubuntu 14.04 recently, after my old 12.10 install decided to stop booting up. Here are my notes on getting my system set up after a fresh install.

### Keyboard modifications

#### Map Caps Lock to Control

I'm pretty good with the default keyboard shortcuts, but one thing that always bugs me is the Caps Lock key. I never actually need it, and it's far more likely to get in my way, so I always map it to the Control key, which is more useful to me.

Unfortunately, it seems they went and made it more difficult to do this in recent releases of Ubuntu. I had to install gnome-tweak-tool, unlike previous versions where I could do it directly in the settings.

1. `sudo apt-get install gnome-tweak-tool`
2. Open Tweak Tool.
3. Go to the Typing tab (warning: background may turn transparent)
4. Change Caps Lock Key Behavior to "Make Caps Lock an additional Ctrl".

#### Set up Japanese keyboard

I'm teaching a Japanese class, so I need Japanese input to create worksheets and post to the class Facebook group.

To install the Japanese keyboard in 14.04, I followed the guide at [http://moritzmolch.com/1453](http://moritzmolch.com/1453). It helpfully goes into detail on each step, but the summary is:

1. In Language Support, install Japanese.
2. Logout and login to restart your session.
3. In Text Entry, add Japanese (Anthy) as an input method.
4. You can now switch to Japanese input with Super+Space (editable in Text Entry) and between Japanese inputs with Ctrl+Comma (editable in Anthy preferences).

### Dev environment

Most of my development these days is in Django, and I can count the number of programs I need for it on one hand:

* Sublime Text 2
* vim
* Git
* PostgreSQL
* pip/virtualenv/virtualenvwrapper

#### Sublime Text 2

    sudo add-apt-repository ppa:webupd8team/sublime-text-2
    sudo apt-get update
    sudo apt-get install sublime-text

#### vim

While I use Sublime Text for most of my work, I occasionally use vim for quick edits. The built-in vi is always a little awkward for me, so I install vim via Apt.

    sudo apt-get install vim

#### Git (plus SSH setup)

I'm ok being a few versions behind, so I just install Git with Apt, as below. For those looking for a more recent version, Digital Ocean has instructions on [how to install from source on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04).

    sudo apt-get install git

Next, I'll create an SSH key and add my public key to my GitHub account.

    mkdir ~/.ssh
    chmod 700 ~/.ssh
    ssh-keygen -t rsa

After entering and confirming my passphrase, copy and paste the contents of id_rsa.pub to [https://github.com/settings/ssh](https://github.com/settings/ssh). For fun, let's install xsel to copy to clipboard via the command line.

    sudo apt-get install xsel
    xsel --clipboard < ~/.ssh/id_rsa.pub

#### PostgreSQL

Digital Ocean once again saves the day with their [instructions for installing PostgreSQL on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04).

Install:

    sudo apt-get install postgresql postgresql-contrib libpq-dev

(libpq-dev is not strictly required for PostgreSQL, but it's necessary for using the Python psycopg2 package, which I do.)

By default, PostgreSQL is installed under the postgres user, so one of the first things I do is create a user for the username I log into Ubuntu with.

    neruson@mycomputer:~$ sudo -iu postgres
    postgres@mycomputer:~$ createuser --interactive
    Enter name of role to add: neruson
    Shall the new role be a superuser? (y/n) y
    postgres@mycomputer:~$ exit

Then I can create the users and databases I need for my projects as myself.

Create project user:

    neruson@mycomputer:~$ createuser --interactive
    Enter name of role to add: myprojectuser
    Shall the new role be a superuser? (y/n) n
    Shall the new role be allowed to create databases? (y/n) n
    Shall the new role be allowed to create more new roles? (y/n) n

Create project database:

    neruson@mycomputer:~$ createdb myproject

Set user password and grant access:

    neruson@mycomputer:~$ psql -d postgres
    myproject=# \password myprojectuser
    Enter new password:
    Enter it again:
    myproject=# GRANT ALL ON DATABASE myproject TO myprojectuser;
    GRANT

#### pip/virtualenv/virtualenv wrapper

I don't know how I'd manage Python development without these. They're great tools for managing dependencies across projects.

    sudo apt-get install python-pip python-dev
    sudo pip install virtualenv
    sudo pip install virtualenvwrapper

These are the only two Python packages I install globally. For everything else, I use a virtualenv.

To get virtualenvwrapper working, a few lines have to be added to the shell startup file (I use ~/.bashrc).

    export WORKON_HOME=$HOME/.virtualenvs
    export PROJECT_HOME=$HOME/Devel
    source /usr/local/bin/virtualenvwrapper.sh

Then I create my virtualenv and install my project requirements into it.

    mkvirtualenv myproject
    pip install -r myproject/requirements.txt

All set, and ready to code!