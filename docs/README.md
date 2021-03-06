[Carl Moser](https://github.com/cmoser96), [Emma Price](https://github.com/ejprice54321), [Cleophas Kalekem](https://github.com/kalekem), [Jeremy Garcia](https://github.com/jag-123)


## Overview
We implemented a screensaver in C++ using OpenGL based on the [pipes screensaver](https://www.youtube.com/watch?v=Uzx9ArZ7MUU). View or download our code [here](https://github.com/jag-123/SoftSysAmusedAnemone/tree/master/final).

## Goals
This project was created for our Software Systems course. For this project, our team was interested in implementing graphics using OpenGL and C++. Creating a screensaver that was 3-dimensional and changed over time seemed like a perfect way to improve our skills and create something enjoyable for everyone.

## Background
Going into this project, half of the team had not used C++ or OpenGL before and the other half had a reasonable amount of experience. Most of the background research that was done in preperation for this project consisted of online tutorials to get all of the members comfortable using OpenGL and a lot of experimentation with example code. The primary outcome of this research was knowledge of how OpenGL creates and displays objects and how it allows objects to change over time. We also learned a lot about how OpenGL programs tend to be structured and which functions act as the building blocks for their programs.

Here is an example of how we used some of the GLUT library opengl functions:

    //Init glut
    glutInit(&argc, argv);
    glutInitDisplayMode (GLUT_DOUBLE | GLUT_DEPTH);
    glutInitWindowSize(screenW, screenH);
    glutCreateWindow("Pipe Screensaver");
    glutFullScreen();

    //Set the glut functions
    glutDisplayFunc(display);
    glutReshapeFunc(reshape);
    glutKeyboardFunc(processKeys);
    glutTimerFunc(MOVE_SPEED, grow, 0);

## Implementation
### Overview
Our pipe screensaver is created with a pipe that grows incrementally around the screen. Once it grows to contain a certain number of pipe units or it cannot grow any more (because it would hit itself otherwise), it stops growing and remains on the screen while more pipes continue to grow. After SOME pipes are created, the entire screen resets and it starts building again.

For example, this is how we implemented our reset function:

    void reset(){
        resetVal += 1;

        //Adds the current pipe to the queue of all created pipes on the screen
        partCoords2.insert( partCoords2.end(), partCoords.begin(), partCoords.end() );
        partCoords.clear();

        //New color
        red = rand()%255;
        green =rand()%255;
        blue =rand()%255;

        addColor();

        //Create a new pipe start
        vector<float> point;
        int x = rand()%(HEIGHT/2);
        point.push_back((float)x+1);
        int y = rand()%(WIDTH/2);
        point.push_back((float)y+1);
        int z = rand()%(DEPTH/2);
        point.push_back((float)z+1);
        map[x+1][y+1][z+1]=1;
        partCoords.push_back(point);
    }

### Map Creation
In order to grow the pipe, we first needed to create an environment for it to grow in. This took the form of a three-dimensional matrix of integers that was slightly larger than the screen. The map with all of the edges marked as 1 and everything within marked as 0. All places within the map that contain a pipe are marked with an integer according to the direction that the pipe should be facing.

This is the constructor map function:

      void constructMap(){
          /*
              Constructs the map with walls being 1 and all other space 0
          */
          for(int row=0; row<WIDTH+2; row++){
              for(int col=0; col<HEIGHT+2; col++){
                  for(int z=0; z<DEPTH+2; z++){
                      if(row==0 || col==0 || z==0){
                          map[row][col][z] = 1;
                      }
                      else if(row==HEIGHT+1 || col==WIDTH+1 || z==DEPTH+1){
                          map[row][col][z] = 1;
                      }
                      else{
                          map[row][col][z] = 0;
                      }
                  }
              }
          }
      }

### Pipe Generation
Our pipe is made of a dequeue (double ended queue) that contains vectors representing coordinates where the pipe has been. The pipe begins at a random point and from there it grows according to the timer function. Each time it grows, the pipe randomly chooses a direction to go in and creates a new coordinate which is pushed onto the pipe dequeue. It also changes that point in the map from 0 to 1-3 (based on the direction the pipe should be facing). After this, a for loop goes through the pipe coordinates to draw and rotate a cylinder at each point.

Our pipe coordinates are contained in a dequeue because pipe growth is determine only based on the previous value in the pipe dequeue. Our pipe coordinates are housed in a vector because they do not need to change after they are created.

This is an example of the code that we used to generate our pipe:

        vector<float> point;
        int x = rand()%(HEIGHT/2);
        point.push_back((float)x+1);
        int y = rand()%(WIDTH/2);
        point.push_back((float)y+1);
        int z = rand()%(DEPTH/2);
        point.push_back((float)z+1);
        map[x+1][y+1][z+1]=1;
        partCoords.push_back(point);

Once the pipe has reached it's limit all of it's coordinates are added into a deque containing all of the coordinates that have been generated this round and the "current pipe" coordinates are cleared. This allows us to continue generating new pipes while still displaying old ones.

This change over happens here:

    partCoords2.insert( partCoords2.end(), partCoords.begin(), partCoords.end() );
    partCoords.clear();

#### Error Checking
When the pipe randomly chooses a direction to grow in, it must first check to see which surrounding coordinates are available. It does so by taking the last coordinate in the pipe queue, and checking the value of the surrounding x, y, and z directions. If that direction is available (is 0), it will add that direction to a vector of possible directions that the growing pipe will choose from. The direction vector contains 10 times as many options to continue growing in the direction that pipe is currently pointed, which mimics the choices made within the [Actuall Pipe Screensaver](https://www.youtube.com/watch?v=Uzx9ArZ7MUU). If this feature were removed, error checking and pipe generation could be combined to simplify the code.

#### Pipe Coloring
In order to get each pipe to be a different color even while another pipe was growing, we used a deque that contained a vector of red, green, and blue values from 0 to 255 for each segment of the pipe that was created. Red, green, and blue are global variables that are first initizialized with randomly selected values. These values are the RGB values for that each segment of that pipe and are pushed onto the deque containing the colors. When a new pipe begins to grow, the RGB values are randomly assigned agained and will hold for the rest of that new pipe's segments. 

## Results
The following video is an example of our pipes screen saver in action.

[Pipes Screen Saver](https://www.youtube.com/watch?v=aWCM1NLJ_yE&feature=youtu.be)

