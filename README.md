# Užitečné aliasy pro práci s IOTstack

Kompletní český návod naleznete přímo na webu:
https://www.iotcz.cz/2021/03/28/navody/iotstack-nastaveni-aliasu/

Chcete-li, můžete použít původní v angličtině:

# Useful aliases for working with IOTstack

When working with [IOTstack](https://github.com/SensorsIot/IOTstack), it is useful to automate many common tasks such as:

* Navigating to a container's "services" directory (eg to edit its environment file);
* Launching a shell **inside** a container (for general nosing around);
* Launching a specific process **inside** a container (eg the InfluxDB command line interface);
* Executing common tasks (like "up" and "down") without having to first change your working directory to `~/IOTstack`;
* Extending commands to fill gaps. For example, terminating a single container instead of taking down your entire stack;
* and so on…

Although you could create a scheme of shell scripts to accomplish most of these goals, aliases and shell functions are usually simpler and, often, the only way to accomplish some goals.

Note:

* This GitHub repository used to be a [gist](https://gist.github.com/Paraphraser/7612d3c780d284a502bd1f158c5186e8). Unfortunately, the copy-and-paste method of acquiring the aliases would occasionally cause problems when the original Unix `0x0A` line-endings (LF) were replaced with DOS/Windows `0x0D 0x0A` line-endings (CR+LF). The alias file would then fail. Providing the alias file via Git should reduce the likelihood of that happening.  

## Installation

### Step 1

Login to your Raspberry Pi and clone this repository. The recommended commands are:

```
$ mkdir -p "$HOME/.local"
$ git clone https://github.com/Paraphraser/IOTstackAliases.git "$HOME/.local/IOTstackAliases"
```

### <a name="step2"> Step 2 </a>

Test the result like this:

```
$ . "$HOME/.local/IOTstackAliases/dot_iotstack_aliases"
```

Notes:

* The `.` followed by a space at the start of the command is called a *source* statement. It tells the shell to process the file in-line, like an `#include` statement in a programming language.

	*Sourcing* a file is different to *executing* a file. If you check, you will see that `dot_iotstack_aliases` does not have execute permission. Neither does the file start with `#!/bin/bash`. This is intentional. Although you *can* add execute permission and then execute the file as a command, it will not produce the correct result. It **must** be sourced.
	
* The "dot\_" prefix on the filename is intended to remind you that the file should be *sourced*.  

If you have done the first two steps correctly, you should get output like this:

```
Useful Docker aliases:
      Gitea: GITEA_SHELL, GITEA_SERVICES
    Grafana: GRAFANA_SHELL, GRAFANA_SERVICES
     Influx: influx, INFLUX_SHELL, INFLUX_SERVICES
  Mosquitto: MOSQUITTO_SHELL, MOSQUITTO_SERVICES
    NodeRed: NODERED_SHELL, NODERED_SERVICES, NODERED_DATA, DATABASES, FIRMWARE
     PiHole: PIHOLE_SHELL, PIHOLE_SERVICES
     Docker: DPS {<container> …}, DNET {<container> …}, IOTSTACK
             UP {<container>}, BUILD {<container>}, REBUILD <container>
             RECREATE <container>, RESTART <container>, TERMINATE <container>
             DOWN, PULL
```

If you get any error messages, go back and check your work. 

### Step 3

To apply `dot_iotstack_aliases` each time you login, add this *source* statement:

```
. "$HOME/.local/IOTstackAliases/dot_iotstack_aliases"
```

to one of the following files:

* ~/.bashrc
* ~/.profile

What's the difference? I'm glad you asked:

* `.profile` is called when you `ssh` into your Raspberry Pi but not when you open a terminal session in VNC.
* `.bashrc` is called when you open a terminal session in VNCbut not when you `ssh`.

You might be thinking, "I access my Raspberry Pi using both `ssh` and VNC so that means I should add the *source* statement to both files." Well, yes and no. There is a wrinkle. The `.profile` that you get by default already sources `.bashrc`, like this:

```
# if running bash
if [ -n "$BASH_VERSION" ]; then
   # include .bashrc if it exists
   if [ -f "$HOME/.bashrc" ]; then
      . "$HOME/.bashrc"
   fi
fi
```

If you have **not** removed those lines from `.profile` then `.bashrc` is the correct place to add the source statement to apply `dot_iotstack_aliases`. If you **have** removed those lines from `.profile` then you may need to add the source statement to both files.

Here's an example of editing `.bashrc` using `vi`:

```
$ vi ~/.bashrc
G
o
. "$HOME/.local/IOTstackAliases/dot_iotstack_aliases"
[ESC]
:wq
$
```

In words:

1. Launch "vi" with the target file.
2. Press [SHIFT] and then press "G" to jump to the end of the file.
3. Press "o" to open a new line after the last line in the file, and go into insert mode.
4. Type the source statement as-is and press [RETURN] at the end of the line. Make sure you pay attention to these key points:

	- The line starts with a period (`.`) followed by a space;
	- There are **no** other spaces on the line; and
	- Make sure you match the spelling **exactly**.Unix is case-sensitive.

5. Press [ESC] to exit insert mode.
6. Press ":" to move to command mode, "w" to write the in-memory buffer to the target file, and "q" to leave "vi".

Use the `tail` command to confirm your editing:

```
$ tail -1 ~/.bashrc 
```

### Step 4

Test your work. Do **NOT** log-out of your existing terminal session. Instead, open a new terminal session.

> How you do this will depend on how you connect to your RPi. If you are using `ssh` then start a new `ssh` session from your host computer. If you are using VNC then launch a new terminal session from the GUI. Ditto if you have gone "all the way" and have connected a keyboard, mouse and screen to your RPi.

Worst case is that you will be unable to login to the new terminal session. This is why you should never log-out of an existing terminal session until you are 100% certain that changes made to either `.bashrc` or `.profile` are working. If you can't launch a new session, you just use the existing session to track down and nail any bugs. Rinse, repeat.

> This advice goes double if you ever need to edit `/etc/profile` !!

In the **new** terminal session, you should expect to see the same list of aliases shown in [Step 2](#step2).

## Using the aliases

I create most of my aliases using all-caps names. I think this makes it easier for auto-completion to do its work but, it's your system, so change it if you don't like it.

### General-purpose aliases and shell functions

The general-purpose aliases (and functions) are the ones listed under the "Docker" heading above.

* DPS (`docker ps`) gets you a short-form of what is running, with the advantage that the information for each container usually fits on one line. Optional arguments limit the display to named containers. 
* DNET (also `docker ps`) but with a different list of columns, focusing on the networking aspects (ports etc). Optional arguments limit the display to named containers.
* IOTSTACK is `cd ~/IOTstack` and, on my system, just typing "IO" and hitting TAB auto-completes.
* UP (`docker-compose up -d`) by itself brings up your entire stack; UP with a container name brings up just that container. Its key advantage is that it works from anywhere. You don't have to `cd ~/IOTstack` first.
* BUILD (`docker-compose up -d --build`) just adds the `--build` flag to cause containers that are built with a `Dockerfile` to be re-built. Like `UP` this can either be whole-of-stack or just a named container, and runs from anywhere.
* REBUILD (`docker-compose build --no-cache --pull`) is a more powerful form of BUILD. It requires a named container as an argument. It forces the download of any newer base image from DockerHub **and** forces the rebuild of the local image by applying the `Dockerfile`.
* RESTART (`docker-compose restart`) requires a named container as an argument and sends a signal to the container to restart itself. It's closer to restarting the process inside the container than tearing down and reconstructing the container.
* RECREATE is a function and requires a named container as an argument. Unlike RESTART, this does a full tear down and reconstruction of the container. It guarantees that what is running inside the container is the same as the image.
* TERMINATE is also a function and requires a named container as an argument. It differs from RECREATE in that it only does a proper tear down of the container.
* DOWN (`docker-compose down`) takes down your entire stack. The `down` command can't take a named container as an argument, which is why TERMINATE needs to exist.
* PULL (`docker-compose pull` followed by `docker images`). The `pull` tells `docker-compose` to check for more-recent images on DockerBub and download any newer images:
	* a PULL followed by an UP will update all containers which **don't** rely on Dockerfiles.
	* a PULL followed by a BUILD will update all containers, except Node-Red (which needs special handling, see REBUILD).

	Generally, any action (eg PULL, BUILD, REBUILD) that results in a new image should be followed by a `docker system prune` to clean up any obsolete images.   

### Container-specific aliases

In most cases, a named container will have a pair of aliases, ending with:

* `_SERVICES`
* `_SHELL`

I have only provided aliases for the IOTstack containers that are installed on my system. That's because those are the only ones I can **test**. If you want to support other containers, either:

* customise `dot_iotstack_aliases` (which might cause merge conflicts when you synchronise your local repository with GitHub); or
* create your own alias file in parallel (eg `dot_my_iotstack_aliases`) and hook it up the same way.

As you think about these aliases, remember that auto-completion is your friend. For example, holding down [SHIFT] key and pressing `IN` followed by [TAB] should auto-complete to `INFLUX_S`. Pressing [TAB] twice will provide the suggestions:

```
INFLUX_SERVICES  INFLUX_SHELL
```

so pressing E then another [TAB] will auto-complete to `INFLUX_SERVICES`, and then you can press [RETURN] to execute the command.

#### «container»\_SERVICES alias

This jumps to the appropriate services directory. For example:

```
$ INFLUX_SERVICES
```

expands to:

```
$ cd ~/IOTstack/services/influxdb
$ ls
```

The logic is that, if you need to go into a container's services directory, you are likely intending to manipulate one of the files so an automatic `ls` is helpful.

#### «container»\_SHELL alias

This opens a shell within the container. For example:

```
$ INFLUX_SHELL
```

expands to:

```
$ docker exec -it influxdb bash
```

Opening a shell within a container means that the next thing you see is a prompt from the shell running inside the container, and with the **container's** view of the file system. `INFLUX_SHELL` will result in a prompt like this:

```
root@95b20550cb8a:/#
```

where the "#" indicates "you are running as root" (which is another way of saying "you don't need to use `sudo` for anything"). To get out of a container shell, either press Control+D or type `exit` and press RETURN.

Another benefit of using an alias scheme for common tasks is that you don't have to remember things like "most containers use `bash` but some, like Mosquitto, don't have `bash` so you need to use `ash` or `sh`, while others, like Portainer and Portainer-CE don't have any shell at all.

Notes:

* If you are trying to develop a "«container»\_SHELL" alias of your own and find that `bash` doesn't work, try replacing `bash` with `ash`, and then `sh`.

* If you're wondering about the `-it` option on «container»\_SHELL aliases, it means "interactive terminal". It's how `docker exec` knows to wait for human interaction, and why you have to type `exit` or press Control+D to get out of a container.

#### special cases

##### "influx"

The `influx` alias is an exception to my all-caps naming convention. It expands to:

```
$ docker exec -it influxdb influx -precision=rfc3339
```

It has the same effect as:

```
$ docker exec -it influxdb bash
# influx -precision=rfc3339
```

Or, in words:

1. Open a shell into the influxdb container, then
2. From inside the influxdb container, execute the `influx` command.

The `influx` command is InfluxDB's Command Line Interface.

The `-precision=rfc3339` option tells `influx` to display timestamps in human-readable form rather than nanoseconds since 1/1/1970. The timestamps are in UTC but you can tell `influx` to convert them to your local time by adding a `tz()` function to queries, like this:

```
SELECT * from «measurement» WHERE «criteria» tz('Australia/Sydney') 
```

You can append any other arguments supported by the `influx` CLI to the `influx` alias and the fact that `docker exec` is involved becomes entirely transparent. For example, all of these are valid:

```
$ influx
$ influx -database=«database name»
$ influx -type=flux
$ influx -database=«database name» -type=flux
```

##### "NODERED_DATA"

This alias expands to:

```
$ cd ~/IOTstack/volumes/nodered/data
$ ls
```

I often have the need to go into Node-Red's persistent data store to see what's what and you may need to do that as well.
