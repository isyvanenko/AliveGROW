![alt](../Graveyard_Bloom/DevLog/Media/logo%20(name).png)
# Development journal "Graveyard bloom"

FGCT5016: Technical Art 

Ihor Syvanenko

2308083

## Project Overview

####  **Game idea**

The theme for this project was Alive, the first thing that comes to mind when I think about Alive is plants. After a couple of days thinking about the game idea I decided to make a game where you play as a ghost who need to take care of plants to progress throw the level.

####  **Core mechanics**

* Player movement ghost like, 
* picking up/dropping tools, 
* interacting with plants, 
* timer, 
* picking up extra time.

###  **Planning**

**Player movement -> Tools pickup/drop -> Enviroment -> Timer -> Picking up extra time -> Interacting with Plants -> UI -> Polish**

I would look for free assets that I could implement in to my game from Fab (https://www.fab.com/), Sketchfab (https://sketchfab.com/) and I would make my own sounds and UI sprites in Photoshop and Procreate.

Counting that I didn't have much programming experience in Unreal Engine I would use ChatGPT (https://chatgpt.com/) and Google Gemini (gemini.google.com).

## Implementation 

###  **Movement**
For this project I decided to go with the first person template since, ghost don't have arms and body I set the arms of the template as **not visiable** and to better understand what I can change in the player movement componwent I looked in to the Unreal Engine documentation. Character Movement Component | Unreal Engine 4.27 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/networking-and-multiplayer-in-unreal-engine  

Here I was able to learn that I can modify the walk speed, velocity, gravity to make my character apear as they are floating. Here is a short video showcasing the movement:

https://youtu.be/he965XDY6oM **<- watch this**

---

###  **Tools implementation**

So I started from searching 3D assets on Sketchfab and I found 3 great 3D models that I dowloaded. I had to modify the the watering can model in blender as well as shears to make sure they're sharing the same material. 

Than I started to think how can I achiave the machanic that I had in my mind where player would: **aprouch a tool -> pick it up -> aprouch another item -> drop the current -> pick up the new one**

Here is where I asked AI for help. Where Gimini was not able to give me the right directions Chat GPT understood my intension and help me create the base function. 

First, I created a parent class **BP_GardeningToll** that can keep all the shared concpets for all of the tools. Than I created child blueprints of **BP_WateringCan**, **BP_Shovel**, and **BP_Shears**. How I imagine the system to work is that tools have a collider that would activate a function that checks if player is holding a tool if not then assign a new tool, but if player is holding a tool drop the current one and assign a new one. 

I had to create a couple of variables in player controller such as **CurrentTool** and **LastToolLocation**. I also created 2 custom events such as **PickUpTool** and **DropTool**.

        PickUpTool -> Branch (is Current Tool valid?) -> TRUE  -> DropTool -> Attach Actor to component -> Set Reletive Location -> Set Current tool
                             (is Current Tool Valid?) -> FALSE -> Attach Actor to component -> Set Reletive Location -> Set Current tool


    DropTool -> Detach from the Actor (current tool) -> Set Current tool 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/wvyg295q/" scrolling="no" allowfullscreen></iframe>

When that was done, I had to find the way to call this function, and the best way to do it is to call it from tools directly. So, what I did is that I added a sphere collider to the **BP_GardeningTools** that will be shared to all tools. And now I can do the function that would check if player is a apriximetry and if they are call **PickUpTool** function. 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/uxp7_wa8/" scrolling="no" allowfullscreen></iframe>

And will all that polishing that testing I had my core mechanic ready. And here is the video showing the pick up/drop off:

https://youtu.be/Xvn72N7d1dQ **<- watch this** 

___

###  **Enviroment + Postprocesing **

So, when core mechanic was done I moved to search and create the enviroment for my game. Since, this unit dosent reqier modelling skills I went on FAB to search for 3D assets. I was looking for free graveyard assets that would be stylized and would fit the atmosphere of my game. I created a couple of AI generated images in **photoshop** for insperation than i found free assets that were a close match to what i was looking for. Stylized Fantasy Skymap Environment - Modular Open world Skymap (s.d.) At: https://www.fab.com/listings/b59af281-5a81-4846-acab-0a303f71c74b (Accessed  02/12/2024).

Then I started to build the level from the assets that were presentented in this assets pack. 

https://youtu.be/Y_0fJxKiv1o **<- watch this** 

I also worked on the PostProcesing for this level. I added the film gain, bloom, fuild of focus, colour grading etc. to give my game a spooky feel.
___

###  **Timer + Extra Time pick ups**

So, I created a **BP_Timer** actor that will controll time decrese as well as I will be able to set custom time for every level. Also, **BP_Timer** will controll what would happen if player runs out of time. To display the time I will need to create a widget **W_Timer** The widget will get the current time value and display the time to the player. 

So, for **BP_Timer** I created a float variable **GameTime** which i exposed on spawn and I also made it instance editabel. Here is BP_Timer:

         Event Tick ->  *Function Node -> Branch (GameTime <= 0)? -> TRUE -> Open Level (WIN)

         *Function Node (DecreseGameTime) -> Delta seconds - GameTime -> Set GameTime 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/sz0izs14/" scrolling="no" allowfullscreen></iframe>

When I did the playtesting and was happy with the result i started to work on the widget **W_Timer** I looked only for free fonts that i can use in my game and I've choosen this ghotic style one. New Rocker (s.d.) At: https://fonts.google.com/specimen/New+Rocker (Accessed  01/11/2024). Then I added a canvas and a text box in the middle top with text that is binded to **BP_Timer**

            Get Text -> Return 
            as BP TIMER -> GameTime : 60 -> :60 -> From Hours -> As Timespawn 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/qt6bjy-m/" scrolling="no" allowfullscreen></iframe>

When that was done I started to work on the extra time pick up actors. I created a **BP_ExtraTime5s**, in this actor blueprint I added a static mesh that I got from the Stylized Fantasy Skymap Environment - Modular Open world Skymap assets pack that would represent a *"crystal of time"*, I also added a PickUpSoundCue and ShowUpSound Cue (this sounds were recorded and edited by me) I also added the *Sphere collider* which will play the most important part in this actor. How pick up time works is that I Created 4 functions that would be triggered when player collids with the sphere collider component of **BP_ExtraTime5s**. 

* **AddTime** -> Get Actor of Class **BP_Timer** -> As BP_Timer **ADD time** (*time to add*) This function will comminicate will BP_Timer and will add extra time to it. (update *current time*)

* **Enable/Disable Extra Time** -> Disable collision *(sphere collision component)* -> Set Actor Hidden in Game *(self)*. This function will handle the respawn function of the crystal as I want the crystals to respawn after a short period of time. 

* **Visual Rep Extra Time** -> Play (pickUpSound) -> Spawn System *(PickUp Particle)* at Location *(self actor location)*. This function will play a pick up sound and instatiate a particle effect at the location of the extra pick up actor 

* **UIupdate** -> Create **W_ExtraTim Widget** -> Add to viewport -> as **W_ExtraTim** -> **Notifyplayer ExtraTime**. This function will create a W_ExtraTim widget which holds a widget animation and calls the Notifyplayer ExtraTime event to play this animation. 

On this stage I also decided to add a crosshair to make sure my player can see where the middle of the screen is when they will start intracting with an plants. I got the crosshair png from here: Flower star icon, SVG and PNG | Game-icons.net (s.d.) At: https://game-icons.net/1x1/delapouite/flower-star.html (Accessed  01/11/2024).

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/90q1-hzq/" scrolling="no" allowfullscreen></iframe>


Video showcase:

https://youtu.be/p7ATVMGFMz4 **<- watch this** 

###  **Timer + Extra Time pick ups**



                           











Assets:

Hedge shears from Sketchfab - The best 3D viewer on the web (2020) At:https://sketchfab.com/3d-models/hedge-shears-0019c537f5884ce08907a07e72bd3b64https://sketchfab.com/3d-models/hedge-shears-0019c537f5884ce08907a07e72bd3b64 (Accessed  01/11/2024).

Stylized shovel from Sketchfab - The best 3D viewer on the web (2020) At:https://sketchfab.com/3d-models/stylized-shovel-3e409779cb3d416b9323c1cf52665308 (Accessed  01/11/2024).

Watering can from Sketchfab - The best 3D viewer on the web (2020) At:https://sketchfab.com/3d-models/watering-can-fe9879c5e6884adbb111b7e7dd88df7d(Accessed  01/11/2024).

Stylized Fantasy Skymap Environment - Modular Open world Skymap (s.d.) At: https://www.fab.com/listings/b59af281-5a81-4846-acab-0a303f71c74b (Accessed  01/11/2024).

New Rocker (s.d.) At: https://fonts.google.com/specimen/New+Rocker (Accessed  01/11/2024).

Flower star icon, SVG and PNG | Game-icons.net (s.d.) At: https://game-icons.net/1x1/delapouite/flower-star.html (Accessed  01/11/2024).







https://skolaztika.itch.io/dark-elegance-renpy-gui