---
layout: post
title: "BioAid part 2: From auditory model to hearing aid algorithm"
date: 2013-01-29 14:54
comments: false
published: false
categories: 
---

In a [previous blog post](/blog/2013/01/24/bioaid-algo/), I introduced the BioAid project and discussed some of the motivations. I advise reading that post before reading this one. In this second installment, I aim to describe the algorithm architecture in technical detail and then discus some of its properties. This information is placed on my blog, allowing me to rapidly, and informally communicate some of the technical details related to the project while I gather thoughts in preparation for a more rigorous account.


Modeling the Auditory Periphery
--------------------------------

The architecture of the BioAid algorithm is based on a [computational model of the auditory periphery](http://www.essex.ac.uk/psychology/department/HearingLab/modelling.html), developed by, and under the supervision of [Prof. (Emeritus) Ray Meddis](http://www.essex.ac.uk/psychology/department/people/Meddis.html) in the hearing research laboratory at the University of Essex. This model has undergone refinements over a time period spanning four decades. Therefore, it would be foolish to describe it in detail in this blog post! However, an overview can be given that describes the processes most relevant to the design of BioAid.

The human auditory periphery (sound processing associated with the ear and low-level brain processing) is depicted in the abstract diagram below. The images represent the stages of processing in the auditory periphery that are modeled. The acoustic pressure waves enter the ear. The waveform in the diagram is a time domain representation of the utterance '2841' spoken by a male talker. The middle ear converts these pressure fluctuations into a stapes displacement that drives the motion of fluid within the cochlear. In turn, this fluid motion results in the displacement of a frequency selective membrane, the Basilar membrane (BM), running the length of the cochlear spiral. Along the length of the BM are:

* Active structures that change the passive vibration characteristics of the membrane in a stimulus dependent manner.
* Transduction units that convert the displacement information at each point along the membrane into an electrical neural code that can be transmitted along the auditory nerve to the brain.

The image plot on the right shows the simulated neural code. The output of the process is made of multiple frequency channels (y-axis), each containing a representation of neural activity as a function time (x-axis). The output resembles a [spectrogram](http://en.wikipedia.org/wiki/Spectrogram) in its basic structure, although the non-linear processing makes it rather unique. For this reason, we call it the auditory spectrogram.

![Audiotory System](/images/BioAid_algo/auditory_system.png)

<small>
	Diagram showing peripheral auditory processes. The input is shown on the left, and is processed to produce the output shown on the right.
</small>

This biological system can be modeled of as a chain of discrete sequential processes. In general the output of each process just feeds into the next process in the sequence. The model takes an array of numbers representing the acoustic waveform as its input. This is then processed by an algorithm that converts the acoustic representation to the displacement of the stapes bone within the middle ear. Following this, there is an algorithm that converts the stapes displacement into multi-channel representation of BM displacement along the cochlear partition. The final stage is a model of the transduction units, which converts the multichannel displacement information into a multichannel neural code representation.

The auditory model can then be used for various tasks. By making a model that can reproduce physical measurements, you can then use the model to predict the output of the system to all manner of different stimuli. For example, we know that the human auditory system is excellent at extracting speech information from noisy environments. By using the auditory model as a [front end for an automatic speech recognizer](http://asadl.org/jasa/resource/1/jasman/v132/i3/p1535_s1?isAuthorized=no), the modeler can investigate how the different components of the auditory periphery may contribute to this ability.


The basic dual resonance non-linear filterbank
----------------------------------------------

There are a numerous models of cochlear mechanics. The dual resonance non-linear filterbank (DRNL) is the model developed within the Essex lab. BioAid is fundamentally a modified version of the latest incarnation of the DRNL model.

The basic DRNL model was designed to account for two major experimental observations. The first observation is the piecewise output (displacement) of the BM relative to the stapes displacement. This is shown by the diagram below. The basilar membrane displacement has a  linear relationship with stapes displacement at low stimulus intensities. For a large part of the auditory intensity range (approximately 20 dB to 80 dB SPL across most of the audible frequency range), the relationship between stapes and BM displacement is compressive, i.e. the BM displacement only increases by 0.2 dB per dB increase in stapes displacement. At very high stimulus intensities, the relationship is linear, like at low intensities.

![Stick](/images/BioAid_algo/obs1.png)

<small>
	Illustration of the BM 'Broken Stick' non-linearity. The x-axis is the input stapes displacement and the y-axis is the output BM displacement.
</small>

The second observation is related to the relationship between the frequency selectivity of the BM with level. Each point along the BM displaces maximally at a specific frequency. Parts of the BM near to the interface with the stapes (base) respond maximally to high frequencies, while the opposite end (apex) responds maximally to low frequencies. For this reason, different regions along the basilar membrane can be thought of as filters. At low stimulus levels, the regions are very frequency selective and do not respond much to off-frequency stimulation. However, at higher stimulus intensities, the BM has a reduced frequency selectivity, meaning that the BM will be displaced by a proportionately greater amount by off frequency stimuli when they have high intensity. Not only does the bandwidth of the auditory filters change with stimulus intensity, but the centre frequency (or best frequency) also shifts.

![Filter](/images/BioAid_algo/obs2.png)

<small>
	Illustration of level dependent frequency selectivity. Each line shows data from a different stimulus intensity. The x-axis is stimulus frequency and the y-axis is BM displacement for a fixed position along the membrane. 
</small>

The DRNL is a parallel filterbank model, in that each cochlear channel along the BM is modeled using a an independent DRNL section. Each frequency channel of the DRNL model is comprised of two independent processing pathways. These pathways share a common input and the outputs of the pathways are summed to give the final displacement value for the location along the BM being modeled. The linear pathway is made of a linear gain function and a bandpass filter. The nonlinear pathway is made of an instantaneous broken-stick non-linearity sandwiched between two bandpass filters. The filters are tuned according to the position along the BM being modeled. This arrangement is shown by the diagram below.

![Filter](/images/BioAid_algo/DRNL.png)

<small>
	Schematic showing one frequency channel of the DRNL model
</small>

The intention of the linear pathway is to simulate the passive mechanical properties of the cochlear. Therefore, the output of this pathway in isolation would give the BM displacement if the active structures in the cochlear were not functioning. Conversely, the non-linear pathway is the contribution from the active mechanisms to the displacement. The 3-part piecewise relationship between BM and stapes displacement can be modeled by just summing the responses of the pathways. When performing decibel addition, the sum value is approximately the greater of the two values being summed. The output of each pathway is shown below, along with the sum total. The parameters are tuned so that the output of the model can reproduce experimental observations of BM displacement.

![Filter](/images/BioAid_algo/DRNL_IO.png)

<small>
	The green line is the input-output (IO) function relating stapes to BM displacement of the linear pathway of the DRNL model. The blue line is the IO function for the non-linear pathway. The red line is the decibel sum of the two pathways.
</small>

The DRNL model can also reproduce the level dependent frequency selectivity data using this architecture. For this, the filters in the two pathways are tuned slightly differently. As the level of stimulation increases, the contribution of the linear pathway becomes significant. By using different filter tunings, it is possible to make a level dependent frequency response using this combination of linear filters. 


The latest dual resonance non-linear filterbank
------------------------------------------------

The active structures in the cochlear that give rise to the non-linear relationship between stapes- and BM-displacement are subject to control by a frequency-selective feedback pathway originating in the brain. When there is neural activity in this feedback pathway, the contribution of the active structures to BM displacement is reduced. The level of activity in the feedback network is at least partially reflexive: the feedback is activated when the acoustical stimulation intensity passes a certain threshold within a given frequency band, and grows with increasing stimulus intensity.

Robert Ferry showed (in his PhD thesis and subsequent publications), that the action of the biological feedback network could be simulated by attenuating the input to the non-linear pathway of the DRNL model. The cochlear and neural transduction processes have limited dynamic ranges, and there is growing evidence to suggest that the feedback modulated attenuation may assist a listener by optimally regulating the cochlear operating point for given background noise conditions.

We subsequently went on to complete the feedback loop in the computer model. This was achieved by deriving a feedback signal from the simulated neural information to modulate the attenuation value. This complete feedback model can then adjust the attenuation parameter over time to regulate the cochlear operating point in accordance with changes in the acoustical environment. Data from automatic speech recognition experiments have shown that machine listeners equipped with the feedback network consistently outperform (i.e. correctly identify a greater proportion of the speech material) machine listeners without the feedback network in a variety of background noises. 

![feedback](/images/BioAid_algo/fback.png)

<small>
	Diagram depicting the latest version of the DRNL model. The feedback signal is derived from the neural data after displacement to neural transduction stage (T). This feedback signal is used to modulate the amount of attenuation applied to the non-linear pathway over time.
</small>


Simulating hearing impairment
-----------------------------

Some origins of hearing impairment are a result of a malfunction of certain parts of the auditory periphery. Some components of the auditory periphery are far more susceptible to failure (or reduced functionality) than others. The two most likely culprits are a reduction in the function of the active structures in the cochlear that alter the BM displacement, and/or a reduction in the effectiveness, or complete loss, of the structures that convert BM displacement into neural signals for the brain.

![feedback_simplified](/images/BioAid_algo/fback_simple.png)

<small>
	Simplified diagram of the DRNL model to highlight the impact of reduced peripheral component functionality on cochlear feedback.
</small>

Firstly, consider the case where the transduction units within a given channel are not functioning properly. Not only is there going to be an adverse effect on the quality of the information transmitted via this channel to the brain, but the feedback loop which is driven by the neural information will also not function optimally, thus compounding the problem.

Secondly, consider the case there the active structures are not functioning correctly. This will result in a reduced BM displacement for a given level of stapes displacement. The output of the transduction units will therefore be reduced, and so the feedback will be derived from a sub-optimum signal. To make things worse, any residual feedback signal will not be effective because the feedback signal modulates the action of the active components, which in this case are not functioning correctly anyway.

BioAid is designed to artificially replace the peripheral functionality that may be reduced or missing for hearing impaired listeners. By simulating the non-linear pathway and feedback loop, it may be able to regulate the cochlear operating point of an impaired listener in a way that would not be achievable using a standard hearing aid.


BioAid Architecture
-------------------

![algo picture](/images/BioAid_algo/arch.png)

<small>
	The image above shows the architecture of the BioAid algorithm in block form. ONly 4 channels are displayed for simplicity.
</small>


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