---
layout: post
title: "BioAid: The algorithm behind the biologically inspired hearing aid"
date: 2013-01-24 14:48
comments: true
published: false
categories: 
---

Just before Christmas, I submitted a free app ([BioAid](http://itunes.com/apps/nicholasclark)) to the Apple app store that turns an iOS device into a hearing aid. It does this by taking the audio stream from the internal microphone, processing the audio in real time, and then playing the audio back over headphones connected to the device. This blog article discusses the audio processing that occurs behind the scenes. For more general information on usage, please visit the [main BioAid site](http://bioaid.org.uk). This information is placed on my blog, allowing me to communicate some of the underlying architecture quickly and informally while I gather thoughts in preparation for a more rigorous and hopefully publishable account of the algorithm.

| ![BioAid Screenshot](/images/BioAid_algo/Screenshot_A_web.png)  | --> Screenshot of the BioAid iOS app|


BioAid is not some gimmicky sound amplifier app. The development and evaluation of the algorithm has been conducted by a team of researchers within the [hearing research laboratory](http://www.essex.ac.uk/psychology/department/HearingLab/Welcome.html) at the University of Essex. Laboratory tests with hearing-impaired volunteers are still in progress. These tests are being conducted using a 'lab-scale' version of BioAid, comprised of standard behind the ear (BTE) hearing aids (called satellites) that are connected to a laptop computer. The signal processing that would normally occur within the hearing aid is offloaded to the laptop, making it easier for us to change the parameters in the hearing aid and even tweak the algorithm itself. Another avenue of research uses the algorithm to pre-process acoustic stimuli in an off-line mode (not real time) before they are presented to listeners over headphones. Therefore, in my opinion, it is important to think of BioAid as an algorithm concept, rather than pigeon hole it as an iOS app. The BioAid algorithm has potential for use in many applications, and the iPhone app is just one form in which BioAid exists. One of the numerous motivations for making the iPhone implementation was that it might inspire others to use the algorithm in unusual ways, perhaps for processing speech in a VIOP application, or as a hack for a media centre, allowing film and television audio to be processed at the source. This is why the source is freely available at [GitHub](http://github.com/audioplastic/BioAid). There is also a [Facebook page](http://facebook.com/bioaidapp) that I encourage anyone interested in the project to 'like' so that they can be periodically informed of developments.


Generic hearing aid 'gain model'
--------------------------------

The first thing to mention here, is that modern hearing aids contain all manner of signal processing wizardry to help with general audibility of sound, and also to help alleviate some of the exhaustion associated with the increased listening effort required from impaired listeners to extract information from sounds of interest in cacophonous environments. Among others, processing strategies include components such as noise reduction algorithms, speech enhancement algorithms, microphone arrays with beam forming algorithms to reduce off-axis sound interference, and feedback cancellation algorithms to prevent howl associated with high gain settings in conjunction with open (non occluded) fittings. Some hearing aids even transpose information from different frequency bands to others, but these technologies are not related to the core BioAid processing.

At the heart of any hearing aid is the 'gain model', and the BioAid algorithm falls into this category. The most basic goal of any hearing assistive device is to restore audibility of sounds that were previously inaudible to the hearing-impaired listener. Hearing impaired listeners have a reduced sensitivity to environmental sounds, i.e. they cannot detect the low level sounds that a normal hearing listener would be able to detect, and so it can be said that their thresholds of hearing are relatively high, or raised. To compensate for this deficit, the intensity of the stimulus must be increased, i.e. gain is provided by the hearing aid. The earliest hearing aids (the ear trumpet) just provided gain.

It is important to note that a flat loss (equal loss of sensitivity across frequency) is not often observed. More commonly, there is a distinct pattern of hearing loss, where the sensitivity is different to that of normal hearing listeners at different frequencies. For a hearing aid to work effectively across the audible spectrum, it must provide differing amounts of gain in different frequency regions. Modern hearing aids decompose sounds into separate frequency bands, perform various processing tasks, then finally recombine the signal into a waveform that can be presented to the listener via a loudspeaker. BioAid processing is no different to current hearing aids with regard to this general principle.

Most hearing impaired listeners will begin to experience discomfort from loud sounds at levels not too dissimilar to those with a normal hearing sensitivity<sup>\*</sup>. This means that the impaired listener has a reduced dynamic range into which the important sonic information must be squeezed. If the hearing aid applies a linear gain irrespective of the incoming sound intensity, it will help the listener detect quiet sounds, but it will also make loud sounds unbearably loud. For this reason, modern hearing aids also use compression algorithms. A lot of gain is applied to low intensity sounds to help with audibility, while considerably less gain is applied to high intensity sounds to preserve listener comfort. 

Unfortunately, any non-linear process (including dynamic range compression) applied to the processing chain will have side effects. In order to protect the listener from sudden loud sounds, the compression algorithm needs to respond quickly. However, standard compression algorithms with rapid temporal acuity tend to make the acoustical environment sound distinctly unnatural. The action of the compressor is clearly audible and can interfere with the important information contained in the amplitude modulations of signals such as speech. Fast compression reduces the modulation depth of amplitude modulates signals, and can therefore reduce our ability to extract information from the glimpses of signal information we might otherwise receive during the low intensity dips in modulated masking sounds. *Very* fast compression also changes the signal to noise ratio (SNR) of steady state signal and noise mixtures. At positive SNRs, the signal is of greater amplitude than the noise signal. If compression is so fast that it works near instantaneously, then the high level peaks of the signal will not be amplified as much as the lower level peaks in the noise signal. The noise level will increase relative to the level of the signal information reducing an otherwise advantageous SNR. This negative effects on speech intelligibility caused by this effect are then compounded by any distortion introduced by the compression process. In contrast, slowly acting compression algorithms do not impose so many negative side effects. A very slow compressor acts like a person with sensitive neighbors riding the volume control of an amplifier while watching a movie: the gain is increased for the quiet spoken passages, and then decreased in the loud action sequences. This works well, and the sound quality is not really altered. The problem comes when the volume is cranked up for quiet spoken passages, when all of a sudden there is an intense crashing noise that nearly deafens the audience (and has the neighbors slamming on the ceiling). For this reason, both fast and slow acting compression algorithms are used in modern hearing aids to get the best possible compromise\*\*. BioAid also utilizes fast and slow acting compression.

If BioAid is a multi-band compressor with both slow and fast acting components, then how is it different to current hearing aid gain models? On the surface, BioAid looks like more of the same, but the architecture is certainly unique, and this gives it some unique properties. Intrigued? Read on.

<small>
	<sup>\*</sup>This is with the exception of those whose hearing is affected by a problem with the transfer of energy through the middle ear, who will generally have an increased discomfort threshold in addition to a raised detection threshold. It is also worth noting that many hearing impaired listeners have a *lower* discomfort threshold than that of normal hearing listeners. This condition is known as [hyperacusis](http://en.wikipedia.org/wiki/Hyperacusis) and is an area of active research.
</small>

<small>
	<sup>\*\*</sup>Modern digital hearing aids generally work by processing blocks (or frames) of samples. Each block of samples is processed and the output buffer is filled before the next block of samples arrives. This a slight delay between the incident acoustic signal and the processed signal. This delay is generally undesirable, but it can be used to good effect. It gives the compression algorithm the opportunity to 'look ahead' a few samples and adjust its parameters in an optimum way given the information about 'future' events.
</small>



Technical motivation for BioAid
-------------------------------

BioAid is unique in that the algorithm has been designed from the ground up to mimic the processes that occur in the ear. Hearing aid technology has generally evolved to solve problems with each generation of algorithm design. This incremental approach provides an increasingly refined product. However, the problem with extended design and refine methods of development, is that the returns from each design revision generally tend to diminish. There is an asymptote. This partly explains why so much effort is now expended on the development of peripheral technologies in hearing aids, away from the core gain model. Machine hearing is a related field in which performance improvements are becoming harder to obtain using refinements of standard methods. In that field, there is a change going on, whereby radically different signal processes are being researched that are based on more physiologically accurate models of human hearing. Following in this revolutionary zeitgeist, BioAid is an effort to break through a current intellectual plateau in hearing aid gain model design.

The human auditory periphery (sound processing associated with the ear and low-level brain processing) can be modeled of as a chain of discrete sequential processes. In general the output of each process just feeds into the next process in the sequence. There are also some feedback signals that originate in processes situated further along the chain that modulate the behavior of the earlier-stage systems. The PhD thesis of [Manasa Panda]() demonstrates that it is possible to model common hearing pathologies by reducing the functionality of, or completely removing some of the processing blocks in the chain. This modified model is called a ['Hearing Dummy'](http://www.electronicsweekly.com/Articles/14/06/2011/51255/ear-simulation-leads-to-unique-hearing-aid.htm), as the models of the periphery can be tailored to individual listeners. An artificial (machine) listener will make the same responses in hearing tests as the human when connected to their personalized Hearing Dummy.

Having isolated the components of the model likely to cause the listening difficulties, we then thought it might be a good idea to replicate those processes in a hearing aid. This could be to assist some residual functionality of certain auditory components, or to completely replace lost functionality of others. BioAid can be thought of as a simplified auditory model, containing a chain of models of the components most susceptible to the malfunctions responsible for hearing impairments.

There is one major difference between BioAid and the peripheral model used in the lab. In a standard model of the auditory periphery, the output is a code made of neural spikes representing the transformed sound information. Information in this form is useful for higher stages of brain processing with the correct interface, but it cannot be played back through a hearing aid. BioAid must deviate from the physiological model in the end, as the sound must be recombined into a waveform that can be presented to the listener acoustically. Apart from this necessary deviation, we aim to remain faithful to the physiological model. This allows us to observe emergent properties of the system, rather than deliberately engineering properties into it.


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

