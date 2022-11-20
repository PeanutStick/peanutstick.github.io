---
layout: programing
title: Create an IRC bot with python
date: 2021-06-21 21:25:10
tags: IRC Python
categories: Programing
---
# Create an IRC bot with Python3

## Installation of python3.

```
apt update
apt upgrade
apt install python3
```

## Create the project and start to code.

You have to create a file like "main.py" in a directory where will be your bot.

### Import some necessary libraries.

In main.py.

```python
import socket, ssl, threading
import score_board as score
```

### Add the configuration variables.

Don't forget to comment the code, it's  important.

```python
server = 'irc.evilcorp.ga' # Server
port = 6697 # Port
channel = "#lobby" # Channel
botnick = "Dovahkiin" # Your bots nick
password = ""
user = botnick + " " + botnick + " " + botnick + " " + "HODL the line apes!" # This is username, hostname, identity and description in the order
```

### Connect to the server

```python
socketHandler = socket.socket(socket.AF_INET, socket.SOCK_STREAM)# Opening up a normal socket.

socketHandler.connect((server,port))# Binding socket to socket address(server ip and port).

ircsock = ssl.wrap_socket(socketHandler)# Wraping the socket with ssl.

ircbuff = ircsock.recv(2048).decode('utf-8') # Receive buffer from the server.
        
ircbuff = ircbuff.strip('\r\n') # Removing any unnecessary linebreaks.
print(ircbuff)	# Printing buffer for once.
del ircbuff	# Deleting buffer for re-use.

ircsend("NICK "+ botnick +"\r\n") # Here we actually assign the nick to the bot
print("Sending Nick " + botnick + " to server")

ircsend("USER "+ user + "\r\n") # User authentication
print("Sending UserName and hostname and identity and description \"" + user + "\" to server")
```

### Create the loop to receive the buffer 

```python
while 1: # Be careful with these! it might send you to an infinite loop
    ircbuff = ircsock.recv(2048).decode('utf-8') # Receive buffer from the server.
    ircbuff = ircbuff.strip('\r\n') # Removing any unnecessary linebreaks.
    print(ircbuff)	# Here we print the buffer from the server.
    if ircbuff.find("PING :") != -1: # If the server pings us then we've got to respond!
        ping(ircbuff)
    elif ircbuff.find("NOTICE " + botnick + " ::This nickname is registered") != -1:
        ircsend("/msg NickServ IDENTIFY "+password)
                
        
    elif ircbuff.find("NOTICE " + botnick + " :*** You are connected to") != -1: # If connected successfully, do the below functions and codes.
        joinchan(channel) # Join the channel using the functions we previously defined
```

And your code will be just after that with an another elif, like this:

```python
    elif ircbuff.find("coffee") != -1: # Bring you a coffee
        coffee()
```

And we delete the buffer because the script will process the previous messages and send too much messages to the server.

```python
del ircbuff
```

now we have to create the function: 

- ping for ping(ircbuff)
- ircsend for ircsend("/msg NickServ IDENTIFY "+password) 
- joinchan for joinchan(channel)
- coffee for coffee()

### Create the functions

```python
def ircsend(msg): # I had to make another function for send() because in python3 and above the socket incoming and outgoing messages are in bytes format. So you have to encode and decode it accordingly.
        ircsock.send(msg.encode('utf-8'))

def ping(ircbuff): # This is our first function! It will respond to server Pings.
        pongReply = ircbuff.split(':',1)[-1]	# This strips out ping reply and server name for pong. If not defined it can cause the bot to break in some servers
        ircsend("PONG :" + pongReply + '\r\n')

def joinchan(chan): # This function is used to join channels.
        ircsend("JOIN "+ chan +"\r\n")
def coffee(): # This function responds to a user that inputs "Hello Mybot"
        ircsend("PRIVMSG "+ channel +" :  ,--.\r\n")
        ircsend("PRIVMSG "+ channel +" : C|:p| Here is your coffee !\r\n")
        ircsend("PRIVMSG "+ channel +" :  `=='\r\n")
```

### Thanks

Thank to Shadow Lobster for the help.
