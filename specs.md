Here the principles of the rhythm game engine will be described.

This is necessary in order to understand how the game elements should work, what parameters should be described, how they should interact with each other and with the player's input.

# 1    Quick time event
## 1.1   Gameplay
The gameplay is based on a QTE. When such an event occurs, the player needs to make an input within a certain period of time.
Input can be pressing or releasing a key in the case of a discrete input, starting or stopping an analog input device, such as rotating an encoder in SDVX controllers, moving a stick dualshock/xbox controller, or moving a lightsaber in a Beat Saber.
A QTE may require multiple inputs to be made in a given small period of time.

## 1.2    Event timing window
A QTE is determined by the period of time in which it is necessary to make an input.
The input before this period does not affect the state of the QTE, the input during the QTE affects its state, the input or lack of input after the QTE does not affect the state of the QTE.
The input behavior at the boundaries of this period is not defined, but it is recommended that the period boundaries be included in the period itself.

## 1.3    QTE Priority
The QTE priority determines to which QTE the input will be applied at a given moment in time.
The QTE priority is applied when periods of two or more QTEs overlap. Among intersecting QTEs, only QTEs with higher priority will accept input. In case there are several QTEs with the highest and equal priority, the input should be applied to all of them. The QTEs to apply the input are selected only among the incomplete QTEs.

## 1.4    Input processing
Processing of user input and QTEs occurs periodically.
At the time of processing, the following data is known:
* the current time
* a list of input events with the time of their occurrence
The current time must be fixed at the start of processing.
The event time must be between the beginning of the last cycle (not inclusive) and the beginning of the current cycle (inclusive).
Processing of a QTE should take place in the following order (sorted by time):
- updating a QTE by the time of the i-th input
- application of the i-th input
- ...
- updating a QTE to the current time

# 2    Rhythm game
## 2.1    Time ralations
### 2.1.1    Play time
The play current time is defined by a special timer. This timer should be able to change the speed of time, change direction, pause, set an arbitrary value of the current time.
This timer can be represented by an internal timer of the playing audio. If the timer is implemented separately, it is recommended to seamlessly synchronize it with the audio.

### 2.1.2    Note time
One note can represent several QTEs.
Each QTE is defined by
- note time, which is used to store the note in the notchart, display the note on the screen.
- early and late window.

## 2.2    Note types
Descriptions of notes are given here without scoring.

### 2.2.1    Short note
Uses discrete input (press only).

Short note is defined by:
- note time
- early hit window => [note time - early hit window, note time)
- late hit window => (note time, note time + late hit window]
- early miss window => [note time - early miss window, note time - early hit window)
- late miss window => (note time + late hit window, note time + late miss window]

QTE period is [note time - early miss window, note time + late miss window]

Short note has the following states:
- clear - initial state
- passed
- missed

The following state changes are possible:
- clear -> passed - press inside  [note time - early hit window, note time + late hit window)
- clear -> missed - press inside early miss window or press inside late miss window or if nothing has been done before note time + late miss window 

### 2.2.2    Long note
Uses discrete input (press and release).
Long note provides 2 QTEs.
Long note is defined by:
- note start time
- note end time

Key press:
- early start hit window => [note start time - early start hit window, note start time)
- late start hit window => (note start time, note start time + late start hit window]
- early start miss window => [note start time - early start miss window, note start time - early start hit window)
- late start miss window => (note start time + late start hit window, note start time + late start miss window]
- late start pressed miss window => (note start time + late start miss window, note start time + late start pressed miss window]

Key release:
- early end hit window => [note end time - early hit window, note end time)
- late end hit window => (note end time, note end time + late end hit window]
- early end miss window => [note end time - early end miss window, note end time - early end hit window)
- late end miss window => (note end time + late end hit window, note end time + late end miss window]
- early end released miss window => [early end released miss window, note end time - early end miss window)

1st QTE period is [note start time - early start miss window, note start time + late start pressed miss window]
2nd QTE period is [note end time - early end miss window, note end time + late end miss window]

Long note has the following states:
- clear
- start passed pressed
- start missed
- start missed pressed
- end missed
- end passed
- end missed passed

The following state changes are possible:

- clear -> start passed pressed - press inside [note start time - early start hit window, note start time + late start hit window]
- clear -> start missed - press inside early start miss window or press inside late start miss window or if nothing has been done before note start time + late start miss window
- clear -> start missed pressed - press inside late start pressed miss window

After these 3 changes first QTE is considered completed

- start passed pressed -> start missed - release inside early end released miss window
- start passed pressed -> end missed - release inside early end miss window or release inside late end miss window or if nothing has been done before note end time + late end miss window
- start passed pressed -> end passed - release inside [note end time - early hit window, note end time + late end hit window]

- *start missed pressed -> end missed passed - same as (start passed pressed -> end passed)
- start missed pressed -> start missed - same as (start passed pressed -> start missed)
- start missed pressed -> end missed - same as (start passed pressed -> end missed)

- start missed -> start missed pressed - same as (start passed pressed -> start missed) but with release
- start missed -> end missed - same as (start passed pressed -> end missed)

After * changes second QTE is considered completed 

## 2.3    Offsets
There are many different implementations of offsets.
But to achieve any effect from these offsets, it is necessary to be able to move the QTE period and the visual display of the note relative to the notchart. During the discussions, it was accepted to use these 2 offsets. Let's call them `input offset` and `visual offset`  

These offsets should be used as follows:
1) All QTEs should be moved by `input offset`. If QTE starts at 1s without offsets, then with `input offset = 1s` QTE should start at 2s.
2) All note views should be moved by `visual offset`. If note view starts at 1s without offsets, then with `visual offset = 1s` note view should start at 2s.

Formulas that implement this should look like this:  
`note time = base note time + input offset`  
`note view time = base note time + visual offset`  
It is assumed that the notcharts are correctly timed.  
If the chart is timed incorrectly, you should use the local offset. Local offset applies to both offsets described above.  
`note time = base note time + input offset + local offset`  
`note view time = base note time + visual offset + local offset`  
