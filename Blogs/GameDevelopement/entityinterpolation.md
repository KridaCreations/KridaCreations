# Entity Interpolation 

In the last `Client side prediction` blog i discussed about the how to sync the player's own character with the same the character on the server.

Now obviously single player will not be playing multiplayer games, So the player need to see his enemies or teammates,
one naive way is to continuously broadcast the movement made by each player every other player connnected to the server but due to network delay this is not a very good idea, this is where `Entity-Interpolation` comes in. Before diving in the algorithm let me fix some points:-

- There are three player playing the Game `Player A`, `Player B` and `Player C`.
- We will be disucssing the algorithm from the perspective of `Player A`, means we will try to synchronize the movement of the other players(Player B and Player C) on the machine of Player A.
- Player A will be synchronised with **his character on server** through the `Client Side Prediction` algorithm.