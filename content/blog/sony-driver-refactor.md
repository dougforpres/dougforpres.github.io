+++
title = 'Sony Driver Refactor'
date = 2023-09-07T11:58:11-06:00
draft = false
+++
Over the past couple of weeks I've made some pretty dramatic changes to the Sony ASCOM driver.

### One Thread To Rule Them All

Up until this point, the driver has communicated with the camera in multiple threads, with locks in place to ensure **A** doesn't talk to the camera while **B** is.

The updated version isolates all camera communication into a single thread, with a simple work-queue so that **A** will just wait until **B** is complete before running. This simplifies things a little and should reduce the possibility of deadlocks.

### Tasks are now implemented as simple state machines

Although most of the tasks that are performed are quite simple and only require one step, there are a small number where it was simpler to break them up into smaller parts. Since the state-machine has *success* and *fail* steps, it's easier to handle errors that way.

One major component that took advantage of this is the flow used to take a photo. Previously, all of the following were implemented as a single "chunk":
* Ensure camera is ready to take a photo
* Take the photo
* Await the image
* Download the image
* Process the image

With the state-machine change, there are now two distinct flows:
1. Set up camera and take photo
2. Download and process photo

_Note:_ I missed a step didn't I? Where is the "Await the image" step? Hold your breath, all will be revealed.

### No more spamming the camera for status updates

Sony cameras expose a number of *events*. Some models of camera expose more events than others. My a6400 exposes only 3 events (which I hope will be the minimum).
1. One of the camera settings has changed
2. There is at least one image waiting to be downloaded
3. There are no more images to download

As a result of this, the driver only asks for the settings *when told something has changed*, and it automatically downloads images *whenever they are available*.

This has some pretty big benefits:
* No more need to constantly ask "*anything new?*"
* Photos get downloaded when they're ready - this means that the *take a photo* task no longer needs to wait - it just takes the photo, and then when it's available a second task is run to download it. **Even better**, you can now take a photo using the shutter button and it should be downloaded (and optionally saved) without causing a connection problem.

### Performance and Reliability improvements

#### Performance

When testing with N.I.N.A I noticed that it seems to be faster getting the image from the camera. This could be my brain playing tricks on me, but previously I could have sworn it was slower.

#### Reliability

Once I finished moving all the communication code into a single thread, the errors *seem* to have stopped. It remains to be seen if this is just chance, or I stumbled upon a solution for the long-standing problem.
This is more a feeling than documented reality at this point, but I *feel* the USB connection to the camera is a little more robust after moving all the communication code to a single thread.

## Bonus Feature

The driver is now actually *TWO* drivers.  The first is the original ASCOM Camera driver with all the changes above, the second is a simple ASCOM Focus driver that will allow control over a **Sony compatible motorized focuser** (in simple terms, a motorized camera lens).

This isn't actually as exciting as I'd hoped, as the two apps I tried don't support this kind of *relative* focuser and only support focusers that use *absolute* positioning.

The camera doesn't give any feedback as to what the focus is currently set to (despite it showing it on-screen... *thanks Sony*) so we're currently stuck with this back-and forth option.

#### What's the difference?

* **Relative** focusers operate by repeatedly shifting the focus relative to the current position - i.e. "a bit in" or "a bit out" until focus is achieved.
* **Absolute** focusers operate by shifting the focus to a specific position - i.e. "position 7433".

A good analogy might be when hanging a picture...
* **Relative** your assistant might say "a little higher" or "a little lower" until the picture is in the right spot.
* **Absolute** your assistant might say "160cm from the floor", then "162cm from the floor", etc.

So, feel free to try out the focuser, but please remember that it currently requires your application to work with _relative_ focusers.

* N.I.N.A only seems to support *absolute* focusers
* APT seems to have the beginnings of some emulation, and I've reached out to its author to see if we can do anything to get it working
* Others, no idea at this time