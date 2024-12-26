# Multiplayer First Person Shooter Basics
<title>Multiplayer First Person Shooter Basics</title>

## There can be two architecture to implement a multiplayer system for first person shooter games
 - Peer to Peer architecture
 - Server Client Architecture

I will be discussing Server Client architecture as this is moslty used in the industry and is cheating proof to very much extent.

## Server Client Architecture

in this architecture are two components

    - A main server (Coordinator)
    - Clients (Multiple players)

### Server
This is the place where actual game world will be running and all the other clients will have the follow the game running here, the clients will only recieve the snapshot of what happening there.

### Clients
Each player will be a client and this client will be running of the players machine and from here they will be providing inputs for their character which they are controlling on the server.<br>
After some periodic intervals they will be reciving the snapshots of what is happening in the server game world and will show this to player playing the game.


### Issues
In this architecture we will be encountering some issues due to network lag and input lag(the time between player pressing the button and the player cpu acknowledging it).<br>
suppose a player presses the button to move forward this button command will go to the client and the then client will send it to the server and then the server will process it and then the server will send the game snapshot back to the client and then the client will show it to the player in this whole process there will be a visible time difference. `How to eliminate it??`
well we have the answer to that the algorithm name is `Client-Side-Prediction`.<br>
As the name suggest the client will be predictiing some movements i will discuss more about this in my other blogs.<br>
Another problem is regarding a player seeing other players, because each player will have different lag in there network. so there will some incosistencies on the movement of the players as seen by each other to fix this we have another algorithm `Entity Interpolation`<br>
This algorithm will try to interpolate the other players states to give a consistent movement on the clients side.<br>
Another problem will arise due to the players shooting each other, because due to network lag each players will be seeing each other at different positions and this may cause confusion among the players. and who shoots first and in middle of all this some players can cheat, the answer to all this is a algorithm called `lag compensation`.

#### The three major algorithm you need to make a multiplayer FPS game is 
- [Client Side Prediction](./clientsideprediction.md)
- Entity Interpolation
- Lag Compensation
I will be discussion all this in the upcomming blogs



