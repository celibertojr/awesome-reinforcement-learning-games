# GPU robot vision simulation full AI

This is currently just a crazy project idea research, and does not have a major implementation yet.

Early stage prototypes:

- <https://github.com/cirosantilli/general-game-ai>
- <https://github.com/cirosantilli/Urho3D-cheat>

The goal is to make an open source game / series of games to be played *by computers*, to serve as interesting problems to develop [full AI ](https://en.wikipedia.org/wiki/Artificial_general_intelligence).

The games are *not* designed to be fun for human players: they are designed to be fun for human programmers who are coding full AI robots.

We will however add an interactive human player mode so that humans can mess around with the games and better understand them, to develop better AI faster.

There are two central focuses:

- use the GPU as much as possible
- design game mechanics that are simplified versions of the real world, while retaining key aspects of the real world like perspective. In particular, with focus on spacial, vision and control loops.

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
-   voxel or vector:
    - the real world is actually voxel, which is tempting
    - however, voxels are expensive to compute, therefore practical games we've seen so far:
        - have voxels at least as large as the player. But 50% of our daily thinking is about things smaller than ourselves, e.g. food.
        - have very limited voxel dynamics, e.g. no rotations

Mechanics ideas:

-   extract discrete concepts from continuous data.

    You notice that some pixels always move together. So you interpret them as being one single "object", a discrete concept in your mind.

    Possibly partition tasks as:

    - CPU: symbolic manipulation tasks
    - GPU: continuous data tasks

-   <https://en.wikipedia.org/wiki/Object_permanence>

    You are a starving predator and you see a single prey turning around a corner.

    The robot should have memory that he is chasing this single prey, and some type of understanding that things disappear from sight around corners, and then turn the corner as well.

-   discrete consequences to continuous action. E.g.: you touch a red wall: then a blue gate opens.

-   topology:

    - two preys go into a closed cave. I enter and eat 2 preys. So then I should go out to find more.
    - I see a prey through a hole in the wall. The hole is too small for me to get in. So I must find another way.
    - I construct a closed barrier around me. Predators cannot eat me! (Minecraft home building)

-   current tool mechanic: the robot has a current tool + a single actuator for it. Like switching weapons on Minecraft / FPS.

-   thinking like someone else: if prey sees me, it runs away. So I find a way to approach without being seen.

-   recognizing places: there is lots of food around the place with two trees. So I hang out around that place more often than others.

Evil ideas:

-   invisible objects. E.g.: Mario hidden blocks. Possibly controlled by agents, like camouflage in nature, or spy in Team Fortress.
-   objects that look like other objects. E.g. a prey that looks like predator. Mimicry in nature: <https://en.wikipedia.org/wiki/Mimicry> Possibly via metamorphosis.
-   two objects that look the same, but act very differently. Agent then has to identify which one it is.

## Literature

Existing research in the area.

-   <https://gym.openai.com> most interesting set of hand made games I've seen so far since 2D focus, but no 2D Minecraft yet
    - <https://github.com/rll/rllab> TODO what value does it add on top of Gym?
    - <https://github.com/openai/roboschool> bullet based robotics simulation environment
-   DeepMind Lab: <https://github.com/deepmind/lab> 3D focus, newbs :-) Labyrinth demo: <https://www.youtube.com/watch?v=M40rN7afngY>
    - but they also do other more interesting things: <https://deepmind.com/blog/understanding-agent-cooperation/>
    - <https://github.com/deepmind/dm_control> continuous control problems. Mujoco based.
-   <https://en.wikipedia.org/wiki/Competitions_and_prizes_in_artificial_intelligence#Games>
    - <https://www.quora.com/What-are-the-most-interesting-online-AI-competitions>
    - <http://www.machinegame.com/> <https://www.reddit.com/r/programming/comments/5fjjej/i_made_this_a_multiplayer_game_where_you_code_to/>
    - <http://www.codetition.com/>
    -   2D tile based games
        - http://vindinium.org/ TODO source
        - https://robotgame.net/login
        - <http://www.halite.io/>
    -   code several bots to reach some goal, strategy-RTS-like:
        -   <https://empireofcode.com>
    -   <http://theaigames.com/> several games, most not every interesting / too limited / artificial, but site goods good, VC backed?
-   <http://togelius.blogspot.co.uk/2016/07/which-games-are-useful-for-testing.html>
    - http://gvgai.net/
    - <https://www.youtube.com/channel/UCov_51F0betb6hJ6Gumxg3Q>
    -   <http://togelius.blogspot.com.br/2016/01/why-video-games-are-essential-for.html> Researcher on this exact domain.
        - <https://www.reddit.com/r/artificial/comments/40kg3b/why_video_games_are_essential_for_inventing/>
        - <http://togelius.blogspot.fr/2016/03/switching-brains-and-putting-cart.html>
        - AI & Games vlog by Scottish researcher Tommy Thompson <https://www.youtube.com/channel/UCov_51F0betb6hJ6Gumxg3Q>
-   FPS
    -   2Ds are called top down shooters
    -   <http://vizdoom.cs.put.edu.pl/competition-cig-2016>
        -   By people of Poznam, Poland:
            - <https://github.com/Marqt>
            - <https://github.com/ebonyclock>
        - <https://www.engadget.com/2016/09/22/facebook-and-intel-reign-supreme-in-doom-ai-deathmatch/>
    -   <http://botprize.org/>
        - <https://www.youtube.com/watch?v=mUNfjMDhCpM>
    -   <http://www.doc.ic.ac.uk/teaching/distinguished-projects/2011/p.lipka.pdf>
        - <https://www.linkedin.com/in/peter-lipka-763aba5b>
        - <http://improbable.io/> Saw this on TechCrunch before. Hot stuff.
        - <https://www.linkedin.com/company/3011959?trk=prof-exp-company-name>
    -   DeepMind <https://www.youtube.com/watch?v=nMR5mjCFZCw>
        - <https://deepmind.com/blog/understanding-agent-cooperation/>
        - <http://deepmind.com/>
        - <https://github.com/kuz/DeepMind-Atari-Deep-Q-Learner>
        - <https://github.com/deepmind/pycolab> minimalistic Python tile based games, terminal graphics, some algorithms implemented 
-   Racing. TODO: what is the current human vs machine state? Better be good if we want self driving cars!
    - <https://www.youtube.com/watch?v=_XKphuYviE0>
    - <https://blogs.msdn.microsoft.com/shawnhar/2009/12/29/bug-or-feature/>
    - mario kart 64 tensorflow <https://www.youtube.com/watch?v=vrccd3yeXnc>
    - super mario kart using marI/O by Seth Bling https://www.youtube.com/watch?v=S9Y_I9vY8Qw
    - <https://github.com/udacity/self-driving-car-sim>
-   <https://www.quora.com/Artificial-Intelligence/How-hard-would-it-be-to-create-an-AI-to-successfully-solve-most-FPS-game-campaigns-today>
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
-   <https://www.riddles.io/>
-   <https://github.com/GoodAI/BrainSimulator> tiny demo at: <https://www.youtube.com/watch?v=Mi8jgH2orP4>, but they are not very good at presenting results
-   GPU path finding <https://www.youtube.com/watch?v=1OSXWhd3hvI>
-   <https://en.wikipedia.org/wiki/Falling-sand_game>
    -   <https://github.com/simtr/The-Powder-Toy>
-   <https://screeps.com/> <https://www.youtube.com/watch?v=0DtltU9ozHI>
-   AI game threads:
    - <https://www.reddit.com/r/coding/comments/1pkemh/games_like_this_where_you_program_a_botai_and_it/>
    - <http://stackoverflow.com/questions/25952/best-programming-based-games>
    - <https://www.quora.com/What-are-some-video-games-where-you-can-program-AI>
-   walking rigid body robots
    - DeepMind 2017 humanoid, spider, <https://www.youtube.com/watch?v=gn4nRCC9TwQ> <https://www.reddit.com/r/compsci/comments/6n0oev/google_deepminds_paper_explores_how_agents_can/>
    - SIGGRAPH Asia 2013 Flexible Muscle-Based Locomotion for Bipedal Creatures <https://www.youtube.com/watch?v=pgaEE27nsQw>
-   roguelike
    - <https://github.com/krajj7/BotHack>
    - <https://github.com/rogueinabox/rogueinabox> Python based and integrates with the major Python libs, relies on a system `rogue` executable, interacts through terminal scraping
    - <https://github.com/cirosantilli/general-game-ai/tree/c1b1f143a5ef2ddf7ddb80cadbe47ee670013dd7>
-   <http://blog.otoro.net/2015/05/07/creatures-avoiding-planks/> 2D with perspective.
-   <http://www.webearthonline.com/>
-   <https://en.wikipedia.org/wiki/Life_simulation_game>
    -   3d first person:
        -   <https://en.wikipedia.org/wiki/WolfQuest> 2011
            -   youtuber playing <https://www.youtube.com/watch?v=ck5BrLh2eqI>
    -   iHasCupquake <https://www.youtube.com/channel/UCqg2eLFNUu3QN3dttNeOWkw> youtubber that reviews tons of games that have some potential
    -   Aavak channel <https://www.youtube.com/channel/UCqvU9Uxf_8YJOq67S6qcrFw>, lots of survival / strategy games
    -   Third person;
        - <https://en.wikipedia.org/wiki/Lion_%28video_game%29>
        - gameplay <https://www.youtube.com/watch?v=opDch4j8Bt8>
    -   <http://agar.io/>
    -   <https://www.youtube.com/watch?v=hbVUF5b307s> Bear simulator 2016
        - <http://kotaku.com/successful-100-000-kickstarter-dev-calls-it-quits-due-1763162848>
        - <http://bearsimulator.com/>
        - 100k kick-starter, but closed source?
-   building / crafting focus:
    -   Minecraft
        - Malmo <https://blogs.microsoft.com/next/2016/03/13/project-aix-using-minecraft-build-intelligent-technology/>
        - Open source clones:
            - https://github.com/fogleman/Minecraft 1Kloc pyglet-only no inventory
        - 2D versions
            - minicraft
                - https://www.youtube.com/watch?v=31byVUMTSTE
                - https://github.com/shylor/miniventure
                - originally by Notch for Ludum Dare: http://ludumdare.com/compo/ludum-dare-22/?action=preview&uid=398
                - TODO originally for android?
    -   http://playrust.com/
    -   http://www.medievalengineers.com/ | http://www.spaceengineersgame.com/ both by same company, which founded Good AI with revenues
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
            - Terrain-Adaptive Locomotion Skills Using Deep Reinforcement Learning (SIGGRAPH 2016) <https://www.youtube.com/watch?v=KPfzRSBzNX4>
        -   evolution of brain:
            - Evolved Step Climbing Creatures https://www.youtube.com/watch?v=euFvRfQRbLI
-   2D continuous vector games
    -   2D fighting
        -   smash bros:
            - https://www.youtube.com/watch?v=2A34d2O_Zb0
        - http://www.ice.ci.ritsumei.ac.jp/~ftgaic/ custom made for research
        -   Fighting games
            - <http://www.ice.ci.ritsumei.ac.jp/~ftgaic/> FightingICE
    -   Top down:
        - Rocket league https://www.youtube.com/watch?v=iXSvDIayLCw closed source
-   3D fightging:
    - https://en.wikipedia.org/wiki/Toribash
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
    - voxel
        - http://dan-ball.jp/en/javagame/dust/366422.html
        - dwarf fortress
        - terraria. Reversed simple map format and editors: <https://www.youtube.com/watch?v=uMtFHa-mZFE>
        - <http://www.particleplanet.com/>
-   <http://stackoverflow.com/questions/6542274/how-to-train-an-artificial-neural-network-to-play-diablo-2-using-visual-input>
-   <https://www.reddit.com/r/SurvivalGaming/comments/5501hy/best_topdown_2d_survival_game/>
    - <https://github.com/CleverRaven/Cataclysm-DDA> open source, crafting, construction, hunger, etc. Humongous scope. Anything made on the genre will be a subset of that :-)
-   actual old games:
    -   http://www.pacmanvghosts.co.uk/ for research
-   Open source game lists:
    -   https://github.com/piranha/osgameclones/

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
-   Skinner behaviourism experiments
    - <https://www.youtube.com/watch?v=QKSvu3mj-14>
-   Watchmojo! <https://www.youtube.com/watch?v=eQMP74-XziI>
    - dog agility (obstacle courses) <https://en.wikipedia.org/wiki/Dog_agility>
        - <https://www.youtube.com/watch?v=ggsIU8rXubk>
    - <https://www.youtube.com/watch?v=bT-fctr32pE> underwater bubble rings

## Tools

## Libraries

### cuDNN

Closed source primitives by CUDA, can be used as backend of open source projects like Caffe.

### Mixing stuff

The key technical challenge of this project is keeping all calculations inside the GPU:

- http://stackoverflow.com/questions/18086519/is-it-possible-to-bind-a-opencv-gpumat-as-an-opengl-texture
- http://stackoverflow.com/questions/4005935/mix-opencl-with-opengl

### Engines that allow to take the image from games

- <http://code.tutsplus.com/tutorials/how-to-build-a-python-bot-that-can-play-web-games--active-11117>

### Robot simulation

- <http://gazebosim.org/>
- <https://github.com/opensim-org/opensim-core>
- <https://github.com/openai/roboschool>

## Events

-   <http://www.cig2017.com/competitions-cig-2017/>
    -   <http://gaips.inesc-id.pt/geometryfriends/> 2D rigid body physics based
    -   http://game.engineering.nyu.edu/showdown-ai-competition/ pokemon turn based fight
-   <http://cig16.image.ece.ntua.gr/> University in Greece

## Misc

-   <http://www.danielslater.net/> <http://www.danielslater.net/2015/12/how-to-run-learning-agents-against.html>
-   game ai
    -   <https://www.reddit.com/r/gameai>

## Keywords

-   AI evaluation platform
-   Photogrammetry <https://en.wikipedia.org/wiki/Photogrammetry>
-   SLAM
    - https://en.wikipedia.org/wiki/Simultaneous_localization_and_mapping
    - https://github.com/pamela-project/slambench
-   <https://en.wikipedia.org/wiki/Cognitive_robotics>

## Spacial hardware

I hereby predict that in the future, if we ever reach full AI, we will have specialized integrated circuits that interpret 3D space somehow.

Here are my current findings:

- <https://www.youtube.com/watch?v=Ng6zy7hI64A> FPGA implementation, Duke publicity: <http://pratt.duke.edu/news/robotic-motion-planning-real-time>, DARPA funded and they made a startup out of it: <https://www.linkedin.com/in/daniel-sorin-ab056a4/> <http://rtr.ai/> so everything must be closed source.
