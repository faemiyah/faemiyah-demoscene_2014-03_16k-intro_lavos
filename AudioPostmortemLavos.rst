Audio Postmortem: Lavos or: How Was Bäbby's First Softsynth Formed
==================================================================
We released our 16K FreeBSD intro `Lavos <http://www.pouet.net/prod.php?which=62833>`_ as an entry to the wild compo at `Tokyo Demo Fest 2014 <http://tokyodemofest.jp/2014/>`_. After the party I started to think about doing a write-up of the experience, a closure of sorts.

.. image:: http://img.youtube.com/vi/kZ14px7JKUA/0.jpg
   :alt: Youtube link to a video capture of the intro
   :target: http://www.youtube.com/watch?v=kZ14px7JKUA

*If you think you should watch the real thing instead, you'd be right. YouTube is the detail killer.*

I've often found postmortems and other anecdotal notes of people making things fascinating even if not familiar with the subject or field in question. Therefore postmortem is the format I've decided to use for telling about the making of the synth and the music for the intro and the related experiences. The things I'm going to describe in this postmortem are the things I've found noteworthy or somehow memorable and while probably not too in depth, will contain some technical details of some of the features implemented and the circumstances leading to their inclusion or certain way to implementation.

As this was my first softsynth, some things I find interesting enough to write about might be obvious or naive to some but might still offer some amusement to the ones in the know or (hopefully) provide some insight or inspiration to someone wanting to get into synth coding completely green like I was when I started working on this.

Regardless of the results, coding the synth and using it to produce the audio to Lavos was fun and I hope the postmortem is amusing and interesting enough to read.

Introduction
============

Getting into something new is hard, especially if there is some air of competitiveness about it. The demoscene was like this for me for ages, I just didn't think I could contribute in any meaningful way, always watching from the sidelines. It wasn't until I saw `The Yellow Rose of Texas <http://www.pouet.net/prod.php?which=10562>`_ by Fit & Bandwagon that I finally got to thinking that doing something like it was possible and that I really wanted to have a go at it. Now the will was there but I still had no idea how to get started in practice.

So, knowing no better, I read up on software synthesizers, coding in general, 4k intros, the works. Read and wrote ...not much code, really. I fooled around with FFT, trying to implement some filtering system using it and music visualizer of sorts. This was not really the best angle of attack in hindsight but that's just how it went then.

In 2013, after a failed attempt at a quick party prod at TDF 2013 I got Trilkk, and later, Warma on board and started to work on an intro for TDF 2014. The premise in all its simplicity was to let me try and build a synth for the first time and have an intro to use it for. The actual coding for the synth started in early 2014. While there was no concept for the intro at the time, I knew I needed a head start to make it before TDF in March. I decided to go for a fairly basic, mostly fixed architecture for the synth, using the `Waldorf MicroQ <http://www.soundonsound.com/sos/feb01/articles/waldorf.asp>`_ I know and love as the basis of the design.

I started building a VST plugin in order to have a flexible basis for prototyping and testing. Using the bare bones monophonic "synth" example of the VST SDK as the basis (despite the comments in the example warning against doing so) and adding and refining features piece by piece: polyphony, envelopes, filters, LFOs, panning and finally a simple delay effect before running out of time and having to settle with what I had. Further details follow in the "what went right / wrong" sections.

Surprisingly the end result was better than I could have imagined despite my code and music being rushed and feature wise rather incomplete.

What went right
===============

Prototyping Early
-----------------

As mentioned in the introduction, I decided to take the VST plugin route for the synth to pretty much immediately have a fairly flexible test bed. This way I could also postpone thinking of and implementing the playback routines etc. for the intro itself, relying on the facilities provided by the sequencer/DAW, namely, Sonar X1. In a sense, this allowed me to "calibrate" the synth by comparing the output to other VST instruments to notice flaws as I went which came in handy as I was building from ground up with no prior experience.

Let me tell you, realtime oscilloscopes and spectrum analyzers can save you from so many headaches and are worth their weight in gold (free ones are mighty fine I say). Just fooling around with your bare bones synth, watching the analyzer graph gives you a lot of insight on how your synth behaves. It certainly saved me from some funky problems when finetuning the filters where some logical errors caused the filter to go all BRRGLUHRRMM while everything mostly seemed to be working fine.

Using Familiar Tools
--------------------

As I hadn't done any audio tool coding before, I had no idea if there were any best practices regarding control data. Having only used trackers and MIDI sequencers and not knowing much about what went on under the hood, MIDI was the most familiar control data format for me and a logical step as I was going the VST / MIDI sequencer route anyway. As MIDI is simple as a format, developing the necessary routines to deal with the MIDI data from Sonar was pretty straightforward and saved a lot of time when the deadline was drawing near. I ended up implementing the conversion using midicsv and Perl, which has been my go-to tool for string handling even though everything else was written in C++. Using Perl for handling a csv file was quick and painless but going with the first idea that comes to mind naturally returned to bite me in the proverbial behind. I will describe these problems later in the "what went wrong" section.

Getting Started
---------------

Last but not least, one of the better decisions I've made in a long time was to get started with the project in the first place. One might feel this is silly and obvious but I'll be having none of that. It's really easy to give up, never getting started, fearing rejection, failure or whatever or feeling that you can't catch up with others. I felt this way for a long, long time and never made the transition.
 
.. image:: http://img.youtube.com/vi/rqdrtzCaSHw/0.jpg
   :alt: Youtube link to UR - Transition
   :target: http://www.youtube.com/watch?v=rqdrtzCaSHw

*Make your transition.*

At the risk of sounding like a bad self help book I'm still going to say if you want to try coding a synth or a cool effect or whatever but are unsure about it, go for it.

What went wrong
===============

Prototyping for Too Long
------------------------

It's easy to stay in ones comfort zone, maintaining the status quo. I was bitten by this during development, just playing around with the synth features but not moving on to the other parts necessary for the intro: the soundtrack itself, the playback routine and control data structure. It wasn't until just a couple of weeks before TDF that I even started to work on these and even then I was still mostly just working on the synth engine. This lead to a situation where a week before the event I had only the basic playback routine and a couple of test notes, no workflow for actual music production, no control data structures and no soundtrack at all. I basically rushed these in a few nights and it shows. The soundtrack itself is basically something I pieced together the night before the flight to Tokyo and has gotten some flak for a good reason.

Not spending enough time on the playback part also lead to a very data driven setup which was not a very good starting point, considering that the aim was to meet executable size constraints, even though said constraints were not very strict. The end result was not very elegant, even though it did work.

Using Familiar Tools (and Ignoring Reality)
-------------------------------------------

While I would say using stuff I knew was mostly a good thing that expedited the development, it made it easier to fall into the trap of going with the first idea that comes into ones mind, however naive, because one feels comfortable with it and considers the idea good. In this case, my decision to go with a MIDI to CSV to (a not completely thought out) magic number array solution made it hard to fix problems very late in the project.

I mentioned earlier that the control data solution came together quickly and naturally but the fact is that the end result is a rather unrefined, enterprise-y mess. When I was first scrambling for a quick solution to the control data problem, I came across the midicsv utility `midicsv <http://www.fourmilab.ch/webtools/midicsv/>`_ utility by John Walker. While I had originally planned to implement a recording feature in the VST instrument that would export the complete code out, as time was running out I had to resort to quicker hacks and this seemed to fit the bill. Unfortunately it turns out that the MIDI data exported by Sonar was more verbose than I had hoped and had to be refined further. Enter Perl. In the end the workflow was as follows:

1. Export MIDI from Sonar
2. Run midicsv
3. Send converted CSV file to a Linux box
4. Run the Perl conversion script
5. Take the converted array of numbers and dump it in the intro source code

Not very elegant but it worked and there was no need to do this all that often so I made do with it. However, I hadn't considered the fact that midicsv output had absolute timestamps instead of delta time and this would require larger numbers than necessary for a size constrained intro. Trilkk was not very pleased with this and hacked around it later.

Another oversight on my part was the need for a user interface for the VST plugin. When I started working on the plugin, I was overjoyed to notice that the VST host provided bare bone slider controls for the defined parameters quite like the default controls in Buzz Tracker. Unfortunately I later found out that (in Sonar at least) the number of controls available this was is quite limited as they are all organized vertically in a single column and you can only fit so many controls in a window with no scroll bars. So, I needed to implement a GUI somehow. I'd read about VSTGUI being a mess so I decided to look for something else and first tried out `JUCE <http://www.juce.com/>`_ library but soon grew to dislike the fact that JUCE is a full-blown toolkit/framework and was getting in my way more than I liked. Searching on, I finally stumbled upon `WDL-OL <https://github.com/olilarkin/wdl-ol IPlug/WDL-OL>`_ which provided all I needed and didn't get in the way much. While fiddling around with GUI stuff can be fun, having to work on one out of necessity and on a deadline is a bit of a bummer. That said, it was very necessary and ultimately a good thing. I will probably release the VST plugin once all the features I want are there and it's given a bit of polish.

.. image:: http://faemiyah.fi/pui/muri/opsv1sss.png
   :alt: screenshot of the VST plugin GUI
   :target: http://faemiyah.fi/pui/muri/opsv1ss.png

*Such was the rush that some last minute additions don't even have labels.*

Getting Obsessed With Details
-----------------------------

When I started working on the synth, my aims were set pretty low. As long as it doesn't sound completely offensive and works, it's fine, I told myself. I had originally planned to just go with basic naive oscillators, maybe lowpass and highpass filters, a delay, aiming for not much more than chip sounds.

Some time later when I finally had naive oscillators implemented with some envelopes I hit some high notes and the aliasing was way too distracting to ignore, way worse than I had imagined. Still, I decided to postpone figuring the oscillators out and proceeded to get some filters and other necessities in place. Eventually I returned to address the aliasing issue but was unsure what to do. Wavetables were the first idea and would have been quite appropriate given the nature of MicroQ but I decided against them thinking they wouldn't be a good fit code size wise. This probably isn't true but whatever. Then I considered oversampling, raw fourier series' and some other approaches but finally after running into a thread about polynomial bandlimited step (PolyBLEP) oscillators on a `KVR Audio forum thread <http://www.kvraudio.com/forum/viewtopic.php?t=375517>`_, I figured this would be a good balance between simplicity and suitably pleasing results.

As for the filters, my starting point was simply browsing through the `Music-DSP code archive <http://musicdsp.org/archive.php>`_, implementing and trying out the different ideas since designing filters from scratch is way out of my league. After spending a lot of time on this I was still quite unhappy about the results as nothing I tried had characteristics I was looking for. That said, I didn't try out ALL of the bazillion designs on musicdsp but out of the ones I tried none tickled my fancy. Eventually I ran into yet another `KVR Audio forum thread about filters <http://www.kvraudio.com/forum/viewtopic.php?f=33&t=350246&start=105>`_ and in the thread found Robin's implementation of a zero delay feedback state variable filter that I decided to try out. It was just about perfect and provided implementations for filter types I didn't even know what to do with. I spent a while tweaking the filter characteristics to suit my preferences, possibly messing up the theoretical correctness of the response but luckily that doesn't really matter since it's not science we're dealing with here. The implementation is a bit heavy given the size coding target but I could not resist.

Unfortunately some time after the filters were in place, I got obsessed with the oscillator aliasing again and wasted a couple of weeks going for oversampling which ultimately just plain failed. This wasted time would've been better spent elsewhere and almost ruined it all given how I essentially ran out of time and had to go with naive and messy solutions later to barely make it for TDF.

Crunch and Beyond
-----------------

Another issue related to failed scheduling and enterprise-y solutions was that I had to enter full crunch mode for the couple of days and nights before leaving for Tokyo and had some health issues accompanied by insomnia the night before the night before departure, so in the end there was a single night to compose the soundtrack and try and make sure everything works. Somehow the soundtrack came together when I strangely hit "the zone" around 1 AM and worked until around noon, ran some final compilations and submitted everything into the repository, thinking it's done, packing my bags and leaving for the airport.

.. image:: http://faemiyah.fi/pui/muri/lavos_sonar_sss.png
   :alt: screenshot of the track structure in Sonar X1
   :target: http://faemiyah.fi/pui/muri/lavos_sonar_ss.png

*Sparse and simple.*

Boy, was I WRONG. Getting online after arriving at Tokyo and checking IRC I found Trilkk, who had been up all night, furious: my code was crap and crashing, precalc took forever and the sound was too quiet and worst of all the executable was 1K too big. I was in no shape to fix anything and it all seemed rather bleak, Trilkk soldiering on while I went to sleep. The next morning I somehow woke up early and checked IRC again to find that Trilkk had fixed most of the issues but the sound was all wrong. The dynamic range was fine now but the sound was distorting like hell and was a bloody mess. Unfortunately I had no way to fix the control data itself since I had no tools, just a Nokia N900 phone and ssh access to a server that had nano and vim installed. I started looking for unnecessary code that could be cut out to save some space and fiddling with the sound level to make it less of a mess, changing, compiling and running the code on the server and exporting the audio as raw audio, compressing it, downloading it to my phone and listening the end result.

Over the course of 5 hours we managed to hack enough useless junk out of the code and arrange the audio channels enough with pure dumb luck (a function that scaled the channel levels by the channel array number) that the end result was tolerable and the intro was finally ready.

Conclusion
==========

All in all I am very happy about the whole project and especially pleased about the fact that no-one has complained about the audio quality per se even though the music understandably has gotten some flak for being sub-par given the size: the audio code (including the synth, playback routine and song data) was almost half of the whole 16K executable (the intro without audio is around 8300 bytes) and I think the size of the audio part can at least be halved. I've also been told that the sound of the pre-TDF version played differently depending on whether it was compiled with GCC or Clang due to me writing past some buffers but these have been fixed later.

I know I haven't touched the technical aspects of the production too much in this write-up, so if you have something you would want to hear more details of or just want to say hi, feel free to hit me up at collateralburger [at] gmail.
