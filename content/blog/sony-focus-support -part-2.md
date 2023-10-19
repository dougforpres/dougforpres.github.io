+++
title = 'Sony Focus Support Part 2'
date = 2023-10-18T19:40:00-06:00
draft = false
+++
It seems that the new support for focusing is quite popular!

The Beta version of the driver now supports four more lenses. This brings the total number to ten.

7. Sony FE 5.6-6.3/200-600 G OSS
8. Sony FE 2.8/70-200 GM OSS II
9. Sony FE 4/24-105 G OSS
10. Sony FE 2.8/90 MACRO G OSS

I'd like to thank **Kimmo Manninen** from **Finland** for providing the data for these lenses, and for helping test out and improve the focus support.

**Kimmo** went above and beyond and provided the data in the following tables showing how he tweaked the driver registry settings to speed up the focus process.

Initially, it was taking NINA 9 minutes to complete autofocus. While I don't have the latest numbers (though there are some calculations that make me believe focus is now around 3x faster), I added some new settings that allowed **Kimmo** to tweak the settings and do some testing.

## In the beginning

These are the settings that the driver originally used.

* Lens reset to *infinite* prior to each move. The reason for this is that the driver has to assume the focus is correct and hasn't been manually adjusted by the user.
* After each step, the driver waits a short period of time to allow the lens to move. This wait is `move_size * 200mS`. This translates to:

|Step Size|Delay|
|:-:|----:|
|1|200mS|
|2|400mS|
|3|600mS|
|4|800mS|
|5|1000mS|
|6|1200mS|
|7|1400mS|

The Sony 200-600 lens has 29 steps of size 7 to get to infinite - that translates to `29 * 1400mS = 40600mS` - that's 40 seconds every time NINA changes the focus.

## Some adjustments were made

The driver was updated to use a default multiplier of only 100mS - this had the immediate effect of halving the about of time to move focus - a good start.

Additionally, a checkbox was added to the driver setup that speeds up the amount of time to return to infinite focus. The checkbox allows the user to confirm that they promise  not to make any manual adjustments to the lens. With this box checked, the time to return to infinite focus is drastically reduced.

### Original focus times on the Sony 200-600 lens (200mS multiplier)

|Focus from → to|Box Checked (3 samples)|Box Unchecked (3 samples)|
|:-:|:-:|:-:|
|7896 → 7646|9.0s 7.7s 8.1s|48.8s 49.2s 50.0s|
|7646 → 7896|4.6s 4.0s 4.4s|45.5s 45.2s 45.4s|
|7896 → 7871|7.6s 6.4s 7.5s|47.4s 47.2s 48.1s|
|7871 → 7896|5.3s 4.5s 5.0s|46.8s 45.3s 45.9s|
|7871 → 7846|7.7s 7.7s 7.0s|49.2s 49.4s 47.7s|
|7846 → 7871|7.5s 5.9s 6.6s|48.0s 47.4s 49.0s|

The first column is the change in focus position, the second is a list of times it took the driver to move focus with the new "I promise not to touch the lens" checkbox checked. The third is a list of times it took to do the same move with the checkbox unchecked.

### Updated focus times on the Sony 200-600 lens (100mS multiplier)

|Focus from → to|Box Checked (3 samples)|Box Unchecked (3 samples)|
|:-:|:-:|:-:|
|7896 → 7646|5.1s 5.2s 5.2s|27.3s 26.8s 26.8s|
|7646 → 7896|4.6s 4.6s 4.9s|24.5s 24.5s 25.3s|
|7896 → 7871|4.8s 4.6s 5.5s|25.9s 26.4s 26.4s|
|7871 → 7896|3.2s 3.5s 3.2s|23.8s 25.0s 24.9s|
|7871 → 7846|4.6s 4.6s 5.4s|25.5s 25.8s 26.8s|
|7846 → 7871|6.0s 5.2s 5.0s|25.8s 26.1s 24.8s|

### Kimmo's custom value on the Sony 200-600 lens (50mS multiplier)

|Focus from → to|Box Checked (3 samples)|Box Unchecked (3 samples)|
|:-:|:-:|:-:|
|7896 → 7646|5.2s 4.3s 4.9s|14.4s 14.7s 16.1s|
|7646 → 7896|4.1s 4.3s 4.1s|14.8s 14.3s 13.8s|
|7896 → 7871|3.6s 4.1s 4.3s|14.6s 15.9s 15.3s|
|7871 → 7896|3.9s 3.8s 3.6s|13.9s 14.3s 14.2s|
|7871 → 7846|3.7s 4.5s 5.0s|14.8s 14.5s 15.8s|
|7846 → 7871|3.6s 5.2s 3.3s|15.2s 14.8s 15.3s|

## The numbers are looking much better

Originally, autofocus would take around 9 minutes. I did a couple of quick calculations to see if I could determine how long the auto-focus process would take given the new numbers.

My calculation is based on the following numbers:

```
Time to change focus:              49s
Time to take a photo and download: 10s (an assumption)
```

{{< figure src="/images/calc-number-of-focus-steps.png" caption="Calculate how many focus changes (9.15)" >}}

Now we have the numbers, we can rearrange the equation and substitute the new average of ~5s.

{{< figure src="/images/calc-time-to-focus.png" caption="Calculate how long focus takes after using tweaked numbers" >}}

If we use the calculated number of exposures (9.15) with the optimized focus times, autofocus should be able to complete in just over 2.25 minutes vs. the previous value of 9 minutes.

## Call for contributions

If you use a lens with your Sony camera and it is currently not supported, I have a (pretty rough) tool that will help you learn it. It is a very manual process that requires a lot of staring at the camera display - the benefits can be well worth it!



The latest installer is available on my [GitHub releases page](https://github.com/dougforpres/ASCOMSonyCameraDriver/releases). You need to install version **1.0.1.13** or later.

Calculator images taken from [https://ti89-simulator.com/](https://ti89-simulator.com/)