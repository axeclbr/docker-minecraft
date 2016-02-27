# Docker-Minecraft

Personal project for running my own Minecraft servers.

## Usage

### Start the container

To just test the container and play a little while in a non-persistend world, just type

    docker run --rm -it \
    --net=host \
    axeclbr/minecraft

and connect to your server-ip on port 25565 with your Minecraft client. After you stop the container your world will be gone.

### Persist settings and world data

If you want to save your settings and Minecraft world, you just can create a data container which persists all your Minecraft stuff:

Create an empty data container to store your Minecraft settings and world data in:

    $ docker create -v /data --name mc_data axeclbr/data /bin/true

To let Minecraft create all necessary setting-files run the Minecraft container one time and kill it after complete startup

    $ docker run --rm -it --volumes-from mc_data axeclbr/minecraft

You should see some log output. If you see output like this and no more action, then it is time to kill your Minecraft container:

    [08:07:50] [Server thread/INFO]: Preparing level "world"
    [08:07:50] [Server thread/INFO]: Preparing start region for level 0
    [08:07:51] [Server thread/INFO]: Preparing spawn area: 6%
    [08:07:52] [Server thread/INFO]: Preparing spawn area: 11%
    [08:07:53] [Server thread/INFO]: Preparing spawn area: 18%
    [08:07:55] [Server thread/INFO]: Preparing spawn area: 26%
    [08:07:56] [Server thread/INFO]: Preparing spawn area: 36%
    [08:07:57] [Server thread/INFO]: Preparing spawn area: 47%
    [08:07:58] [Server thread/INFO]: Preparing spawn area: 55%
    [08:07:59] [Server thread/INFO]: Preparing spawn area: 67%
    [08:08:00] [Server thread/INFO]: Preparing spawn area: 79%
    [08:08:01] [Server thread/INFO]: Preparing spawn area: 92%
    [08:08:01] [Server thread/INFO]: Done (11.059s)! For help, type "help" or "?"

To kill the Minecraft container just press **Ctrl+C**.

Now all setting-files with minecraft standard settings are created and stored in your data container "mc_data". If you want to customize some settings just connect to it with

    $ docker run --rm -it --volumes-from mc_data axeclbr/workbench

The minecraft setting files are found with

    $ cd /data
    $ ls -l
    total 36
    -rw-r--r--    1 root     root             2 Feb 27 08:07 banned-ips.json
    -rw-r--r--    1 root     root             2 Feb 27 08:07 banned-players.json
    -rw-r--r--    1 root     root            10 Feb 27 08:07 eula.txt
    drwxr-xr-x    2 root     root          4096 Feb 27 08:07 logs
    -rw-r--r--    1 root     root             2 Feb 27 08:07 ops.json
    -rw-r--r--    1 root     root           755 Feb 27 08:08 server.properties
    -rw-r--r--    1 root     root             2 Feb 27 08:07 usercache.json
    -rw-r--r--    1 root     root             2 Feb 27 08:07 whitelist.json
    drwxr-xr-x    7 root     root          4096 Feb 27 08:10 world


Once you customized all your settings you should start a new Minecraft container. It should read and write all your valuable data to the data container with "--volumes-from mc_data". Additionaly it should expose it's Port to the host system to ensure that it is available from the outside with "--net host":

    docker run --rm -it \
    --net=host \
    --volumes-from mc_data \
    axeclbr/minecraft

If you want to give Minecraft more RAM, you add just one line and start it like this:

    docker run --rm -it \
    --net=host \
    --volumes-from mc_data \
    axeclbr/minecraft \
    java -Xmx1024M -Xms1024M -jar /minecraft_server.jar


That's it. Now your Minecraft service in Docker is ready to play!

