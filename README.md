Matrix IRC Application Service
------------------------------
This is a Node.js IRC bridge for Matrix, using the Application Services (AS) API.

What does it do?
----------------
This bridges IRC channels into Matrix, allowing IRC users to communicate with
Matrix users and vice versa. The application service creates 'virtual' IRC clients for real Matrix
users. It also creates 'virtual' Matrix users for real IRC clients. It currently has support for:
 - Bridging specific (specified in the config) IRC channels and specific Matrix rooms.
 - Bridging of private conversations, which can be initiated both from IRC (as PMs) and from 
   Matrix (as invites to virtual Matrix users).
 - Dynamically bridging *any IRC channels on a network* to a specially crafted room aliases 
   (e.g. Joining the room ``#irc.example.com_#python:homeserver.com`` would join the channel
   ``#python`` on ``irc.example.com`` even if this isn't specified in the config.)

Quick Start
-----------
- ``git clone`` this repository.
- Run ``npm install``.
- Install mongodb (e.g. ``apt-get install mongodb``)
- Make a ``data`` folder to dump database files into (this will be ``.gitignore``d).
- Run a mongodb instance: ``mongod --dbpath=./data --port 27017``
- Optional: Run the tests by running ``npm test``.
- Copy ``config.sample.yaml`` to ``config.yaml`` and configure it for your IRC server / home server.
- Run it using ``node app.js``.

Configuration
-------------
``` .yaml
ircService:  # configuration for the IRC service
  # The mongodb database URI to connect to.
  databaseUri: "mongodb://localhost:27017/matrix-appservice-irc"
  servers:
    # the address of the server to connect to. You can have more than one.
    irc.example.com:  
      # the nickname of the AS bot which listens in on rooms
      nick: "appservicebot"  
      expose:
        # allow people to dynamically join other channels not on the list of 'mappings' below.
        channels: true
        # pass PMs to virtual IRC clients to their real Matrix counterparts
        privateMessages: true
      # Optional. The user ID prefix for virtual matrix users. Defaults to "@<SERVER_ADDR>_" e.g.
      # @irc.example.com_
      userPrefix: "@irc_"
      rooms:
        # Optional. The room alias prefix when joining channels dynamically by alias. Defaults to
        # "#<SERVER_ADDR>_" e.g. #irc.example.com_
        aliasPrefix: "#irc_"
        mappings:
          # 1:many mappings from IRC channels to room IDs on this IRC server.
          "#mychannel": ["!kieouiJuedJoxtVdaG:localhost"]
   logging:
     # Level to log on console/logfile. One of error|warn|info|debug
     level: "debug"
     # The file location to log to.
     logfile: "debug.log"
     # The file location to log errors to.
     errfile: "errors.log"
     # Whether to log to the console or not.
     toConsole: true
     # The max size each file can get to in bytes before a new file is created.
     maxFileSizeBytes: 134217728
     # The max number of files to keep. Files will be overwritten eventually due to rotations.
     maxFiles: 5

appService:
  # The URL to the home server for client-server API calls
  hs: "http://localhost:8008"
  # The 'domain' part for user IDs
  hsDomain: "localhost"
  # The application service token set for this home server
  token: "1234567890"
  # The webhook URL for the home server to hit on new events
  as: "http://localhost:3500"
  # The port to listen on.
  port: 3500
```
