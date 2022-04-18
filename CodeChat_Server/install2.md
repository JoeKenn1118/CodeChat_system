# Goals

**The installation of CodeChat is more efficient and better documented**

"Make. Installation. Simpler." - Dr. Jones

# Old Install Instructions for Reference (Linux)

https://codechat-system.readthedocs.io/en/latest/CodeChat_Server/install.html

1. [Open a terminal.](https://www.howtogeek.com/howto/22283/four-ways-to-get-instant-access-to-a-terminal-in-linux/)

2. Make sure pip, the Python installer, is at least V. 3.7 (https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#linux-and-macos): at the terminal, type `python3 -m pip install --user --upgrade pip`.

3. [Create a virtual environment](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#creating-a-virtual-environment) named *codechat* by typing `python3 -m venv codechat`. This keeps the installation of the CodeChat System from interfering with other installed Python programs and vice versa.

4. [Activate this virtual environment](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#activating-a-virtual-environment) by typing `source codechat/bin/activate`.

5. Install the CodeChat Server by typing `python3 -m pip install --upgrade CodeChat_Server`.

6.  Determine the location of the installed CodeChat Server by typing `which CodeChat_Server`. You’ll need to enter this path when setting up the CodeChat plugin/extension in your IDE.

7. Install the [CodeChat extension/plugin](https://codechat-system.readthedocs.io/en/latest/extensions/contents) for your IDE or text editor.

To update the CodeChat Server, repeat steps 1, 4, and 5.

# Updated Install Instructions (Linux)

1. download codechat.sh (link)
2. chmod 700 codechat.sh
3. bash codechat.sh

 .. NOTE::

  (although now we will still have to nicely explain what's going on as is done in the current instructions in case something goes wrong)


# Current Shell Script (Linux)

    #!/bin/bash
    # install CodeChat

    # Run (without the pound or dollar signs):
    # $ chmod 700 install.sh
    # to make this script executable on your system
    # then run:
    # $ bash install.sh

    # https://stackoverflow.com/questions/6141581/detect-python-version-in-shell-script

    # check for python
    if ! hash python; then
        echo "Python is not installed"
        exit 1
    fi

    # todo: if no python, echo commands to install

    # todo: check python version

    # install / upgrade pip
    python3 -m pip install --user --upgrade pip

    # create virtual environment (no harm doing this multiple times)
    python3 -m venv codechat

    # activate virtual environment
    source codechat/bin/activate
    # . codechat/bin/activate  (alternate command?)

    # check to see if CodeChat is already installed
    # todo: check version number

    if [[ -d codechat ]]; then
        echo "CodeChat already installed"
    else
        python3 -m pip install --upgrade CodeChat_Server
    fi

    # echo server directory to user
    which CodeChat_Server

# Errors (Linux)

- ~~`source codechat/bin/activate` - should `(codechat) $` but does nothing~~ Run with `bash` not `./`

- virtual environment still not activating

- install codechat server errors @ `Building wheels for collected packages: strictyaml, thrift`


# Todo (Linux)
- debug/improve shell script
- rewrite installation instructions
- Fix broken links in Codechat documentation
- rewrite extension installation instructions (if not found)
- Convert to rst and combine with current install.rst

# Todo (Windows)

- debug pwershell script
- find a way to just download `.psl` to make instructions easier to follow.
- Add a system to determine what version of pythin/pip is installed and inform the user if an update is needed.

# Places for things

- [Repository](https://github.com/bjones1/CodeChat_system) 

- [Install Instructions](https://codechat-system.readthedocs.io/en/latest/CodeChat_Server/install.html)

- [Broken Link](https://codechat.readthedocs.io/en/latest/)

- [Broken Link](https://codechat-system.readthedocs.io/en/latest/extensions/contents)


# Tests

- Test subject has agreed to participate

## Scrum 2

- ask Dr. Jones about installation errors
- second guess shell script install method
- get help with virtual environment bug
- get required version of python

-----

# An Overview of Linux Install methods

## Software Manager / Package manager

Pros: 
- Super easy, 10/10, like windows store/Google Play
- gives you "start menu" entry

Cons: 
- Need separate version for every distro, 
- not available on every distro
- always 2-10! years out of date on Mint

## Command Line (e.g. apt, yum, pip)

Pros:
- Current method, known to work
- all other methods require developer effort ranging from minor (shell script) to Herculean 
- most linux users can use a terminal
- Simplicity 8/10

Cons:
- multistep

## Shell Script (.sh)

Pros:
- same as command line, less typing
- simplicity 9/10

Cons:
- stuff still sometimes doesn't work, but now it's harder to see where things went wrong
  - Possible solution, include -v(erbose) to commands where applicable

## PPA

Pros:
- Simplicity 7/10 if things go right
- provides automatic updates

Cons:
- things go wrong. W: GPG error: Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY DA360C64005E0276
- your system diligently updates software years after you've forgotten what it does

## .deb / .rpm

Pros:

- Simplicity 9/10 like windows .exe file
- can do it from gui file manager
  
Cons:

- have to make a different one for each linux, though most companies now seem content with only offering these two


## app image, snap etc

Pros:

- Simplicity 10/10 
- embrace the future!

Cons:

- Simplicity 4/10 if you have to first install Snap
- unsure which future to embrace and which will cease development in 3 weeks

## Flatpak

Pros:

- Simplicity 10/10

Cons:

- Gigabytes used 10/10

## Compile source code yourself

Pros:
- It will be a learning experience!

Cons:
- Simplicity: 1/10 nobody wants a learning experience
- Feels like the devs (choose 2): 
  - are computer science students
  - are computer science professors
  - have died in 2013
  - hate you and desperately want to dissuade you from using their software
  - want you to visit their help forum bc they are lonely
- Makefile *never* works 100% out of the box, leading at *best* to ominous warnings 


