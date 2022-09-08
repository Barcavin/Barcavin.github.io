---
title: 'Visual Studio Code on Remote Server'
date: 2021-09-19
permalink: /posts/2021/09/19/vscode-ssh
tags:
  - Visual Studio Code
  - Tool
---

<!-- python extension
ssh configuration setup
what if vs code server crash
rsync -->

[Visual Studio Code](https://code.visualstudio.com/) has become my favorite all-in-one editor since it has a list of excellent extensions which allow accomplishing all my work in one place. For instance, it supports Jupyter Notebook (I love its variable explorer), LaTex writing, Jira Integration (No need to go back and forth between editor and browser), and Python Debugger tool (Save me huge amount of time debugging). Recently, its [remote development tools](https://code.visualstudio.com/docs/remote/remote-overview) really catch my heart on its simple setup but powerful functionality. 

The remote development can be via ssh or container. We just cover ssh part here. As there are also a lot of other great tutorials about setting it up so I'd like to go through it more with my personal tips and tricks.

![VS Code](/images/mohammad-rahmani-Y5yxdx2a4PI-unsplash.jpg)

What I like about it
======

> VS Code provide a local-quality development experience - including full IntelliSense (completions), code navigation, and debugging - regardless of where your code is hosted.

1. Have a file explorer for the remote server
2. Git integration
3. Code completion and Class/Method Signature
4. Debug just like local
5. Jupyter Notebook via Jupyter Server
6. Python Extension works the same on the server side

Visual Studio Code SSH Setup
======
In general, one can follow the [official document](https://code.visualstudio.com/docs/remote/ssh) about setting up the VS Code server.

SSH configuration Setup
======
If you have some hosts used a lot, or your ssh command is more than just `ssh user@hostname`, you
may want to use the [advanced settings](https://code.visualstudio.com/docs/remote/ssh#_remember-hosts-and-advanced-settings) for ssh connection.

My setup of SSH config:
```
Host crc
  HostName crcfe01.crc.nd.edu
  User <username>
  ProxyJump <username>@jumpbox.nd.edu
```

If the ssh server has a jump server as the bridge to access, one can set up the ProxyJump in SSH config.

What if the VS Code Server crash?
======
Occasionally, my VS Code cannot connect to the remote server even though I have successfully set up
the connection before. Often, the issue can disapper after a while, but there are some cases that
you need to uninstall the VS Code server on the remote side and reinstall it.

The officially suggested way is to run the VS Code command palette and call `Remote-SSH: Uninstall VS Code Server from Host...`. However, as it fails to connect, one can't run the command.

The other way is to remove the entire VS Code server folder on the remote server if your SSH connection
from terminal still works.

```
rm -rf ~/.vscode-server
```

If the VS Code server is still running on the host server, the file in `.vscode-server` may not be deleted as it uses the resource. Another way to restart the VS Code server is:

```
lsof +D .vscode-server/
kill -9 
ps uxa | grep <username>/.vscode-server | awk '{print $2}' | xargs kill -9
```

File sync to local machine
======
Even though I always use Git to manage the source code on the remote server, there still can be a chance that
you want to sync the file on the remote side to local machine. [Rsync](https://code.visualstudio.com/docs/remote/troubleshooting#_using-rsync-to-maintain-a-local-copy-of-your-source-code) can be your pal here!