---
layout: post
title: "BioAid: The algorithm architecture and its properties"
date: 2013-01-29 14:54
comments: false
published: false
categories: 
---

In a [previous blog post](/blog/2013/01/24/bioaid-algo/), I introduced the BioAid project and discussed some of the motivations. I advise reading that post before reading this one. In this second installment, I aim to describe the algorithm architecture in technical detail and then discus some of its properties. This information is placed on my blog, allowing me to rapidly, and informally communicate some of the technical details related to the project while I gather thoughts in preparation for a more rigorous account.


Modeling the Auditory Periphery
--------------------------------

The architecture of the BioAid algorithm is based on a [computational model of the auditory periphery](http://www.essex.ac.uk/psychology/department/HearingLab/modelling.html), developed by, and under the supervision of [Prof. (Emeritus) Ray Meddis](http://www.essex.ac.uk/psychology/department/people/Meddis.html) in the hearing research laboratory at the University of Essex. This model has undergone refinements over a time period spanning four decades. Therefore, it would be foolish to describe it in detail in this blog post! However, an overview can be given that describes the processes most relevant to the design of BioAid.

The human auditory periphery (sound processing associated with the ear and low-level brain processing) is depicted in the abstract diagram below. The images represent the stages of processing in the auditory periphery that are modeled. The acoustic pressure waves enter the ear. The waveform in the diagram is the utterance '2841' spoken by a male talker. The middle ear converts these pressure fluctuations into a stapes displacement that drives the motion of fluid within the cochlear. In turn, this fluid motion results in the displacement of a frequency selective membrane, the Basilar membrane (BM), running the length of the cochlear spiral. Along the length of the BM are:

* Active structures that change the passive vibration characteristics of the membrane in a stimulus dependent manner.
* Transduction units that convert the displacement information at each point along the membrane into an electrical neural code that can be transmitted along the auditory nerve to the brain.

The image plot on the right shows the simulated neural code. The output of the process is made of multiple frequency channels (y-axis), each containing a representation of neural activity as a function time (x-axis). The output resembles a [spectrogram](http://en.wikipedia.org/wiki/Spectrogram) in its basic structure, although the non-linear processing makes it rather unique. For this reason, we call it the auditory spectrogram.

![Audiotory System](/images/BioAid_algo/auditory_system.png)

<small>
	Diagram showing peripheral auditory processes, including input and output data.
</small>

This biological system can be modeled of as a chain of discrete sequential processes. In general the output of each process just feeds into the next process in the sequence. The model takes an array of numbers representing the acoustic waveform as its input. This is then processed by an algorithm that converts the acoustic representation to the displacement of the stapes bone within the middle ear. Following this, there is an algorithm that converts the stapes displacement into BM displacement, taking the non-linearities caused by the active structures into account. At this point in the processing chain, the data is a multichannel, representing individual points along the BM. The final stage is a model of the transduction units, which converts the multichannel displacement information into a multichannel neural code representation.

The auditory model can then be used for various tasks. By making a model that can reproduce physical measurements, you can then use the model to predict the output of the system to all manner of different stimuli. For example, we know that the human auditory system is excellent at extracting speech information from noisy environments. By using the auditory model as a [front end for an automatic speech recognizer](http://asadl.org/jasa/resource/1/jasman/v132/i3/p1535_s1?isAuthorized=no), the modeler can investigate how the different components of the auditory periphery may contribute to this ability.


The dual resonance non-linear filterbank
-----------------------------------------

There are a number of different models of cochlear mechanics. The dual resonance non-linear filterbank (DRNL) is the model developed within the Essex lab. BioAid is basically a modified version of the latest incarnation of the DRNL model.

The basic DRNL model was designed to account for two major experimental observations. The first observation is the piecewise output (displacement) of the BM relative to the stapes displacement. This is shown by the diagram below. The basilar membrane displacement has a  linear relationship with stapes displacement at low stimulus intensities. For a large part of the auditory intensity range (approximately 20 dB to 80 dB SPL across most of the audible frequency range), the relationship between stapes and BM displacement is compressive, i.e. the BM displacement only increases by 0.2 dB per dB increase in stapes displacement. At very high stimulus intensities, there is a return to the linear relationship observed at low intensities.

![Stick](/images/BioAid_algo/obs1.png)

The second observation is related to the relationship between the frequency selectivity of the BM with level. Each point along the BM displaces maximally at a specific frequency. Parts of the BM near to the interface with the stapes (base) respond maximally to high frequencies, while the opposite end (apex) responds maximally to low frequencies. At low stimulus levels, the regions are very frequency selective and do not respond much to off-frequency stimulation. However, at higher stimulus intensities, the BM has a reduced frequency selectivity, meaning that the BM will be displaced by a proportionately greater amount by off frequency stimuli when they have high intensity.

![Filter](/images/BioAid_algo/obs2.png)

The DRNL is a parallel filterbank model, in that each position (interchangeable with frequency band) along the BM is modeled using a an independent DRNL section. The DRNL models observations described above using two independent processing pathways per frequency band.


BioAid Architecture
-------------------



The image below shows a block diagram of the various components

* Flow diagram of the auditory periphery
* Discussion of the
* Flow diagram from presentation
* Discuss things in terms of auditory periphery here - only describe the necessary bits and reference where possible
* asd


The properties of this system
-----------------------------

* Designed for a closed fit - actually makes some sounds quieter
* The SNR point I made
* The transfer from MOC to instant
* DFAC - the delay always means that onsets will be instantaneously compressed
* The reduction of distortion products (and also their potential uses and benefits)

 

Wrap up
-------

* Hint at lab-based research
* -- Loudness scaling
* -- Tuning curves
* This is a blog - it is dynamic and can be updated as info available