# Working with Go in a VS Code Remote Development Container

I've been frustrated for a while with the fact that the Golang ecosystem is very
opinionated on where to put your source code. It's of course possible to set up
your environment to use something else than the defaults, but then you will have
to set up your environment with a bunch of path variables that are IMO not super
clear on exactly how they work.

When VS Code added the functionality to [connect to a container workspace][1], I
figured it would be worthwhile to try to get a Golang development environment up
that way instead.

[1]: https://code.visualstudio.com/docs/remote/containers

This is that attempt.

## How to use this repository

1. Clone this repository to somewhere you want to use as the root of your Golang
   development environment.

1. (Optional) If you don't like to be in my timezone (`Europe/Stockholm`),
   change the argument at the top of the Dockerfile.

1. Open the workspace in VS Code. If you didn't already, VS Code should prompt
   you to re-open the workspace in Remote development instead.

1. The workspace is mounted to `/go/src`, which is the default source root in
   the Go environment setup. So, to work on e.g. [go-training/helloworld][2],
   create folders `github.com/go-training`, and clone the repo into there:

   ```sh
   mkdir -p github.com/go-training
   cd github.com/go-training
   git clone git@github.com:go-training/helloworld
   ```

1. To avoid issues with `git`, add any folders with source repos in them to the
   `.gitignore` file (in the same way as `github.com` is already there).

[2]: https://github.com/go-training/helloworld

## Features

- Based on the `golang` image, so gives you the default setup for Golang
  development

- Runs under a non-root user (by default called `vscode`) inside the container

- Supports Docker and Docker Compose, by mounting the host Docker socket

- Includes your local `~/.ssh` config, letting you use SSH communication with
  e.g. git remotes
