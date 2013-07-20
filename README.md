#PyProMan

PyProMan (that's **Py**thon **Pro**ject **Man**ager) is a small package that aims to make switching between python projects and their respective virtualenvs easier.  Usually it's enough to run the activate script in each project's virtualenv, but I'm (particularly) lazy.  Also, this doesn't agree with SublimeText.  *(see ``Motivation``_)*

# Usage

- Once you've set up pyproman, just prefix your commands with `proj` much as you would `sudo`.
- If you previously ran a script with `python some_project/test.py` instead use `proj python some_project/test.py`.
- If you're installing a package, instead of `pip install numpy` use `proj pip install numpy`.

# Setup
1. Setup the following environment variables:

        $PROJ - the folder that projects are in
        $VENV - the folder that virtualenvs should be saved in
2. Put ``proj`` somewhere on your path.

# Example
Let's create a sample project, `foo` with a basic script.

    ~ $ cd $PROJ
    ~/projects $ mkdir foo
    ~/projects $ cd foo
    ~/projects/foo $ proj init python3.3
    ... A bunch of output as we set up a virtualenv with python3.3, using --no-site-packages

Or pass a folder name, much as you would with git clone:

    ~ $ proj init foo python3.3

A quick script:

    ~/projects/foo $ echo "print('Hello, World.')" > script.py

Any of the following work:

    ~/projects/foo $ proj python script.py
    Hello, World.
    ~ $ proj python projects/foo/script.py
    Hello, World.
    /tmp $ proj python ~/projects/foo/script.py
    Hello, World.

### What other scripts can I wrap?

Anything that lives in the bin/ folder of a virtualenv can be run using proj.

Scripts in bin/ such as python or nosetests which specify a file to run can be called from anywhere, since the project name will be pulled out of the first (non option) argument to that function.

For scripts like pip that don't pass a parameter which identifies the project, these must be run from within the project folder.

#### Pip - must be run from the project folder

Installing nose:

    ~/projects/foo $ proj pip install nose
    ... A bunch of output as pip installs nose, including the script nosetests in $VENV/foo/bin/

#### Nosetests

We can either run all tests from the project directory:

    ~/projects/foo $ proj nosetests
    ----------------------------------------------------------------------
    Ran 0 tests in 0.000s

    OK

Or against a specific test (which isn't a test in this case) from anywhere:

    /tmp $ proj nosetests ~/projects/foo/script.py
    ----------------------------------------------------------------------
    Ran 0 tests in 0.000s

    OK

# Motivation

SublimeText is my primary development tool, and I wanted switching between projects to be as close to zero work as possible.  Unfortunately, the build system was hardwired to a specific python instance and either of these seemed like too much work:

1. Create a build system for each project that has a different virtualenv and update the cmd.  This also requires switching the build system in a menu whenever I change projects.
2. Change the cmd string of a single build system whenever I switch projects.

Either of those is a lot of work.  This way, I can use the same build system cmd and it will map to the right venv python.  Here's my python build system in full:

    {
        "cmd": ["proj", "python", "-u", "$file"],
      "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    	"selector": "source.python",
    	"env":
    	{
    		"PYTHONPATH": "$PROJ"
    	}
    }
