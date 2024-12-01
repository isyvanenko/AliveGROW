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

####  **Movement**
For this project I decided to go with the first person template since, ghost don't have arms and body I set the arms of the template as **not visiable** and to better understand what I can change in the player movement componwent I looked in to the Unreal Engine documentation. Character Movement Component | Unreal Engine 4.27 Documentation | Epic Developer Community (s.d.) At: https://dev.epicgames.com/documentation/en-us/unreal-engine/networking-and-multiplayer-in-unreal-engine  

Here I was able to learn that I can modify the walk speed, velocity, gravity to make my character apear as they are floating. Here is a short video showcasing the movement:

https://youtu.be/he965XDY6oM

[![IMAGE ALT TEXT HERE] (http://img.youtube.com/vi/he965XDY6oM.jpg)](http://www.youtube.com/watch?v=he965XDY6oM)

Assets:
https://skolaztika.itch.io/dark-elegance-renpy-gui