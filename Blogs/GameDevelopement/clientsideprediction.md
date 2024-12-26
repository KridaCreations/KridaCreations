# Client Side Prediction

in the [basics of multiplayer first person shooter](./basicsfps.md) blog, i told you about the lag which the player will feel due to time difference between pressing a button and movement of his player after server acknowledgemen. I will discuss this issue and solution in more detail here.

## Issue
For now i will assume there is server and only one player is connected to it and the player want to move his character forward
what are the events which need to happen to move his character forward.

- player presses the "forward" button
- the client application `(game running on the player machine)` recieves the "forward" buttton press 
- client application send this to the server 
- the server recieves the "forward" button press
- the server moves the player character in the game world running on the server.
- server send back the acnowledgement back.
- server then sends a snapshot of the game world running on the server after some interval
- client machine recieves it and shows to the player that his character moves.

As you might have guessed, in this process lot of steps are involved and surely there will be network lag and the player will feel a visible time lag between his button press and the character movement.

So how to eliminate this issue?? we have the answer as `Client Side Prediction`

## Algorithm explanation
So for now in our architecture the game world was running on the server machine and players were reciving the snapshot of the the world running there and interpolating between the snapshots to see the gameplay on there machine, As the server is running the game world so the server is the one which is simulating the movement of the players.<br>

But to fix our issue will be simulating player movement on the client application also,this is where the `prediction` comes in, we will be predicting the character movement untill a acnowledgement comes from the server,if our movement is correct will be ignore the acknowledgement and continue, but if our character state is wrong we will simulate all the inputs from when the server acknowledgemnt starts to current input, Okay Okay this may have confused you a lot but i will explain in more detail.
First some points to keep in mind.

- For now only our player is connected to the server no other player.
- there are no packet loss in communication(this can be achieved by certain algorithm which you game engine might have already implemented).

Now i will start the detailed explanation

we will be capturing inputs 60 times every second (60Hz - standard physics frame rate) and sending the keyboard state to the server , for example if our players can move in four direction:- forward , backward , left and right, and the button presses needed to move in the directions are "W", "S", "A" and "D" respectively. then the  keyboard state which we will be sending each frame(each snapshot) will look like this 

```json
{
    "F":"<frameno>", //the serial no of the frame helps in tracking when the the acknowledgement of the frame is recieved
    "input":{
        "forward":"<ispressed>", //ispressed will be true if "W" button is pressed otherwise false
        "backward":"<ispressed>", //ispressed will be true if "S" button is pressed otherwise false
        "left":"<ispressed>", //ispressed will be true if "A" button is pressed otherwise false
        "right":"<ispressed>", //ispressed will be true if "D" button is pressed otherwise false
    }
}
```

**The client application will be sending this frame to the server and running on it's machine also, and saving the this input state in an `input array`, Also the client will run this input frame on the client application and move the player character accordingly and the save the current state of character(character state in our case is the location coordinates) which we get after running the input state in an `output array`.**

as the player character can move in forward, backward, left and right direction the charater state will look like this :-

```json
{
    "F":"<frameno>", //the serial no of the frame which output is store in the field output
    "output":{
        "X":"<x-coordinate>", //x coordinate after processing keyboard state input of frameno "F"
        "Y":"<y-corrdinate>", //Y coordinate after processing keyboard state input of frameno "F"
    }
}
```

the input array and output array will look like this
|Input array|Output array|
|:------------:|:------------:|
|.|.|
|.|.|
|{"F": "5","input": "inputstate at frame 5"}|{"F":"5","output":"outputstate after frame 5}|
|{"F": "6","input": "inputstate at frame 6"}|{"F":"6","output":"outputstate after frame 6}|
|{"F": "7","input": "inputstate at frame 7"}|{"F":"7","output":"outputstate after frame 7}|
|{"F": "8","input": "inputstate at frame 8"}|{"F":"8","output":"outputstate after frame 8}|
|{"F": "9","input": "inputstate at frame 9"}|{"F":"9","output":"outputstate after frame 9}|
|.|.|
|.|.|


When server will recieve the input state frame, it will run the input state and move the character player according to the output, and then send the output state back to the client, when the client will recieve the output state from server some frames have already passed on the client application. Suppose the output state recieved from the **server** is for **`"Frame 6"`** but client application is running **`"Frame 12"`**, So the client will search the output state of the **`Frame 6`** in the `Output array` and compare the `Frame 6 Server character state` and `Frame 6 Client character state`, this is called `Reconciliation`.For comparision we need to calculate the difference between the states, for this case iam considering difference between the states as the distance between the `"Frame 6 server state coordinate"` and `"Frame 6 client state coordinate"` <br>

Now, There can be two cases :- 

- If the `difference of the Frame 6 states` between Server and Client  is greater than the `Threshold` (Threshold will be some arbitrary distance let's say 0.5 units), then if means the difference between the states is too much and the client state needs to be corrected **(character state on the client side is too much deviated from the sever side character state, this difference can be due to some physics calculation, `due to cheating on the client side` or somtimes due to to much lag in the network )**. **the client will be moved back to the server state** (you might have sometimes seen you character moving back again and again when playing PUBG, Call of duty or any other first person shooter this is same thing happening there) and all the inputs from `Frame 7` to `Frame 12` will be processed again(because the client state state right now is set to the output state which should be after processing `Frame 6`), the input state of this frames can be found in the input array. `Now we can remove all the entries from input array and output array which are before "Frame 6"` because the output state of the "Frame 6" is verified.

- If the `difference of the Frame 6 states` between Server and Client is smaller than the `Threshold`then it we can say that `server state of Frame 6` and `client state of Frame 6`is not much different and do not need correction and we will simply  remove all the entries from input array and output array which are before "Frame 6" because the output state of the "Frame 6" is verified.

In this way the gameplay will keep on going....

That was pretty much all the details of Client prediction algorithm. 

