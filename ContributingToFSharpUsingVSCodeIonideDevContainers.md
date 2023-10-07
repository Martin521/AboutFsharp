# Contributing to the F# compiler using VS Code, Ionide and Dev Containers

The FSharp repository has great support for VS Code with Ionide and Dev Containers. It still took me a while though, as a newcomer, to understand how it fits all together and to get my infrastructure up and running. 

One reason was that, currently, `fsharp/DEVGUIDE.md` is not really up to date and can be misleading. My main hint in our context: when using Dev Containers, forget about `build.sh`.

So this is a brief summary of the setup that may help others in the future.

*Note: I am writing this on October 7, 2023 and most of it will probably soon be outdated.*

*Another note, written a few hours later: the amazing F# team (lead) has already integrated my fixes [here](https://github.com/dotnet/fsharp/pull/16089) and [here](https://github.com/dotnet/fsharp/pull/16090), so I have to re-write some paragraphs.*

## About the Dev Containers extension of VS Code

This extension does amazing magic. It reads the devcontainer configuration from the remote repository, creates the container, installs all the prerequisites needed for the specific repository, sets up a remote VS Code server in the container, installs the necessary extensions there, sets up a gpg agent that supports code signing in the container with your keys, and a lot more.

## Local setup

I have a Windows 11 laptop. VS Code is installed with Ionide and Dev Containers extensions. And Docker Desktop.

## Setting up the Dev Container

* Fork the dotnet/fsharp repository
* Open VS Code
* Run (i.e. Ctrl Shift P) `Dev Containers: Clone Repository in Named Container volume ...`
    * Enter the location of the fork that you just created (it will be offered as first choice automatically)
    * Enter a name such as "MyRepositories" for the volume
    * Confirm to the repository name `fsharp`
* Watch the container being built
* ***Don't*** install the extensions recommended by VS Code (they are outdated, everything works out of the box now)
* Wait until the `dotnet test` run (indicated in the status bar, running on behalf of the test explorer to find the tests) has finished

... and you are done.

## Basic activities

* Press `Ctrl Shift B` and choose `Build (Debug)` to build the `FSharp.Compiler.Service` solution.
    * >Important note: Make sure the Ionide solution explorer shows the above solution (and not FSharp.sln, which is only meant for use with `build.sh`). And never use `build.sh`.

* Select a test in the Ionide test explorer and run it.

* Debugging tests out of the test explorer, unfortunately, currently does not work. (I guess this has to do in some way with BUILDING_USING_DOTNET, but I still have to find out what needs to be changed (in Ionide probably)). However, below is a work-around.

* Make a code fix and check it in.

## Work-around for debugging tests

Add the following task to `.vscode/tasks.json`:

```
       { 
            "label": ".NET Core Test with debugger", 
            "type": "process", 
            "isBackground": true, 
            "command": "dotnet", 
            "args": [ "test", "-c", "Debug", "--filter", "My new test", "FSharp.Compiler.ComponentTests"], 
            "options": 
                { 
                    "cwd": "${workspaceFolder}/tests", 
                    "env": 
                    { 
                        "VSTEST_HOST_DEBUG": "1" 
                    }, 
                }, 
            "group": "test", "presentation": 
                { 
                    "echo": true,
                    "reveal": "always",
                    "focus": false,
                    "panel": "shared"
                },
            "problemMatcher": [] 
        },
 ```

 Replace "My new test" by the name of your new test (or some existing test)

 Run the task. Note the process id.

 Make sure that in the Debug window, the launch configuration "Attach to a .net process" is selected (You have to do this only once).

 Add breakpoints as necessary. Press F5. After the attach-breakpoint is reached, press F5 again. Wait until your own breakpoint is hit.

 Have fun finding your way through the compiler code.

 ## Feedback

 PLEASE send feedback (via issues or on FSF Slack) if you find mistakes or missing items, or have other remarks, or questions.