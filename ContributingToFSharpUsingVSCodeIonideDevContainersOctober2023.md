# Contributing to the F# compiler using VS Code, Ionide and Dev Containers

VS Code with Ionide and Dev Containers is a great environment for working on the FSharp repository. It still took me a while though, as a newcomer, to understand how the pieces all fit together and to get my infrastructure up and running. 

One reason was that, currently, `fsharp/DEVGUIDE.md` is not really up to date and can be misleading. My main hint in this context: when using Dev Containers, forget about `build.sh`.

So this is a brief summary of the setup that may help others in the future.

*Note: I am writing this on October 23, 2023 and most of it will probably soon be outdated.*

## About the Dev Containers extension of VS Code

This extension does amazing magic. It reads the devcontainer configuration from the remote repository, creates the container, installs all the prerequisites needed for the specific repository, sets up a remote VS Code server in the container, installs the necessary extensions there, sets up a gpg agent that supports code signing in the container with your keys, and a lot more.

## Host setup

I have a Windows 11 laptop. VS Code is installed with Ionide and Dev Containers extensions. And Docker Desktop.

## Setting up the Dev Container

* Make sure Docker is running
* Fork the dotnet/fsharp repository
* Open VS Code
* Run (i.e. Ctrl Shift P) `Dev Containers: Clone Repository in Named Container volume ...`
    * Enter the location of the fork that you just created (it will be offered as first choice automatically)
    * Select the `main` branch
    * Enter a name such as "MyRepositories" for the volume
    * Confirm the repository name `fsharp`
* Watch the container being built
* ***Don't*** install the extensions recommended by VS Code (they are outdated, everything works out of the box now)
* Wait for the initial build and the test discovery of the test explorer to finish. (The latter is still ongoing if you see `Running MSBuild 'Build' target` in the notification area).

... and you are done.

The whole thing takes about 10 minutes on my laptop.

When I reopen the repository, it takes less than 20 seconds until I can start editing the sources. It takes about 2 minutes until the compiler test file that have open (and its huge number of dependencies) is fully checked and annotated by Ionide and another minute for the test discovery to finalize.

BTW I reopen the repository through the Windows task bar context menu for VS Code, selecting `fsharp in MyRepositories` . I have not yet found how to do it out of VS Code.

## Basic activities

* Press `Ctrl Shift B` and choose `Build (Debug)` to build the `FSharp.Compiler.Service` solution.
    * >Note: Make sure the Ionide solution explorer shows the above solution (and not FSharp.sln, which is only meant for use with `build.sh`). And never use `build.sh`.

* Select a test in the Ionide test explorer and run it or debug it by clicking the respective icon.

* Make a code fix and check it in. :-)


 ## Problems?

 You have messed stuff up? Something does not work anymore and you can't find the reason?

 Just delete the containers and volumes in Docker Desktop and start all over. It just takes a few minutes.
 
 ## Feedback

 Please send feedback on this document (via issues or on FSF Slack) if you find mistakes or missing items or have other remarks or questions.