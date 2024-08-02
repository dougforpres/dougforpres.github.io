+++
title = 'More native Sony Camera support in N.I.N.A'
date = 2024-08-01T20:50:00-06:00
draft = false
+++
Many of the users of my Sony ASCOM driver are N.I.N.A fans, and have more than their share of... let's call it *fun*...  trying to dial in things like ISO/Gain via the interface that both N.I.N.A and ASCOM expose.

Another feature I miss in N.I.N.A that I'm always using in APT is live-view.

These things aren't really N.I.N.A's fault, it's the fact that ASCOM was designed around dedicated cameras, and Astrophotography software was designed around ASCOM, so getting a DSLR/Mirrorless camera to perform these tricks is not what either of them was really designed to do.

## N.I.N.A == Open Source

Because N.I.N.A is Open Source software, anyone is free to change/fix/extend it. In fact, N.I.N.A supports a way for developers to add new functionality without even having to change N.I.N.A's source-code. This is N.I.N.A's [Plugin functionality](https://nighttime-imaging.eu/docs/master/site/tabs/plugins/available/). It's possible to add all sorts of new abilities to N.I.N.A via plugins.

## The Sony Camera Plugin (for N.I.N.A)

The plugin (`Sony Camera Plugin`) provides a slightly more direct way of communicating with your camera, and as such makes some features easier to use.

### Live-View

If your camera supports Live-View, a new button will appear on the Imaging tab that looks like an old-school video-camera. Clicking on this button will show a **Black-and-White** view from the Live-View feature on your camera. The image is updated at a fairly low frame-rate - I get around 8fps.

This is great if you're manually focusing with/without a Bhatinov mask, as the image on your computer is undoubtedly bigger than the LCD on your camera. You also get the advantage of using the live-view zoom directly on your camera.

### Gain/ISO shows the actual ISO values

You get to see a drop-down box with the actual ISO values listed.

*Note that this feature requires the driver to have learnt (learned) your cameras supported ISO values.*

### Auto selection of exposure time/BULB

If you select an exposure time of 30s or less, the driver will tell the camera to select the nearest exposure time from it's list. Exposure times of greater than 30 seconds will use BULB mode. This is a feature available in the ASCOM driver, but must be enabled.

*Note that this feature requires the driver to have learnt (learned) your cameras supported exposure times.*

### No messing around in the ASCOM Driver dialog

The Sony Plugin will show a list of attached Sony cameras under the heading "Sony". Your cameras model will be printed (in my case, I see "a6400" in the list).

### Images are not converted from ARW

The Plugin will download the raw ARW file directly from the camera, and N.I.N.A will save that. In order to preview the image on-screen, N.I.N.A will use either "DCRaw" or "FreeImage". I have found "FreeImage" to give me better results.

*Note that neither of these tools are able to handle Sony's new "Uncompressed" ARW files. If you have a newer camera and you have your camera set to use this format, you'll need to go the ASCOM route, as I use a newer library (libraw) that can handle the newer format.*

*I am experimenting with a new plugin feature that will provide a third alternative for preview decoding that uses the "libraw" software.*
