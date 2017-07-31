---
layout: post
title: 1080 Ti Liquid Cooling with Kraken G10
---

In this post, I'll be walking you through the process of tearing down an MSI Armor 1080 Ti, removing the extremely underpowered heat sink, and replacing it with a Kraken x40 using a Kraken G10 adapter.

## Initial temps and overclock:
![png]({{ site.baseurl }}/images/tut1/output_3_1.png)
![Initial Temps]({{ site.baseurl }}/images/1080ti/finals.png)
Initially, the highest stable clock was 2012 MHz, which quickly throttled to 1924 MHz due to high thermals.  The peak temperature was 84 C and the fans were screaming at 89%.  This is apparently fairly typical is the MSI Armor variant of the card, which [uses the same heat sink as a GTX 1070 but has the custom PCB of an MSI Gaming X.](https://www.youtube.com/watch?v=BNQtfNFCWa8)

Let's look at the results first: after changing the cooler to the Kraken x40, the new temperatures peak at 53 C and the core clock of 2012 is maintained throughout the benchmark. Excellent!  As noted by others, [the large thermal mass of the liquid can give artificially low temperatures under short load conditions](), and the steady-state temperature seems to be around 58 C while mining at a constant 60% power draw.  However, the system is much quieter and much less sensitive to the seemingly extremely poor airflow in my case, which is awesome.
![Final Temps]({{ site.baseurl }}/images/1080ti/finals.png)

# The Process

Now, let's get into the process! Unforunately, I didn't take as many pictures as I wanted, but the process was extremely straightforward - my graphics card didn't have a backplate, so I only had to remove the four screws holding on the heat sink, and remove the Warranty Void If Removed sticker.  After the removal of these screws, the heat plate comes right off, requiring only a couple pounds of force to break the adhesion caused by the thermal paste, leaving a naked PCB:
![Pic_1]({{ site.baseurl }}/images/1080ti/IMG_2081.jpg)

The heat sink resting on the card, screws removed:
![Pic_2]({{ site.baseurl }}/images/1080ti/IMG_2082.jpg)

After removing the heat sink, clean off the existing thermal paste with Isopropyl Alcohol, until it's nice and shiny:
![Pic_3]({{ site.baseurl }}/images/1080ti/IMG_2083.jpg)

The adapter used is the Kraken G10, which comes with an included fan.  Screw the fan to the mount such that the air will blow towards the graphics card (NZXT fans eject air from the side with the sticker), and insert the business end of your CPU cooler of choice - in this case, the Kraken x40.  Align the nubs such that the cooler can be held in place by the bracket.  Unfortunately, this design is not particularly robust to movement - the force between the bracket and the PCB holds the cooler in place, so when the bracket is not screwed onto something, the cooler is free to flop around, which can be frustrating.  I didn't replace the thermal paste, but using a superior product like [Liquid Metal](https://www.amazon.com/gp/product/B0039RY3MM) can apparently [decrease die temps by up to 10C compared to stock thermal compound](https://www.youtube.com/watch?v=TCu4IsUEyCA), so I may upgrade this interface in the future.
![Pic_4]({{ site.baseurl }}/images/1080ti/IMG_2084.jpg)

Once mounted, the cooler should look like this:
![Pic_5]({{ site.baseurl }}/images/1080ti/IMG_2086.jpg)

A mounting bracket is inserted through the back of the graphics card, which has threaded rods which extend through the holes in the mounting bracket.  There are 4 nuts which hold the bracket to the graphics card.  It is recommended to tighten these to only finger-tight, and in a criss-cross pattern to help ensure the thermal paste is uniformly squished between the copper plate and the die.  It is possible to damage the GPU by over-tightening these screws, so I took a conservative approach to how tight they should be.  The rubber gaskets essentially extrude themselves from between the bracket and the nuts, which is not a good indicator of overtightness, if I could do it over again I would remove them outright.

Here is a (potato quality) glamour shot of the card in my case:
![Pic_6]({{ site.baseurl }}/images/1080ti/IMG_2088.jpg)

An important thing to note - initially, my GPU temps were very high, the core temperature linearly increased to 73 C, and looked as though it would keep climbing.  Thinking I had done something wrong, I consulted the internet, at which point it was fairly apparent that I had not tightened the nuts down far enough. I removed the card from the system and re-tightened the nuts another turn or so, and after plugging the card back in, the temperatures had improved to their current (and final) values - an impressive temperature drop of over 30 C!  These results are in line with the results of Gamers Nexus, who also demonstrated that the VRM and FET temps drop as a result of not being artificially heated by the (usually pretty warm) heat sink, even without small heat sinks of their own.   
![Pic_7]({{ site.baseurl }}/images/1080ti/GN_temps.png)

Overall, this was a fairly simple and rewarding project, and hopefully the reduced temperatures help extend the lifetime and performance of the card!

