# GPU robot vision simulation full AI

This is currently just a crazy project idea research, and does not have an implementation yet.

The goal is to make an open source game / series of games to be played *by computers*, to serve as interesting problems to develop [full AI ](https://en.wikipedia.org/wiki/Artificial_general_intelligence).

The games are *not* designed to be fun for human players: they are designed to be fun for human programmers who are coding full AI robots.

We will however add an interactive human player mode so that humans can mess around with the games and better understand them, to develop better AI faster.

There are two central focuses:

- use the GPU as much as possible
- design game mechanics that are simplified versions of the real world, while retaining key aspects of the real world like perspective

## GPU

The GPU is the most promising computational breakthrough of our time, and might be the first thing that allows us to reach the critical speed / cost point needed for full AI.

The game / AI loop is:

1.  update the rigid body physics engine world state (e.g. Bullet Physics supports OpenCL, NVIDIA PhysX)

    This step can also generate as output some inputs for the game AI, e.g. if the robot touches a wall, a touch sensation input for that part of the robot.

1.  render scene (with OpenGL GPU, duh)

1.  do the game AI, that is, simulate the brain of the virtual robot / animal

    Inputs:

    -   rendered scene (array of pixels) from the renderer
    -   touch input from the physics engine
    -   other inputs:
        - radio signals / voice from other robots
        - currently held "non-physical" symbolic state, like the Minecraft inventory, or the amount of bullets on an FPS

    Processing method:

    -   try to keep everything inside the GPU to avoid CPU round-trip
        - OpenCL / OpenGL bridges for rendered pixels
        - physics engine GPU / OpenCL bridges
    -   use custom OpenCL to make the decisions as much as possible
        -   many useful libraries now have GPU support,  e.g. OpenCV and [NVIDIA cuDNN](https://developer.nvidia.com/cudnn), [NVIDIA VisionWorks](https://developer.nvidia.com/embedded/visionworks) on CUDA

    Outputs:

    -   forces generated by the robot's motors, to be passed as input to the physics engine
    -   other side effects like:
        - radio signals / voice emitted to other peer robots for communication
        - magic "non-physical" symbol operation actions, like crafting an item in Minecraft or pulling a trigger on an FPS

1.  to back to step 1

## Game mechanic

Good game concepts should both:

-   mimic key aspects of simple real robots / animals, which can hopefully be generalized to "real" reality:
    -   first person with perspective, possibly 2D to simplify as in <http://blog.otoro.net/2015/05/07/creatures-avoiding-planks/>
    -   goals similar to those of real animals: eat, don't get eaten, find resources
    -   mix a physical simulation engine with purely symbolic information that skips the rigid body simulator.
        - bullets in real life are physical, but in FPS games they are just one integer.
        - Minecraft raw materials are interesting, as they bridge the voxel physics engine and symbolic information: when you mine, you take a voxel from the physics engine, and change a symbol on your inventory
-   simplify the real world
    - we don't have enough computational power yet for reality
    - we don't know good AI algorithms yet, start with simpler problems first

Mechanics ideas:

-   extract discrete concepts from continuous data.

    You notice that some pixels always move together. So you interpret them as being one single "object", a discrete concept in your mind.

    Possibly partition tasks as:

    - CPU: symbolic manipulation tasks
    - GPU: continuous data tasks

-   <https://en.wikipedia.org/wiki/Object_permanence>

    You are a starving predator and you see a single prey turning around a corner.

    The robot should have memory that he is chasing this single prey, and some type of understanding that things disappear from sight around corners, and then turn the corner as well.

-   discreet consequences to continuous action. E.g.: you touch a red wall: then a blue gate opens.

-   topology:

    - two preys go into a closed cave. I enter and eat 2 preys. So then I should go out to find more.
    - I see a prey through a hole in the wall. The hole is too small for me to get in. So I must find another way.
    - I construct a closed barrier around me. Predators cannot eat me! (Minecraft home building)

-   current tool mechanic: the robot has a current tool + a single actuator for it. Like switching weapons on Minecraft / FPS.

-   thinking like someone else: if prey sees me, it runs away. So I find a way to approach without being seen.

-   recognizing places: there is lots of food around the place with two trees. So I hang out around that place more often than others.

## Literature

Existing research in the area.

-   <https://en.wikipedia.org/wiki/Competitions_and_prizes_in_artificial_intelligence#Games>
-   <http://gvgai.net/>
-   <http://togelius.blogspot.com.br/2016/01/why-video-games-are-essential-for.html> Researcher on this exact domain.
    - <https://www.reddit.com/r/artificial/comments/40kg3b/why_video_games_are_essential_for_inventing/>
    - <http://togelius.blogspot.fr/2016/03/switching-brains-and-putting-cart.html>
-   FPS
    -   2Ds are called top down shooters
    -   <http://vizdoom.cs.put.edu.pl/competition-cig-2016>
        -   By people of Poznam, Poland:
            - https://github.com/Marqt
            - https://github.com/ebonyclock
    -   <http://botprize.org/>
        - <https://www.youtube.com/watch?v=mUNfjMDhCpM>
    -   <http://www.doc.ic.ac.uk/teaching/distinguished-projects/2011/p.lipka.pdf>
        - <https://www.linkedin.com/in/peter-lipka-763aba5b>
        - <http://improbable.io/> Saw this on TechCrunch before. Hot stuff.
        - <https://www.linkedin.com/company/3011959?trk=prof-exp-company-name>
-   Racing. TODO: what is the current human vs machine state?
    - <https://www.youtube.com/watch?v=_XKphuYviE0>
    - <https://blogs.msdn.microsoft.com/shawnhar/2009/12/29/bug-or-feature/>
-   <https://www.quora.com/Artificial-Intelligence/How-hard-would-it-be-to-create-an-AI-to-successfully-solve-most-FPS-game-campaigns-today>
-   <http://deepmind.com/>
    - <https://github.com/kuz/DeepMind-Atari-Deep-Q-Learner>
    - BroadMind: <https://www.youtube.com/watch?v=wfL4L_l4U9A>
-   MarI/O <https://www.youtube.com/watch?v=qv6UVOQ0F44>
    - <http://pastebin.com/ZZmSNaHX>
-   Playfun Computer program that learns to play classic NES games
    <http://www.cs.cmu.edu/~tom7/mario/>
    - <https://www.youtube.com/watch?v=xOCurBYI_gY>
-   <https://www.youtube.com/watch?v=bBZ7kEphv3s> Mario AI
-   <http://code.tutsplus.com/tutorials/how-to-build-a-python-bot-that-can-play-web-games--active-11117>
-   <https://developer.nvidia.com/gpu-ai-board-games>
-   <https://www.reddit.com/r/pcgaming/comments/2vwx06/would_it_be_possible_for_a_game_to_run_100_on_the/>
    - <http://stackoverflow.com/questions/1789178/can-you-program-a-pure-gpu-game>
    - <http://gamedev.stackexchange.com/questions/60776/gpgpu-physics-game-logic>
-   WebGL physics + rendering in GPU <http://nullprogram.com/blog/2014/06/29/>
-   <http://www.uvm.edu/~ludobots/index.php>

## Possible games

### Realistic animal games

Possible games we could use.

-   <http://blog.otoro.net/2015/05/07/creatures-avoiding-planks/> 2D with perspective.
-   <http://www.webearthonline.com/>
-   <https://en.wikipedia.org/wiki/Life_simulation_game>
    -   3d first person:
        -   <https://en.wikipedia.org/wiki/WolfQuest> 2011
            -   youtuber playing <https://www.youtube.com/watch?v=ck5BrLh2eqI>
        -   Minecraft
            -   <https://blogs.microsoft.com/next/2016/03/13/project-aix-using-minecraft-build-intelligent-technology/>
    -   iHasCupquake <https://www.youtube.com/channel/UCqg2eLFNUu3QN3dttNeOWkw> youtubber that reviews tons of games that have some potential
    -   Third person;
        - <https://en.wikipedia.org/wiki/Lion_%28video_game%29>
        - gameplay <https://www.youtube.com/watch?v=opDch4j8Bt8>
    -   <http://agar.io/>
-   tier 2 / under evaluation
    -   https://www.youtube.com/watch?v=gYZyyWwqdiw
    -   http://jobsimulatorgame.com/
    -   baking simulator https://www.youtube.com/watch?v=qqwAnDgsi6Y
    -   Pet simulator <https://www.youtube.com/watch?v=gYZyyWwqdiw>
    -   http://boards.straightdope.com/sdmb/showthread.php?t=614572
    -   2D animal simulations
        -   http://otoro.net/planks/
        -   2D fish simulator: https://www.youtube.com/watch?v=TTj6F1OaK0A
        -   2D bird swarm simulation https://www.youtube.com/watch?v=M028vafB0l8
        -   evolution body structure
            - carykh car evolution simulator https://www.youtube.com/watch?v=GOFws_hhZs8
        -   evolution of brain:
            - Evolved Step Climbing Creatures https://www.youtube.com/watch?v=euFvRfQRbLI
-   first person versions of 2D NES games:
    -   Mario first person
        - https://github.com/olynch/fp_mario
        - https://www.youtube.com/watch?v=6GPQUPZQI88
        - https://www.youtube.com/watch?v=KBb9wFP7uZM
        - https://www.youtube.com/watch?v=U1ojNtSZIjI
        - https://www.youtube.com/watch?v=95anZ3aWw0E
    -   wolfenstein 1D (but in wrong axis to what I want...) http://www.wonder-tonic.com/wolf1d/
    -   non playable by Machima:
        - sonic https://www.youtube.com/watch?v=JlWnShSe6e8
        - street fighter https://www.youtube.com/watch?v=tLOlXozCzjk
        - pokemon https://www.youtube.com/watch?v=mVq_-9jkRFI
    -   zelda https://www.youtube.com/watch?v=KAHRKnZzYDA
    -   bomberman
        -   https://www.youtube.com/watch?v=j94zTevuYZ8
        -   https://www.youtube.com/watch?v=qBUoeJZG03E
-   simulation games, interesting for the world mechanics aspect, but possibly no FPS perspective vision aspect:
    - http://dan-ball.jp/en/javagame/dust/366422.html
    - dwarf fortress
    - terraria
    - <http://www.particleplanet.com/>

## Animal intelligence

Understanding animals could give insights into what our OpenCL intelligence should look like.

-   insects
    -   fruit fly
        - fruit fly associate odour to electric shock <https://www.youtube.com/watch?v=-dPfZE5adYg>
        - fly cyborg 2010 http://spectrum.ieee.org/automaton/robotics/artificial-intelligence/cyborg-fly-pilots-robot-through-obstacle-course
-   reptiles:
    - informal <https://www.youtube.com/watch?v=hr1bKVPyqwU>
-   birds
    - crows:
        - drop stones to raise water level, like in a fable, trained: <https://www.youtube.com/watch?v=lrYPm6DD44M>
        - using sticks as tools: <https://www.youtube.com/watch?v=URZ_EciujrE>
        - 3 tools in sequence: <https://www.youtube.com/watch?v=41Z6Mvjd9w0>
-   tier 2
    - bear turns off electric power to eat deer: <https://www.youtube.com/watch?v=8eC9ZmCaIWY&feature=youtu.be>
-   young humans:
    - <https://www.youtube.com/watch?v=R9QSBUrf94s>

## Tools

### Libraries

### cuDNN

Closed source primitives by CUDA, can be used as backend of open source projects like Caffe.

### Mixing stuff

The key technical challenge of this project is keeping all calculations inside the GPU:

- http://stackoverflow.com/questions/18086519/is-it-possible-to-bind-a-opencv-gpumat-as-an-opengl-texture
- http://stackoverflow.com/questions/4005935/mix-opencl-with-opengl

### Engines that allow to take the image from games

- <http://code.tutsplus.com/tutorials/how-to-build-a-python-bot-that-can-play-web-games--active-11117>

## Events

- <http://cig16.image.ece.ntua.gr/> University in Greece

## Misc

-   <http://www.danielslater.net/> <http://www.danielslater.net/2015/12/how-to-run-learning-agents-against.html>
-   game ai
    -   <https://www.reddit.com/r/gameai>
    -   <http://theaigames.com/>
