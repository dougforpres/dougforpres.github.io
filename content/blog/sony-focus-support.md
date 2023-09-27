+++
title = 'Sony Focus Support'
date = 2023-09-27T10:51:55-06:00
draft = false
+++
This is a follow-up to the prior post where I introduced the ability to control a camera-attached lens through the ASCOM Focuser interface.

It turns out that while it did indeed work, there isn't a lot of support out there for the mode of operation (Relative) that the focuser supports. APT has partial support, but I had no joy in getting it to actually focus properly.

As such, the focuser code has done-away with the Relative mode of operation and now emulates an Absolute focuser. This works much better, although each different lens requires custom configuration.

At this point in time, six different lenses are supported:

1. Sony E 3.5-5.6/PZ 16-50 OSS
2. Tamron 150-500mm F/5-6.7 Di III VC VXD (1)
3. Sigma 16mm F1.4 DC DN | Contemporary 017
4. Sigma 100-400MM F5-6.3 DG DN OS | C (2)
5. Sigma 85mm F1.4 DG DN | A (1)
6. Sigma 50mm F1.4 DG DN | A (1)

(1) Thanks to Fabian for his contribution.\
(2) Thanks to Joshua for his contribution.

The learning process for a new lens is currently very clunky, but once completed I've been able to successfuly focus on stars using both APT and NINA using the new code.

Note: Both AF sessions were taken with differing lighting conditions (one was at dusk) and using different locations in the sky due to weather. Both were done using my Sigma 16mm lens.

{{< figure src="/images/APT-af.png" caption="APT Auto-Focus" >}}
{{< figure src="/images/NINA-af.png" caption="NINA Auto-Focus" >}}

### In a nutshell

The code emulates an absolute focuser by learning the seven possible step sizes that the lens can make - it then calculates the best combination of steps to get from the current focus position to the desired one.

Of course, the camera doesn't provide any hints, so instead the code drives the focus to infinite, and then uses that as the known starting point.

### Why not just use the Sony SDK?

There's a couple of really good reasons I don't use the Sony SDK.

* The Sony SDK only supports *new* cameras released in the last couple of years - my driver supports many more models of camera.
* My camera isn't compatible with the Sony SDK (despite being less than 4 years old) - this means I would have no way to develop/test any code.

### I use a Sony compatible lens, how can I get it supported?

First, see if the lens can be driven by the camera - I use the Sony Imaging Edge - Remote app to test out my lenses.
The app has a panel that allows focus to be moved in and out.

{{< figure src="/images/Sony-Remote-Focus.png" caption="The Focus Pane in the Sony Remote App" >}}

Set your camera/lens to Manual Focus, and try hitting the "\<\<\<", "\>\>\>", "<<", ">>", etc buttons. Does the lens focus change? Does the focus position displayed by the camera move?

On my Sigma 16mm lens, the buttons worked, but the camera display showed that focus was not moving. Luckily, I found a firmware update on the Sigma site that enabled the feature.

Once you've confirmed that the lens can be controlled from the camera, we can move onto learning the specific moves that the lens supports (contact me via email at retrodotkiwi@gmail.com).

The control values for each lens seem to vary wildly from lens to lens, so picking another model that's "close" will probably not give great results.

For example, my Sony kit lens has a maximum of 264 steps from full-macro to infinite focus. The Sigma 100-400mm lens I just added supports over 5000 steps!

### Where can I get this version?

Please bear that in mind that this code is still very much in alpha mode. Also, remember that "*If I don't know it's broken, I can't fix it*" - so be prepared to send log files if reporting any issues.

The latest installer is available on my [GitHub releases page](https://github.com/dougforpres/ASCOMSonyCameraDriver/releases). You need to install version **1.0.1.13** or later.