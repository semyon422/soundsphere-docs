# 1    Rhythm model specs
## 1.1    Engines
### 1.1.1    Graphic engine
Specifies which notes should be drawn at the moment and where.
Notes: short, long, image

### 1.1.2    Logic engine
Specifies which notes should respond to input, and how they should respond.
Implements specification.
Notes: short, long

### 1.1.3    Audio engine
Specifies the sound response to input for notes from the logic engine.
Provides playing audio current time.
Notes: short, long, auto(SoundNote)

### 1.1.4    Score engine
Specifies the numerical response to changes in note states from the logic engine.

### 1.1.5    Time engine
Manages currentTime, timeRate. Synchronise them to current time from the audio engine. 

## 1.2    Managers
### 1.2.1    Pause manager
Pauses and plays

### 1.2.2    Input manager
Maps external input events to internal ones.
Single input event can be mapped to many input events with arbitrary delays.

## 1.3    Input mode, input type and input index
It should be used for skinning only, not any game behaviour should depend on it.

# 2    Rhythm view
Draws notes from graphic engine, provides additional information to them (e.g. chord info, probably Graphic engine should do this). 

# 3    Time units
The game uses seconds (double/number type) to preserve the ability to choose any precision.
In the logic engine and replays, the current time values are rounded up to 1/1024.
This allows you to use 10 bits to store the fractional part of the time in replays, as well as avoid all the bugs associated with 0.1 + 0.2 ~= 0.3 
