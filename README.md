![alt](../Graveyard_Bloom/DevLog/Media/logo%20(name).png)
# Development journal "Graveyard Bloom"

FGCT5016: Technical Art 

Ihor Syvanenko

2308083

## Project Overview

####  **Game idea**

The theme for this project was Alive, the first thing that comes to mind when I think about Alive is plants. After a couple of days of thinking about the game idea, I decided to make a game where you play as a ghost who needs to take care of plants to progress through the level.

####  **Core mechanics**

* Player movement ghost-like, 
* picking up/dropping tools, 
* interacting with plants, 
* timer, 
* picking up extra time.

###  **Planning**

**Player movement -> Tools pickup/drop -> Enviroment -> Timer -> Picking up extra time -> Interacting with Plants -> UI -> Polish**

I would look for free assets that I could implement into my game from Fab (https://www.fab.com/), and Sketchfab (https://sketchfab.com/) and I would make my sounds and UI sprites in Photoshop and Procreate.

Counting that I didn't have much programming experience in Unreal Engine I would use ChatGPT (https://chatgpt.com/) and Google Gemini (gemini.google.com).

## Implementation 

###  **Movement**
For this project I decided to go with the first-person template since ghosts don't have arms and bodies I set the arms of the template as **not visible** and to better understand what I can change in the player movement component I looked into the Unreal Engine documentation. Character Movement Component | Unreal Engine 4.27 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/networking-and-multiplayer-in-unreal-engine  

Here I was able to learn that I can modify the walk speed, velocity, and gravity to make my characters appear as if they are floating. Here is a short video showcasing the movement:

https://youtu.be/he965XDY6oM **<- watch this**

---

###  **Tools implementation**

So I started searching 3D assets on Sketchfab and I found 3 great 3D models that I downloaded. I had to modify the watering can model in a blender as well as the shears to make sure they were sharing the same material. 

Then I started to think how can I achieve the mechanic that I had in my mind where the player would: **approach a tool -> pick it up -> approach another item -> drop the current -> pick up the new one**

Here is where I asked AI for help. Where Gimini was not able to give me the right directions Chat GPT understood my intention and helped me create the base function. 

First, I created a parent class **BP_GardeningToll** that can keep all the shared concepts for all of the tools. Then I created child blueprints of **BP_WateringCan**, **BP_Shovel**, and **BP_Shears**. How I imagine the system to work is that tools have a collider that would activate a function that checks if the player is holding a tool if not then assign a new tool, but if the player is holding a tool drop the current one and assign a new one. 

I had to create a couple of variables in the player controller such as **CurrentTool** and **LastToolLocation**. I also created 2 custom events such as **PickUpTool** and **DropTool**.

        PickUpTool -> Branch (is Current Tool valid?) -> TRUE  -> DropTool -> Attach Actor to component -> Set Reletive Location -> Set Current tool
                             (is Current Tool Valid?) -> FALSE -> Attach Actor to component -> Set Reletive Location -> Set Current tool


    DropTool -> Detach from the Actor (current tool) -> Set Current tool 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/wvyg295q/" scrolling="no" allowfullscreen></iframe>

When that was done, I had to find a way to call this function, and the best way to do it is to call it from tools directly. So, what I did is I added a sphere collider to the **BP_GardeningTools** that will be shared with all tools. Now I can do the function that would check if the player is an apriximetry and if they are called the **PickUpTool** function. 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/uxp7_wa8/" scrolling="no" allowfullscreen></iframe>

And will all that polishing that testing I had my core mechanic ready. And here is the video showing the pickup/drop-off:

https://youtu.be/Xvn72N7d1dQ **<- watch this** 

___

###  **Enviroment + Postprocesing **

So, when the core mechanic was done I moved to search and create the environment for my game. Since this unit doesn't require modelling skills I went on FAB to search for 3D assets. I was looking for free graveyard assets that would be stylized and would fit the atmosphere of my game. I created a couple of AI-generated images in **photoshop** for inspiration then I found free assets that were a close match to what I was looking for. Stylized Fantasy Skymap Environment - Modular Open world Skymap (s.d.) At: https://www.fab.com/listings/b59af281-5a81-4846-acab-0a303f71c74b (Accessed  02/12/2024).

Then I started to build the level from the assets that were presented in this assets pack. 

https://youtu.be/Y_0fJxKiv1o **<- watch this** 

I also worked on the PostProcesing for this level. I added the film gain, bloom, fuild of focus, colour grading etc. to give my game a spooky feel.
___

###  **Timer + Extra Time pick ups**

So, I created a **BP_Timer** actor that will control time decrease as well and I will be able to set a custom time for every level. Also, **BP_Timer** will control what would happen if the player runs out of time. To display the time I will need to create a widget **W_Timer** The widget will get the current time value and display the time to the player. 

So, for **BP_Timer** I created a float variable **GameTime** which I exposed on spawn and I also made it instance editable. Here is BP_Timer:

         Event Tick ->  *Function Node -> Branch (GameTime <= 0)? -> TRUE -> Open Level (WIN)

         *Function Node (DecreseGameTime) -> Delta seconds - GameTime -> Set GameTime 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/sz0izs14/" scrolling="no" allowfullscreen></iframe>

When I did the playtesting and was happy with the result I started to work on the widget **W_Timer** I looked only for free fonts that I could use in my game and I've chose this gothic style one. New Rocker (s.d.) At: https://fonts.google.com/specimen/New+Rocker (Accessed  01/11/2024). Then I added a canvas and a text box in the middle top with text that is bound to **BP_Timer**

            Get Text -> Return 
            as BP TIMER -> GameTime : 60 -> :60 -> From Hours -> As Timespawn 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/qt6bjy-m/" scrolling="no" allowfullscreen></iframe>

When that was done I started to work on the extra time pick-up actors. I created a **BP_ExtraTime5s**, in this actor blueprint I added a static mesh that I got from the Stylized Fantasy Skymap Environment - Modular Open world Skymap assets pack that would represent a *"crystal of time"*, I also added a PickUpSoundCue and ShowUpSound Cue (these sounds were recorded and edited by me) I also added the *Sphere collider* which will play the most important part in this actor. How pick-up time works is that I Created 4 functions that would be triggered when the  player collides with the sphere collider component of **BP_ExtraTime5s**. 

* **AddTime** -> Get Actor of Class **BP_Timer** -> As BP_Timer **ADD time** (*time to add*) This function will comminicate will BP_Timer and will add extra time to it. (update *current time*)

* **Enable/Disable Extra Time** -> Disable collision *(sphere collision component)* -> Set Actor Hidden in Game *(self)*. This function will handle the respawn function of the crystal as I want the crystals to respawn after a short period. 

* **Visual Rep Extra Time** -> Play (pickUpSound) -> Spawn System *(PickUp Particle)* at Location *(self actor location)*. This function will play a pickup sound and instantiate a particle effect at the location of the extra pick-up actor 

* **UIupdate** -> Create **W_ExtraTim Widget** -> Add to viewport -> as **W_ExtraTim** -> **Notifyplayer ExtraTime**. This function will create a W_ExtraTim widget which holds a widget animation and calls the Notifyplayer ExtraTime event to play this animation. 

At this stage, I also decided to add a crosshair to make sure my players can see where the middle of the screen is when they start interacting with plants. I got the crosshair PNG from here: Flower star icon, SVG and PNG | Game-icons.net (s.d.) At: https://game-icons.net/1x1/delapouite/flower-star.html (Accessed  01/11/2024).

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/90q1-hzq/" scrolling="no" allowfullscreen></iframe>

Also, because players can fall I needed to implement a function that would teleport players back to spawn point and deduct some points from them. So I created **BP_DeadZone** which is an empty actor which has a box collider. What happens is

        On Box, collider begin overlap -> Player? -> Set Actor Transform (Player ) to New Transform -> Get Actor of class BP_Timer -> Remove Time -> Create W_PlayerFelt widget -> Add to Viewport -> Player Felt Anim

Simply saying what happens is that the player gets teleported to the assigned position and we remove time from the **BP_Timer** timer class to produce the **Remove Time** function and add the widget to notify the player about the lost time and play animation from the widget.

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/d95qaozm/" scrolling="no" allowfullscreen></iframe>

Video showcase:

https://youtu.be/p7ATVMGFMz4 **<- watch this** 

###  **Plants system**

When I did this with timer, environment and tools pickup. I started to work on Plants, as they are the key concept of the game. I looked for assets for my plants on sketfab and I found this amazing pack: Planets - A 3D model collection by Baxter Baxter (2022) At: https://sketchfab.com/baxterbaxter/collections/planets-82b7abd4e0644304b65fdc5af7d0aa72. I started my development by thinking that I was from BP_Plants.

**Static mesh of a plant - Keep track of progress - Play particle effects and sounds when something happens - Show what needs to be done to them** 

Starting from the basics of creating the **BP_Plant** adding a *Static mesh* for plant 3D model, Sphere collider to make sure the player can interact with the plants only when they are close enough to the **BP_Plant**, Adding sounds Cues of Success/Fail, also I was researching into the way how can I show the steps that needs to be taken towards every plant separately. For example, some plants need water and other shovels. I researched this matter and found a billboard component. ArtStation - Billboard Material Function - Unreal Engine (2023) At: https://www.artstation.com/blogs/briz/63n8/billboard-material-function-unreal-engine (Accessed  01/11/2024).

So, I drew my sprites in Procreate a put them as a billboard on top of the plant static mesh, as well as an arrow that would indicate where the plant is what plants need doing and which ones were already done.

Blueprint breakdown:

        CheckIfAllDone -> Branch 
        [X]E Has Been Wat / -> TRUE -> Destroy Component (Watering Can billboard) -> Success Visual Audio Function*
            [X]E Has Been Sci / -> TRUE -> Destroy Component (Watering Can billboard) -> Success Visual Audio Function*
                     [X] Has Been Shov / -> TRUE -> Destroy Component (Watering Can billboard) -> Success Visual Audio Function*
                    
What this function does is check what has been done to the plant, for example, when the lant *has been watered* we **remove** watering can billboard + play the the success audio and institute a particle to represent that. 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/m5e2xszq/" scrolling="no" allowfullscreen></iframe>

Then I also created a custom event **AlredyBeenDone** which checks if shovels, scissors, and watering cans, have been already used. If it has been used already the blueprint would create a widget **W_AlredyBeenUsed** which will have custom-bound text in the animation. 

        Alredy Been Done -> True -> Alredy Done Visual Audio -> can be used again(yes) -> TRUE -> set can be used again (no) -> Create a W_AlredyBeenUsed -> in W_AlredyBeenUsed set Used text -> Add to Viewport -> NotifyPlayer anim -> Delay 6.0s -> SET can be used again (yes)
        []Shovel
        []Scissors
        []WateringCan

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/v597v4g_/" scrolling="no" allowfullscreen></iframe>

The last event in the **BP_Plant** is CheckProgress which will check if the progress on the plant went from 3 to 0 meaning the plant is completed. What happens is:

            IF Progress <= 0 -> Destroy Component (Player Proximity) -> Destroy Component (Billboard arrow) -> Get Actor of  Class BP_WinManager -> Adds +1 to progresstoWIN to the WinManager 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/xhikkoyu/" scrolling="no" allowfullscreen></iframe>

###  **Tools functionality**

No, we are super close to the finish line I just need to implement the tool's functinality. So the first think I did was to create the Input Action **IA_UseTool** "Input Actions are the communication link between the Enhanced Input system and your project's code." Enhanced Input in Unreal Engine | Unreal Engine 5.5 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/enhanced-input-in-unreal-engine (Accessed  02/12/2024).
 I assigned the left mouse click to this action. Then I added it to the **BP_FirstPersonCharacter** and on started action it gets actors of class **BP_Plant** and for each loop, it cast to the **BP_Plant** checks if the *Current tool* is either BP_WateringCan, BP_Shovel or BP_Secateus and then activates corresponding function **UseWateringCan, UseShovel, UseSecateus**, and then set the *Watered?, Shoveled?, Scissors Used? bool in **BP_Plant** to *TRUE*. 

**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/vn932kcc/" scrolling="no" allowfullscreen></iframe>

Now I want to talk about the **UseWateringCan, UseShovel, UseSecateus** these are the functions that are directly present in the BP_WateringCan, BP_Shovel or BP_Secateus. In my development, I will only talk about the **UseWateringCan** since the other 2 functions are the  same. 

So what happens in this function is that we input the plant that we have successfully cast to and then check is the plantis  *Watered?* if not we check again if the plant is valid and -1 from the plant *progress* variable. (Reminder, we have *Progress* 3 where 3 is plant needs an action and 0 plant is completed). Then we play the Watering sound (*that was made by me*) and trigger the **Check Progress** event that we talked about prior. 


**Here is the blueprint:**
<iframe src="https://blueprintue.com/render/091au0ou/" scrolling="no" allowfullscreen></iframe>

###  **UI and Polishing**

When the main game was completed I made a UI menu, win and lose screen. For the concept of my game, I decided to go with Flipbook actor. "Each Flipbook Component instance can specify a custom colour that will be passed down to the Flipbook Material as a Vertex Color." Flipbook Components in Unreal Engine | Unreal Engine 5.5 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/flipbook-components-in-unreal-engine (Accessed  01/11/2024).

I created my Flipbooks in Procreate and created materials with them as well to make my UI aimed. Why have I chosen Flipbook? Because it's a good way to show a stop-motion effect like an old cartoon. 

##  **Playtesting**

When my game was in playable test I asked my classmates to play test it. They all gave their consent and permission for me to film them. Here is the video of them playing the game:

https://youtu.be/BDejTYlSl6g **<- watch this** 

Generic feedback was to improve player movement and UI because testers didn't know where to go and what to do.

After I fixed all of the issues I gave my friends a chance to play the new version of the game. Unfortunately, I didn't get their concept to film them but their feedback was:

* Amelia: *"Love the concept, the game feels very nice, although the environment doesn't much the idea, but I liked the game in general."*

* Nadia *"Being a ghost is cool, maybe make a flying plasma particle, like the afterglow, to make sure the layer knows there is a ghost. But in general, I liked the game"*.

After analysing feedback, I changed what I could and know in what direction I should be moving to improve this project further. 

##  **New skills**

This project helped me to get used to Unreal Engine Blueprints, as well as working with Unreal Engine Niagara FX system, at the beginning, it was very hard to get my head around it, but after a couple of tries and fails I was able to achieve quite a lot. Also, it was very helpful to look at the Unreal documentation and ask AI models to help me understand blueprints better and how my knowledge of c# can be used to work Unreal Engine Blueprints. 

It was also great to ask my tutor Assad for feedback and help with my blueprints, because my knowledge of the engine was pretty low I was over-engineering everything, Assad helped me to make everything work better and at the same time look professional.

## Outcome


- [Example Video Link](https://youtu.be/V82Bseexc6Y)
- [Graveyard Bloom REP](https://github.com/isyvanenko/Graveyard_Bloom.git)
- [Example Build Link](https://perisinch.itch.io/graveyard-bloom)



## Critical Reflection

### What did or did not work well and why?



### What would you do differently next time?









                           







## Bibliography

Flipbook Components in Unreal Engine | Unreal Engine 5.5 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/flipbook-components-in-unreal-engine (Accessed  01/11/2024).

Enhanced Input in Unreal Engine | Unreal Engine 5.5 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/enhanced-input-in-unreal-engine (Accessed  01/12/2024).

BP_Plant was made with the help of Chat GPT 5.0 mini

Enhanced Input in Unreal Engine | Unreal Engine 5.5 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/enhanced-input-in-unreal-engine (Accessed  01/11/2024).

Character Movement Component | Unreal Engine 4.27 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/networking-and-multiplayer-in-unreal-engine  Accessed  01/11/2024)



## Declared Assets

Hedge shears from Sketchfab - The best 3D viewer on the web (2020) At:https://sketchfab.com/3d-models/hedge-shears-0019c537f5884ce08907a07e72bd3b64https://sketchfab.com/3d-models/hedge-shears-0019c537f5884ce08907a07e72bd3b64 (Accessed  01/11/2024).

Stylized shovel from Sketchfab - The best 3D viewer on the web (2020) At:https://sketchfab.com/3d-models/stylized-shovel-3e409779cb3d416b9323c1cf52665308 (Accessed  01/11/2024).

Watering can from Sketchfab - The best 3D viewer on the web (2020) At:https://sketchfab.com/3d-models/watering-can-fe9879c5e6884adbb111b7e7dd88df7d(Accessed  01/11/2024).

Stylized Fantasy Skymap Environment - Modular Open world Skymap (s.d.) At: https://www.fab.com/listings/b59af281-5a81-4846-acab-0a303f71c74b (Accessed  01/11/2024).

New Rocker (s.d.) At: https://fonts.google.com/specimen/New+Rocker (Accessed  01/11/2024).

Flower star icon, SVG and PNG | Game-icons.net (s.d.) At: https://game-icons.net/1x1/delapouite/flower-star.html (Accessed  01/11/2024).

Stylized Plant Pack by Marbles studio (2022) At: https://skfb.ly/oBWK8 (Accessed  01/11/2024).

