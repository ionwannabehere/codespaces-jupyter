## Table of Contents

-----

- [Introduction](#introduction)
  - [System Structure](#system-structure)
- [Pong Game](#pong-game)
  - [Pong Heartbeat](#pong-heartbeat)
  - [Pong Buttons](#pong-buttons)
  - [Pong Initialization](#pong-initialization)
- [Menu System](#menu-system)
- [Heartbeat Events](#heartbeat-events)
- [Global Variables](#global-variables--variable-sharing)
- [Buttons and Events](#buttons-and-events)
  - [Event Handling](#event-handling)
    - [Buttons](#buttons)
      - [Ports / Gold Icon](#ports--gold-icon)
    - [Event Handler](#event-handler)

---
  
Full Code: https://github.com/ionwannabehere/Microbits/blob/main/main.py



## Introduction

### System Structure
The type of system that you use for these type of process enumeration, and several process handling is important. As well as your event handling / input handling; since the way that code is structured each line is consecutive, and becomes a struggle when wanting several things to happen at once.
The structure of my system is as follows:  

EventList ["Event 1", "Event 2", "Event 3"]  

```
Events:
    While True:
        If event is active:
            If event isnt already active:
                Add to Event List  
```
Processes {
    Menu,
    Pong,
    Settings
}
```
Process Structure:  
    ProcessInit():  
        Clear Display  
        Display Normal process  
        Set variables  
    ProcessButtonA():  
        Grab global variables  
        Change and process variables  
        Display changes  
        Set variables back  

        
Event Handling Structure:  
    When I get event  
        If Process is Active:  
            ProcessButtonA()  

Menu Structure:  
    If process is active, and gold is touched:  
        All processes off  
        Wait for completion (100ms)  
        Display Clear  
        MenuInit()  
    If Menu is Active, and gold is touched:  
        Menu off  
        Process On  
        Wait for completion (100ms)  
        Display clear  
        ProcessInit()  
```        

## Pong Game

### Pong Heartbeat

Pong's Heartbeat requires a couple of things, first it requires the heartbeat check [here](#heartbeat-events), Global event timer [here](#hrtCheck), 

### Pong Buttons

Pong's buttons utilizes the [globalValue's](#global-variables--variable-sharing) players paddle var, and the ball's location; these functions are called by the [event handler.](#event-handling)


```python
def pongABtn():
    plrD = getattr(globalValues,"plrD")
    ball = getattr(globalValues,"ball")
    if plrD >0 and not (ball[0] == 0 and ball[1] == plrD-1):
        display.set_pixel(0,plrD+1,0)
        setattr(globalValues,"plrD",plrD-1)
        display.set_pixel(0,plrD-1,9)
        
def pongBBtn():
    plrD = getattr(globalValues,"plrD")
    ball = getattr(globalValues,"ball")
    if plrD < 3 and not (ball[0] == 0 and ball[1] == plrD+2):
        display.set_pixel(0,plrD,0)
        setattr(globalValues,"plrD",plrD+1)
        display.set_pixel(0,plrD+2,9)
```

Due to the nature of the display, the lower the number the higher it is on the screen, respectivly the higher the number the lower it is on the screen. Following this logic, for our A button, the player's paddle is the top pixel of the paddle so in order to check if we are at the maximum height, we just put the condition `plrD > 0`, next we have to check if the ball is already above the players paddle, so we dont collide with it. Its a simple `not (ball[0] == 0 and ball[1] == plrD-1)`, `ball[0]` is the ball's X componet and the ball[1] is the ball's Y componet; if the ball isn't on the left most side, and isn't above the players paddle, continue. From there we just update the display, and update our [global values.](#global-variables--variable-sharing)

Pong's B button is in a similar structure as button A, the discrepancy being the `plrD < 3`, and `plrD + 2`. The if condition has to be 3 since the paddle is 2 units long, 5 - 2 = 3, and the plrD + 2, checks if the ball is below the second paddle pixel, and the global values are update with the corresponding + 1 movement.

### Pong Initialization
Each Process in this code structure is given a process Initialization function, this is required since these are not in any loop and would be laggy and bloated if conditions were in the loop.  


```python
def pongI():
    setattr(hrtValues,"H",0)
    
    r1 = random.randint(1,2)
    r2 = random.randint(1,2)
    ball = [r1,r2]
    if random.choice([True, False]):
        ballDir = -1
    else:
        ballDir = 1
    ballRef = 1
    display.set_pixel(r1,r2,5)
    
    plrD = 2
    display.set_pixel(0,2,9)
    display.set_pixel(0,3,9)
    cmpD = 2
    display.set_pixel(4,2,9)
    display.set_pixel(4,3,9)
    setattr(globalValues,"ball",ball)
    setattr(globalValues,"ballDir",ballDir)
    setattr(globalValues,"ballRef",ballRef)
    setattr(globalValues,"plrD",plrD)
    setattr(globalValues,"cmpD",cmpD)
```

Firstly we set our [Heartbeat](#heartbeat-events) value `"H"` to 0, we use this hearbeat event to move the ball every 1/3 of a second (stated at the start of the while loop, at the end of the code) [Pong's Heartbeat](#pong-heartbeat). Next we state our starting position, this becomes important since due to the screen size being exactly 5x5, it limits the possiblity for the ball to go. So I set a radom number from 1 to 2, and set both x and y to be like this in order to acheive a 50-50 game; and set the local ball variable to those x and y's.

#### X-Game Diagram
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="470px" height="470px" viewBox="-0.5 -0.5 470 470" content="&lt;mxfile host=&quot;app.diagrams.net&quot; modified=&quot;2023-04-28T20:03:04.023Z&quot; agent=&quot;Mozilla/5.0 (X11; CrOS x86_64 14541.0.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36&quot; etag=&quot;SO0d5vmw5cF9i4z7smFY&quot; version=&quot;21.2.3&quot; type=&quot;google&quot;&gt;&lt;diagram name=&quot;Page-1&quot; id=&quot;ZuK0w4bH6WoKOF9JjrW9&quot;&gt;7Vtbb5swFP41eZzENaGPa9Ku0jRtWrSte5ooOMEqwZnjNKS/fibYYC5taUpyWCGRKnx8/77PNufUGZnTVfyJuuvgC/FRODI0Px6Zs5FhOI7G/yaGfWqwJuPUsKTYT016bpjjRySMot5yi320KRRkhIQMr4tGj0QR8ljB5lJKdsViCxIWe127S1QxzD03rFp/YZ8FYlq2lttvEF4GsmddEzkrVxYWhk3g+mSnmMyrkTmlhLD0aRVPUZhgJ3FJ610/kZsNjKKINalw8fXv3CE6vv/jfP95fRN+/hHjD6KVBzfcigmLwbK9RGAXYIbma9dL0jtO8si8DNgq5CmdP7qbdYr7AseId3W5YZTcZ2Bp3LLAYTglIaGHBs2FnXwTO4mYYk8/WQtKzvjw4Tkr8uDeHQaWdE3RBj+qacJcpqS5FJGaRj5WkyHx7pMRH8Yo9KNkC2QQZSh+EnI9I5IvAERWiNE9LyIrjAX3+6Kod7mSrImwBYqKMqMr1LvMms4J5g+C49fwbUAQ7njI8+qIvXNsy9ZaAtspgm1Wwa7D+nRQmwBQX86S77Nr6KlV1wIFGZhdocB6t2o3xx2D2u6d2i27YxSM363abatjUDu9U3v5eNUNaA4uADjwbeT4Vh0HjnFnHjg4wTkKjrXsv0d6Lx+w8ByA+Gpn2d7LJyk81hBuEqzey0csPAcQ/hOMq1oXGDgv1hCOUrd8VXgOIDyo87zPlM9SeKwhXKVueavwHEze7f5ePkvhsR78VVOD5gDCXz2P3qtxX2CsZXyoR3qvBoShOeiPvwqP9eCvwnPQH3/VvIDGevBX4TmA8FeB/rkKjvXgr8Jz0B9/FR7rBv4qivyPyQU/nopIhIpIU7KN/OxyF4oxu1Wef4tSyfMsVjJme5mI+DRuZbEk8VvNySsdUrJWkU77DXQiv3ItsUQmx4JsqYcavJEwly4Re6ZgenOgKg+FfruGfmmjKHQZfigOuE4ToodvBPOp5CtdK6ova1c2kU5U1MqVVW2oHOLSSg2lQFQaOkg0m/bxqpWrqC3VSg1qr9Gg1LreWOtdU+2kqWqNQbVtqLaBnz7stS+q1m6o2jTsPaj2raptEPE4Zq8taFBvuNeqWtf+p71WXpAdVHsm1TaIER2x1zY/6497p+iYapvvteag2jZU2yDaNrwhvKRao7E3ZnVKtUZLqtXL0YJTq/YE8UlVQELmRZVNWgr2li+f1lwgyILyqggy4ytCMjyZ/woxRT//Kad59Q8=&lt;/diagram&gt;&lt;/mxfile&gt;" style="background-color: rgb(255, 255, 255);"><defs/><g><rect x="0" y="0" width="470" height="470" fill="#f5f5f5" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="20" y="20" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="20" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="200" y="20" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="290" y="20" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="380" y="20" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="20" y="110" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="110" y="110" width="70" height="70" fill="#d5e8d4" stroke="#82b366" stroke-width="0.1" pointer-events="all"/><rect x="200" y="110" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="290" y="110" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="380" y="110" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="20" y="200" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="200" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="200" y="200" width="70" height="70" fill="#d5e8d4" stroke="#82b366" stroke-width="0.1" pointer-events="all"/><rect x="290" y="200" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="380" y="200" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="20" y="290" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="110" y="290" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="200" y="290" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="290" y="290" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="380" y="290" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="20" y="380" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="380" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="200" y="380" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="290" y="380" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="380" y="380" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><path d="M 20 450 L 450 20" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 450 450 L 20 20" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 200 450 L 450 200" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 200 20 L 450 270" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 270 450 L 20 200" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 20 270 L 270 20" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><rect x="100" y="100" width="180" height="180" fill="none" stroke="rgb(0, 0, 0)" stroke-width="7" pointer-events="all"/></g></svg>

#### O-Game Diagram
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="470px" height="470px" viewBox="-0.5 -0.5 470 470" content="&lt;mxfile host=&quot;app.diagrams.net&quot; modified=&quot;2023-04-28T20:06:53.150Z&quot; agent=&quot;Mozilla/5.0 (X11; CrOS x86_64 14541.0.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36&quot; etag=&quot;_SjloPvgLKc_ZBXhdAG3&quot; version=&quot;21.2.3&quot; type=&quot;google&quot;&gt;&lt;diagram name=&quot;Page-1&quot; id=&quot;ZuK0w4bH6WoKOF9JjrW9&quot;&gt;7Ztdb5swFIZ/TS4nAYaUXq5pu0rTtGnRtu5qouAkVgnOHKch/fUzwYCBpHU+4LAkjVThAzbwnic+PrbTQ4Np/Il5s8kXGuCwZxlB3EO3PctyXUP8Twyr1GBf9VPDmJEgNZmFYUhesTTKeuMFCfC8dCGnNORkVjb6NIqwz0s2jzG6LF82omH5rjNvjGuGoe+FdesvEvCJfC3HKOwPmIwn2Z1NQ56ZetnF0jCfeAFdKiZ010MDRilPj6bxAIeJdpkuab37LWfzB2M44joVrr/+HbrUJM9/3O8/7x/Czz9i8kG28uKFC/nC8mH5KlNgOSEcD2een5SXwsk9dDPh01CUTHHozWep7iMSY3Grmzln9DkXyxCWEQnDAQ0pWzeIRk7ySew04oo9/ctbUM7013/izJS+eE/rB0tuzfCcvKplyj2ulAWKWC3jgKjFkPrPyROvn1Hyo5yWymDGcbxVcjN3pPgCYDrFnK3EJVmFvvT9qgz1siDJvpK2iUJRbvQkveO86cLB4kD6eBd/WwAOv7lNPpsc++Q6tmMcSWy3LDaqi71J6+akRhDfLdfHvt+01LloXZHaPlmqUb9jUjsnS7XtdEzq/slS7dgdk9o9WaqrYdG0oLW+7hTWrvWE1mPLBuIiuNbZ/VvVOnCwG9hNa10NjPBaQ+RS7XTX1cgIrzVEGtNOf10NjfBaQ+QxMCnjpgS9Xa0hEpl2+utqbITXGiKTaWccUo2N8FpDpDIwWSO81led4rrJ2Aiv9fnkjciA1rpbeWOz86nAWmfzMSfIdX1CFVrr88kb4bU+n7wRXuvzyRvRNbTWEHkj0FojuNbdyhsbjY3gWndrCSzfh7Jt50oTMRPcB+eTT4JrnT3PW1rjKPiYbGwTpYhGuKw0o4soyDc1CRnY6lEt/FYLt3GptMpKMeGPsr3k+LdyXFRJClmNsjudA0IFDmrb8SrOFFrQBfOxRq/BPTbG/I0L02XfOh6K+50N7s9sDIceJy/lB97EhLzDN0rEqxTfdKNMX95u1kT6orJWQVa9oeoUlFFpKBWi1tAa0fy1D6BWI4PZhVrJn6HNX865uQvnXaPW1qQ2VfdC7aHUauSC+/S1JQZNzb5WZd34n/rabD/YhdqWqNXIqvfoa/Vj/X5jio5Rq9/XXkYIR6FWY37iMkJ4j9psten9ca3TKWqtI1FrVvOupqnVyHzPPBuDYqxvltFAVTR0Gdv2U4a2ENNYQL50jO8OQmGi9KHsoS274NtiT2NB/czZAwuhlchn9/dErNZPtj3wa2CxRHW3hLLMxNVxZoet6g7zDbuN8pVAFYLcuMP8sCgWPwVO1S9+T43u/gE=&lt;/diagram&gt;&lt;/mxfile&gt;" style="background-color: rgb(255, 255, 255);"><defs/><g><rect x="0" y="0" width="470" height="470" fill="#f5f5f5" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="20" y="20" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="20" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="200" y="20" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="290" y="20" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="380" y="20" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="20" y="110" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="110" width="70" height="70" fill="#bdbdbd" stroke="#82b366" stroke-width="0.1" pointer-events="all"/><rect x="200" y="110" width="70" height="70" fill="#d5e8d4" stroke="#82b366" stroke-width="0.1" pointer-events="all"/><rect x="290" y="110" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="380" y="110" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="20" y="200" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="200" width="70" height="70" fill="#d5e8d4" stroke="#82b366" stroke-width="0.1" pointer-events="all"/><rect x="200" y="200" width="70" height="70" fill="#bdbdbd" stroke="#82b366" stroke-width="0.1" pointer-events="all"/><rect x="290" y="200" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="380" y="200" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="20" y="290" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="290" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="200" y="290" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="290" y="290" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="380" y="290" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="20" y="380" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="110" y="380" width="70" height="70" fill="#f8cecc" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="200" y="380" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><rect x="290" y="380" width="70" height="70" fill="#bdbdbd" stroke="#666666" stroke-width="0.1" pointer-events="all"/><rect x="380" y="380" width="70" height="70" fill="#bdbdbd" stroke="#b85450" stroke-width="0.1" pointer-events="all"/><path d="M 360 450 L 20 110" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 110 450 L 450 110" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 290 20 L 450 180" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 180 450 L 20 290" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 20 360 L 360 20" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 450 360 L 110 20" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 20 180 L 180 20" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><path d="M 290 450 L 450 290" fill="none" stroke="#b85450" stroke-width="5" stroke-miterlimit="10" pointer-events="stroke"/><rect x="100" y="100" width="180" height="180" fill="none" stroke="rgb(0, 0, 0)" stroke-width="7" pointer-events="all"/></g></svg>

####
Next, we set our ball direction, this is the Y componet of the ball, if its going to go up a pixel or down a pixel. `ballRef` just referres to the Ball's Reflection (if its going left or right). Finally we set our computer and player paddle location and render everything; and update everything in the class. 

## Menu System

## Heartbeat Events


Hearbeat events utilize the built in tick time, in order to find the difference in time frames to have the next hearbeat event to be fired. The syntax in question is `time.tick_ms()`, I made a function that will check if the next heartbeat function should be fired; this is called within the while loop at the end of the code. For this section I will be disecting [pong's hearbeat event.](#pong-heartbeat)


```python
pongA = True

class hrtValues:
    pass

def hrtFnCh(n,z):
    if math.trunc((time.ticks_ms()/1000)*z) > getattr(hrtValues,n):
        setattr(hrtValues,n,math.trunc((time.ticks_ms()/1000)*z))
        eventHandList.append(n)

def pongI():
    setattr(hrtValues,"H",0)

while True:
    if pongA:
        hrtFnCh("H",3)
```

**Heartbeat Check**<div id="hrtCheck"></div>  

Firstly inorder to track the time difference between each event we save the heartbeat values inside of the class `hrtValues`. The function `hrtFnCh(n,z)` (or Heartbeat Function Check), is what checks if the heartbeat should be fired again, I am going to break it down here.

The condtion `math.trunc((time.ticks_ms()/1000)*z) > getattr(hrtValues,n)`, does a few things, `time.ticks_ms()` returns the amount of time in milliseconds since the device has been booted: 

`5623`

Firstly we divide by 1000 in order to break it into seconds, 

`5.623`

This is pretty sufficent but incase we want to have the heartbeat function be greater than or less than 1 second but still in regards, like x0.5 (half second) x2 (2 seconds), we multiply this by the z componet [in our case 3 for "H"], 

`16.869`

Now understand that since we are multiplying these together it is the opposite, 2 = 1/2 of a second, and 0.5 = 2 seconds. I could have fixed this inconvenience but since I was the only one using this code, and fixing would take more space I left it as is. Continuing we have `16.869`, we need to get rid of the decimals we use `math.trunc()` this rounds down the number towards 0, leaving us with 

`16`

Finaly we take this number `16`, and if it is greater than `getattr(hrtValues,n)` (or the hearbeat value stored in hrtValues, our case it is `"H"`). and if so it will set the global value to the new heartbeat number, and lastly it will append the event name to the Event Hand List, to be processed.

**Heartbeat Wrap up/Global timer**  
The rest of the code is self-explanatory, PongA is the boolean variable that is active when the process/game is active, the itialization of the game sets the global hearbeat value of `"H"` to 0, incase it was started from boot. And inside the while true loop, if pong is active, then it will run the heart function check; which checks if the hearbeat should be fired, and adds to the event list automatically due to the function.

## Global Variables / Variable Sharing


Our "global" variables take 2 shapes. The way that functions are made in python, any variables stated before the function are known as global variables in our case we have `vol = 0`. it becomes apparent that we use it as a global variable since inside our setting function for the volume we have to state `global vol` this allows the function to grab this variable. Another way we use variables and variable sharing between functions is through classes, I used `class globalValues`, this isnt entirely required since if needed you can just state all your functions at the top of the code. I use classes in this case to have all the game variables in one place for better management.

For this reason at the top of each button or input function we have to state `var = getattr(globalValues,"var")`
this does 2 things, it grabs the variable var from globalValues.
```
class globalValues
    var = 10  -----> returns
    bar = 30
    foo = 50
```
And sets this to our local variable, `var`, and we then use this variable and adjust it to our needs and at the end of the code, if needed we send it back to the class:

`setattr(globalValues,"tabA",tabA)`

There is a slight issue with this structure, since we initalize the variable, change it, and send it out, it adds a lot more code and questions the size of the code as a whole. However it also gives a pro, since we decide if we want to return the variable back or not, this gives us the extra mobility of having two thing being calculated consequtively and sent off as one. 

`[Calculate A] + [Calculate B] -> Display [A + B]`  

Compared to the conventional

[`Calculate A] -> Display [A], [Calculate B] -> Display [B]`.

## Buttons and Events

### Event Handling

#### Input / Event Handling
These are the conditions for the events, inputs, outputs, and others.


```python
if len(eventHandList) > 0:
    nHrt = True
    while nHrt:
        if len(eventHandList) > 0:
            if eventHandList[0] == "G" or eventHandList[0] == "P1":

                #Clear extra port events
                e = eventHandList[0]
                clrR = True
                while clrR:
                    if len(eventHandList) > 1:
                        if eventHandList[1] == e:
                            eventHandList.pop(1)
                        else:
                            clrR = False
                    else:
                        clrR = False

                # Menu Inputs
                if menuA:

                    # Menu, Gold Input
                    if eventHandList[0] == "G":
                        tabA = getattr(globalValues,"tabA")

                        # Selected Tab is Settings
                        if tabA == 0:
                            menuA = False
                            settA = True
                            sleep(100)
                            display.clear()
                            settI()

                        # Selected Tab is Pong
                        if tabA == 1:
                            menuA = False
                            pongA = True
                            sleep(100)
                            display.clear()
                            pongI()
                else:

                    # Any other program presses the Gold Input. Go to Menu.
                    if eventHandList[0] == "G":
                        pongA = False
                        settA = False
                        menuA = True
                        sleep(100)
                        display.clear()
                        menuI()
                    elif eventHandList[0] == "P1":
                        if settA:
                            settP1T()
                eventHandList.pop(0)
            else:

                # Button Event Inputs
                if eventHandList[0] == "A" or eventHandList[0] == "B":
                    if eventHandList[0] == "A":

                    # If process is active, fire corresponding process for the event.
                        if pongA:
                            pongABtn()
                        elif menuA:
                            menuABtn()
                        elif settA:
                            settABtn()

                    elif eventHandList[0] == "B":
                        if pongA:
                            pongBBtn()
                        elif menuA:
                            menuBBtn()
                        elif settA:
                            settBBtn()
                    eventHandList.pop(0)
                else:
                    #Hearbeat Call Action, Custom events that are on timers 
                    if pongA:
                        pingHrt()

                    # All front non-heartbeat events are cleared.
                    nHrt = False
        else:
            nHrt = False
```

First, if there is any events in the event list, continue, Then it will create a while loop variable and clear out all inputs **instantanously**, where as custom Heartbeat events are handled differently. This is done incase it is needed for *sleeping during hearbeat* functions and to clear out input events faster than the heartbeat ones.

We then clear out extra Pin / Gold events, this is further broken down in [this section](#ports--gold-icon). Next the A and B buttons are handled next, I gave each process their own functions to handle each input. After each branch of conditions we then delete the first event to move onto the next. [Pong Buttons:](#pong-buttons)

```
pongABtn()
pongBBtn()
```

If the event is not "A" or "B" or "G" or "P1", then it has to be a custom hearbeat event. Hearbeat Call Action, will fire if the process is active and the function `pingHrt()`, will be fired. And finally cleaned up by setting notHeart (nHrt) to false.

##### Event List
The event list is stated at the beginning of the code, in order to maintain scope. The variable is named eventHandList, and is an array.  

`eventHandList = []`

After each input it will append to the end of this list to be processed.

### Buttons
Buttons are set up in correspondence with the [Event Handling](#event-handling), each button is given a specific variable to prevent continous input, and will switch off once it has been released.  


```python
if button_a.is_pressed():
    if not aAct:
        aAct = True
        eventHandList.append("A")
else:
    if aAct:
        aAct = False
```

The button, when activated adds their identifier to the eventHandList to be processed, in this case we appended "A" to the Event Handle List.

##### Ports / Gold Icon
These are sightly different due to them having the ability to be active and deactived several times even though the user intended to only give one event. This is due to the sensors not being reliable and isn't a simple on/off input.


```python
if pin_logo.is_touched():
    if not gAct:
        gAct = True
        eventHandList.append("G")
else:
    if gAct:
        gAct = False
```

Since this code still gives multiple inputs, in our [Event Handling](#event-handling) we have to remove all extra instances of these types of inputs.


```python
if eventHandList[0] == "G" or eventHandList[0] == "P1":
    e = eventHandList[0]
    clrR = True
    while clrR:
        if len(eventHandList) > 1:
            if eventHandList[1] == e:
                eventHandList.pop(1)
            else:
                clrR = False
        else:
            clrR = False
```

This code will first check if the first item of the eventHandList is either the gold button event or a Port 1 event

`eventHandList = ["G", "G", "G", "A"]`

Then it is going to set a while active loop with the variable "clrR". Then inside the loop if the length of the Event List is greater than 0 (if there isn't any other events afterwards). It checks if the second index (1) is also the same event, and if so it will delete that index, else if its not the same event it will stop the loop. Lastly if the length of the event handler is only 1 indext long it will also stop the loop for the event to be further processed.

`eventHandList = ["G","A"]`
