# Razor C++ State Synchronization (Netcode) Library

Razor synchronizes the state of video game clients with a server over
the internet.

Features include:

* A light-weight, low latency, immediate-mode API for clients and servers
* Compiles on Windows, Linux, and OSX
* Compatible with any type of data state from FPS games, to strategy, to realtime business applications
* Engine-agnostic design that can be integrated into small and large projects
* Syncable datatypes including Vector3, Matrix44, Quaternion, and Arrays
* Syncable datastructs that encapsulate groups of data
* Automatic multipart packet assembly and disassembly

# Razor's Objective

The main problem that is difficult to solve in network state
synchronization is how to account for the race conditions induced by
the latency inherent in sending packets over the internet.

The second and related problem is after you have received new information
from a remote source that affects your local state, how to gracefully
update your state to account for the new information.

Razor's objective is to automate the process of keeping remote states
synchronized and helping to gracefully update the local state when 
new information arrives.

Additionally, Razor aims to make all this simple and free of byte
manipulation for applications using the library.

# What does Razor NOT do?

Most netcode is integrated deep into a game engine because it is difficult
to separate the intricate inner workings of netcode's state manipulation from
the actual data state.

Razor applies the principle of "bring your own state manipulation" so Razor's netcode
may be shared with many types of engines.

Razor assumes your application is capable of the following:

* Serializing a data state (helper data types are included in Razor)
* Deserializing a foreign data state and actualizing it into the current state (helpers included)
* Manipulating the application's current frame number
* Serialization and deserialization of commands

Additionally, you can increase the effectiveness of your application's synchronization
with the following additional features:

* Ability to rewind and fast-forward through time
* Ability to record and authorize commands

# Razor's Approach

Razor uses three separate channels of communication to orchestrate synchronization:

* Data States
* Commands
* Events

***Data States*** are raw serializations of the server's state that are periodically shared with
clients. Data States allow clients to absolutely synchronize with the server, except for
the latency the state took to get to the client. Because Data States are relatively large
and processor intensive, they are only shared occassionally and other more optimal approaches
are used to make up the difference. Razor will request your data state, send it over the
internet, and then request that your client load it, but it will be your responsibility
to have a strategy for latency.

***Commands*** are your application's notation for things that users do. Razor provides an 
automatic method of broadcasting them among the clients. Razor provides no guarantees
on delivery of user commands, or whether the same command is received multiple times.
It will be your responsibility to play them correctly in your application's logic, 
to ensure if there are lost packets that the commands are rebroadcast, and to ensure if
a duplicate command is received it is caught.

***Events*** are authoritative chronologically-recorded conclusions to application logic.
Events have a distinct difference from commands, because they are only generated by the server.
Razor guarantees delivery and exact synchronicity of events. It is your responsibility to
create and respond to them. Events are useful if you would like to ensure your 
chronographically-fluid application has certain things not be replayed multiple times. A 
good example is a death animation.

Additionally, Razor provides a suite of timing synchronization tools that will be available to
your application automatically. Razor will calculate the difference between local and remote
clocks, estimate the latency to the server, and provide you all the frame and timing data
necessary to build accurate states of the past that can be used to simulate the future
as part of an overall latency resolution strategy. Razor provides an example latency 
resolution strategy to model your application after.

# Compiling Razor

Dependencies:
* g++-10 or higher
* make
* pthread
* SDL2
* SDL2_net 
* curl

Build the static library (librazor.a):
```
make
```

Build the test executable:
```
make test
```

Run the test executable:
```
./razortest
```

# Using Razor in Your Project

Razor is designed to be used as a static library. To link against it,
add librazor.a into your project directory and add `./librazor.a` 
to your linking step next to your object files. Ensure
Razor's includes are in your project's includes directory.

API Documentation for razor is pending.