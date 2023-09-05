+++
title = 'Sony Dslr Mirrorless Camera Control [Part 3]'
date = 2020-01-29T18:51:25-06:00
draft = false
+++
Sony take a very programmer-unfriendly approach to taking photos (any many other aspects of camera control).
Most actions involve taking the same steps as the user of the camera would take.

## TAKING A PHOTO

The process of taking a photo via the USB connection involves a number of steps:

1. Push the shutter button half-way down - this will engage auto-focus if enabled.
2. Push the shutter button all the way down - this will actually start the image acquisition using the camera's current settings.
3. Release the shutter button. If using **BULB** mode, this needs to be timed to be at the end of the exposure, for other exposure durations, the button can be released immediately.
4. Wait for the camera to extract the photo from the sensor.
5. Wait for the camera to package up the photo into the RAW and/or JPEG formats (and save it if set to save to memory card is enabled).
6. Download the photo from the camera (if the Camera is set to RAW+JPEG this may involve downloading multiple files)..

There are some important notes related to the above process:

* If auto-focus is enabled, it is necessary to introduce a pause before moving to step 2 as the camera needs time to perform the focus operation. I have noticed that if the button is fully depressed too quickly when auto-focus is enabled the camera will occasionally *NOT* take a photo at step 2.
* Once you've committed to taking the photo in step 2, it is necessary to follow through with the remaining steps, *even if you don't want the photo anymore*. This is because the camera buffers the photo until you read it. If you don't retrieve the image, it'll still be there waiting for you when you take the next photo. Do this too many times and the buffer will fill up and the camera will appear to freeze/hang. Photos will remain waiting for you even after disconnecting the camera/turning it off. It is occasionally necessary to physically remove the battery from the camera if the "saving" icon on the display won't stop.
* The photo isn't immediately available - the camera needs to be polled regularly until the **Photo Buffer Status** property indicates camera is ready for you to download the image.
* For "reasons", it is necessary to read the cameras current settings before it'll let you take a photo. If you don't it won't take the photo. This is not really an issue, as you'd normally want to check that (a) there isn't a photo waiting in the buffer that needs to be dealt with, and (b) the camera is in a "ready to take a photo" state (i.e. there isn't someone already pressing the shutter button manually). Both of these require the settings to be read.

The process I follow when taking a photo is (pseudocode):

```
def take_photo(duration):
    settings = get_camera_settings()

    # Pull any waiting images off camera (and discard)
    while settings["PhotoBufferStatus"] != IDLE:
        if settings["PhotoBufferStatus"] == READY:
            fetch_image_info()
            fetch_image()

        settings = get_camera_settings()

    # Ensure the shutter button isn't already down
    if settings["ShutterButtonStatus"] != UP:
        error

    # Take the photo
    set_shutter_button(HALF)
    set_shutter_button(DOWN)
    sleep(duration)
    set_shutter_button(HALF)
    set_shutter_button(UP)

    # Wait for the image to be ready
    while settings["PhotoBufferStatus"] != READY:
        settings = get_camera_settings()

    # Download the image, we loop in case multiple images are waiting
    # (Camera can be set to provide both RAW and JPEG images for each photo)
    while settings["PhotoBufferStatus"] == READY:
        info = fetch_image_info()
        image = fetch_image()
        settings = get_camera_settings()

    return info, image
```

## MESSING WITH THE CODE
A number of very helpful testers have helped me get the driver working quite reliably. I myself have taken 100's of photos in a single session with no issues.

Because of this I've decided to publish the code for the [ASCOM Driver (C#)](https://github.com/dougforpres/ASCOMSonyCameraDriver) and for the actual [Sony Camera Control DLL (C++)](https://github.com/dougforpres/SonyCamera) on GitHub.

I welcome any contributions/fixes/bug reports.