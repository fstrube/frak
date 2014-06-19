# About

Frak is a deployment tool that uses `rsync` to upload file changes to remote servers. With this tool you can rapidly deploy code without having to commit it into your version control repository, which, although controversial to some, is preferred when working in a fast-paced environment.

# Installation

Simply copy the `bin/frak` file to a location in your `$PATH`. Or, you can symlink it as well.

    $ git clone git@github.com:fstrube/frak.git
    $ ln -s `pwd`/frak/bin/frak ~/bin/frak

# Configuration

All configuration options are stored in a dotfile in the root of your project. This dotfile contains the details necessary to deploy your application. You can run the command `frak init` in the root of your project to get the configuration process started. Below is a sample configuration file.

    server: dev.example.com
    remote_path: /var/www/com.example
    after: cd /var/www/com.example; rm cache/*
    method: rsync

    stage:
      server: dev.example.com

    production:
      server: www.example.com

You can see in the example above that there are three environments. The first four lines constitute the "default" environment. Then, you have a "stage" environment and a "production" environment where the server option varies. You can specify which environment to use when you run frak by doing `frak env=production`.

# Usage

By default, simply running `frak` will attempt to deploy your application. A dry-run is always conducted first, and you are prompted whether or not you want to continue with the deployment. Other commands exist to allow you to pull changes from the server to your local copy, view the diff between your files locally and on the remote server, and execute commands on the remote server.

     Usage: frak <command> [option=value option=value ...]

     Commands
       init          Initialize the current directory for frak deployment
       console       Connect to the remote server via SSH
       diff          Show differences between local and remote files
       pull          Download the remote file(s)
       push          Deploy website via the method specified in the config

     Options
       env           load the specified environment into the config
       method        currently only supports rsync
       command       execute a command on the remote server
       path          defaults to current directory
       remote_path   override what is read from .frak file
       ignore        ignore certain files or folders when deploying

     Examples

       Execute a command on your deployment server:
         frak console command="vi index.html"

       Compare specific files before deploying:
         frak diff path="*.html"

       Deploy files but exclude certain paths:
         frak ignore="logs uploads"