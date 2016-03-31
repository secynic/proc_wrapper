============
proc_wrapper
============

proc_wrapper is a simple bash script for wrapping commands with process
control and logging. It works well with cron and managing hung processes
e.g., kill, restart.

Features
========

* Control the run time of processes.
* Lock commands by name to avoid duplicate processes.
* Can be used with cron or directly.
* Logging
* BSD license

Usage
=====

Command::

  proc_wrapper.sh [-c '<string>'] [-n <string>] [-a] [-t <int>] [-ld <dir>]
    [-td <dir>] [-d] [-v]

Options::

  -c|--command '<string>' : Command (required)
    The command to run. Must be in single quotes.

  -n|--name '<string>' : Unique name (required)
    A unique name string used to identify this command. Needed for process
    tracking, log file names, and duplicate command support. Must be in quotes.

  -a|--active : Toggle active
    When set, this script remains active throughout the duration of the process
    or timeout. Disabled by default. Generally, this option should not be used
    when running via scheduled jobs e.g., cron.

  -t|--timeout <int> : Timeout (seconds)
    The maximum amount of seconds the command is allowed to run. The default
    value is 0, which does not set a time constraint on the command. Generally,
    this value should be greater than the scheduled interval when running via
    jobs e.g., cron.

  -ld|--logdir <dir> : Log directory path
    The path of the directory used to store log files. The default directory is
    /var/log.

  -td|--tempdir <dir> : Temp directory path
    The path of the temp directory used to store pid files. The default
    directory is /tmp.

  -d|--debug : Debug
    When set, verbose logging is enabled. Disabled by default.

  -v|--version : Version
    Prints the script version and exits.

Usage Examples
==============
**In the below examples, '/home/uname' is used as the example user directory.
Make sure to follow hardening standards for locations, permissions, etc.**

|

Python script with 180s timeout, active enabled, and directories::

  cd /home/uname/proc_wrapper
  ./proc_wrapper.sh -c '/usr/local/bin/python /home/uname/myproj/script.py' -n
    'myproj' -a -t 180 -ld /home/uname/myproj/log -td /home/uname/myproj/tmp

The above in cron, run every minute (**-a is not used here**)::

  crontab -e
  * * * * * /home/uname/proc_wrapper/proc_wrapper.sh -c '/usr/bin/python -u
    /home/uname/myproj/script.py' -t 180 -ld /var/log/myproj -td /tmp/myproj >>
    /var/log/myproj-cron.log 2>&1

Show the proc_wrapper version::

  cd /home/uname/proc_wrapper
  ./proc_wrapper.sh -v

Installing
==========

Retrieve the latest version::

  cd /tmp
  wget https://github.com/secynic/proc_wrapper/tarball/master

Copy proc_wrapper files to some folder::

  tar -zxvf master -C /home/uname/proc_wrapper --strip-components=1
  chmod u+x /home/uname/proc_wrapper