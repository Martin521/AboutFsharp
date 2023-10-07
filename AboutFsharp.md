# Contributing to the F# compiler using VS Code Dev Containers

The FSharp repository has great support for VS Code and Dev Containers, for development, testing and debugging. As a newcomer, it still took me a while to get my infrastructure up and running and to find and fix a few minor items. So this as a summary that may help others in the future.

Here also is what I miss in many posts: This was written on October 7, 2023 and will probably soon be outdated again.

At this point fsharp/DEVGUIDE.md was not really up to date and misleading in some aspects. Most important: when using Dev Containers, forget about `build.sh`.

## Local setup

I have a Windows 11 laptop. VS Code is installed with Ionide and Dev Containers extensions. And Docker Desktop.

## Setting up the Dev Container

* Fork the dotnet/fsharp repository
* Open VS Code
* Run (i.e. Ctrl Shift P) `Dev Containers: Clone Repository in Named Container volume ...`
    * Enter the location of the fork that you just created (will be offered as first choice automatically)
    * Enter a name such as "MyRepositories" for the volume
    * Agree the the repository name `fsharp`
* Watch the container being built
* Don't install the extensions recommended by VS Code
* Wait until the `dotnet test` run (indicated in the status bar) has finished

... and you are done.

## Basic activities

Press `Ctrl Shift B` and choose `Build (Debug)` to build the `FSharp.Compiler.Service` solution.

>This, btw is important: make sure the Ionide solution explorer shows this solution (and not FSharp.sln, which is only meant for use with `build.sh`); also never use `build.sh`.

Select a test in the Ionide test explorer and run it.

Debug run, unfortunately, currently does not work. (This also has to do with BUILDING_USING_DOTNET in some way, I guess). But below is a work-around.

Make a code fix and check it in. If you have code signing enabled, this will unfortunately not work out of the box, because gnupg is not installed automatically. I have created a [fix](https://github.com/dotnet/fsharp/pull/16089) for it. If you add that line and rebuild the container (or if the PR is accepted before you start), everything works like a charm.

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

 Make sure that in the Debug window, the launch configuration "Attach to a .net process" is selected (You have to do that only once).

 Add breakpoints as necessary. Press F5. After the attach-breakpoint is reached, press F5 again. Wait until your own breakpoint is hit.

 Have fun finding your way through the compiler code.