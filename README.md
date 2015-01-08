# About

Frak is a deployment tool that uses `rsync` to upload file changes to remote servers. With this tool you can rapidly deploy code without having to commit it into your version control repository, which, although controversial to some, is preferred when working in a fast-paced environment.

# Installation

Simply copy the `bin/frak` file to a location in your `$PATH`. Or, you can symlink it as well.

    $ git clone https://github.com/fstrube/frak.git
    $ ln -s `pwd`/frak/bin/frak ~/bin/frak

# Dependencies

The `frak` script relies on the Highline gem for prompting and ANSI-colored output. Simply run `gem install highline` and you should be good to go.

Also, you will need rsync installed on your machine. This should already be installed in most cases. However, if it is not installed you can find it in your Homebrew or apt repositories.

# Configuration

All configuration options are stored in a dotfile in the root of your project. This dotfile contains the details necessary to deploy your application. You can run the command `frak init` in the root of your project to get the configuration process started. Below is a sample configuration file.

    label: Example Frak Project
    server: dev.example.com
    remote_path: /var/www/com.example
    after: cd /var/www/com.example; rm cache/*
    method: rsync
    rsync_path: sudo rsync

    stage:
      server: dev.example.com

    production:
      server: www.example.com
      webhook_url: http://api.example.com/frak/frak.php

You can see in the example above that there are three environments. The first four lines constitute the "default" environment. Then, you have a "stage" environment and a "production" environment where the server option varies. You can specify which environment to use when you run frak by doing `frak env=production`.

## Backups

By default, frak will backup your file changes to a `.backups` folder on the remote server in the path specified by the `remote_path` configuration parameter. Backups contain full copies of any modified or deleted files, as well as a patch file containing line-by-line changes. Currently, restoring from backup is still a manual process.

Backups will remain available for 30 days by default, but you can set the retention policy with the `backup_retention` parameter in the configuration.

## Ignoring Files

When deploying your app, often times you will want to exclude certain files and directories from being pushed to the server. By default, frak will ignore the following files:

    - .frak*
    - .git*
    - .svn*
    - .tags*
    - Capfile
    - Gemfile*
    - Vagrantfile*

You can tell frak to ignore additional files by configuring a `.frakignore` file. The syntax of this file follows rsync's filter rules. Refer to the **Filter Rules** section in the [rsync man pages](http://rsync.samba.org/ftp/rsync/rsync.html). The most common filter is the exclude pattern, as seen below:

    # Sample .frakignore file
    - var/log
    - *.bak
    - *.DS_Store

# Usage

By default, simply running `frak` will attempt to deploy your application. A dry-run is always conducted first, and you are prompted whether or not you want to continue with the deployment. Other commands exist to allow you to pull changes from the server to your local copy, view the diff between your files locally and on the remote server, and execute commands on the remote server.

     Usage: frak <command> [option=value option=value ...]

     Commands
       init          Initialize the current directory for frak deployment
       console       Connect to the remote server via SSH
       diff          Show differences between local and remote files
       pull          Download the remote file(s)
       push          Deploy website via the method specified in the config
       backups:list  List any backups found on the server
       backups:purge Purge all backups
       --help        Display this help message
       --version     Display the current version of frak, also checks for upgrades

     Options
       label         a short name for the project, used by webhooks
       env           load the specified environment into the config
       method        currently only supports rsync
       command       execute a command on the remote server
       path          defaults to current directory
       remote_path   override what is read from .frak file
       rsync_path    override the rsync-path read from .frak file (if present)
       ignore        ignore certain files or folders when deploying
       webhook_url   URL for a webhook which will receive information about the code pushed via frak

     Examples

       Execute a command on your deployment server:
         frak console command="vi index.html"

       Compare specific files before deploying:
         frak diff path="*.html"

       Deploy files but exclude certain paths:
         frak ignore="logs uploads"

## Updating

As of version 1.1.0, frak keeps itself up-to-date with the latest code on Github. Any time a new version is available, you will be prompted to update the frak executable.