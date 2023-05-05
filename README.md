Download Link: https://assignmentchef.com/product/solved-ece310-project-1-sampling-rate-conversion
<br>
In discrete‐time signal processing, it is often necessary to convert a signal from one sampling rate to another. A common example is the conversion from the sampling rate of a compact disk (CD); signal (44.1 KHz) to that of a Digital Audio Tape (DAT) signal (48 KHz). Another example is the aud io standard in High‐Definition Television (HDTV) transmission, where at least three sampling rates are supported (32, 44.1, and 48 KHz). Although in principle we may convert the signal back to analog form and resample at the desired rate, it is usually preferable to perform t he entire conversion digitally. This is clue to many considerations including the fact that conversion to analog form often introduces noise in the signal, and that digital signal processing can be much more cost‐effective and flexible.

This MATLAB project asks you to perform a sampling rate conversion on segments of audio signals. The input audio signals are quantized to 8 bits and sampled with a sampling frequency of 11,025 Hz. You are required to convert the signal to a sampling rate of 24,000 Hz in a computationally efficient manner. Although one conceptual way of realizing this sampling rate conversion process is to upsample the signal, lowpass filter, and downsample it, a more clever implementation can lead to an implementation that is many times more efficient. To do this, you can exploit various aspects of class to optimize the system, such as multistage filter implementation, filter design, and polyphase implementation. By the end of the project, you hopefully will have a much better understanding of both the theoretical aspect of the system as well as various issues in implementing a practical DSP system at a software level.

<h1>Project Goal</h1>

<em>L</em>

A sampling rate converter which produces an output signal with a sampling rate which is  times the

<em>M</em>

original sampling rate can be specified as shown in Figure 1.

Figure 1: Sampling rate conversion system.

For an ideal sampling rate converter, the lowpass filter in Figure 1 is an ideal lowpass filter with cutoff

⎛π π⎞ frequency ω<em><sub>c </sub></em>= min⎜ , ⎟ The goal of this project is to design an efficient DSP algorithm that

⎝<em>M L</em>⎠

implements the system in Figure 1 subject to the following constraints:

<ul>

 <li>The system performs the correct sampling rate conversion from 11,025 Hz to 24,000 Hz. In particular, do not assume that 11,025 Hz ≈ 11,000 Hz.</li>

</ul>

If the system in Figure 1 is an ideal sampling rate converter , when the input <em>x[n]</em> is a unit impulse δ[ ]<em>n </em>, the output <em>y[n]</em> has a Fourier transform <em><sup>Y e</sup></em>( <em><sup>j</sup></em><sup>ω</sup>) which corresponds to an ideal lowpass filter with

a cutoff frequency at ω<sub>c</sub> = (11,025/ 24,000<em>)</em>π<em>. </em>For an equivalent system which you are to implement, when the input <em>x[n]</em> is a unit impulseδ[ ]<em>n </em><em>, </em>the output <em>y[n] </em>must have a Fourier transform <em><sup>Y e</sup></em>( <em><sup>j</sup></em><sup>ω</sup>) which is an approximation of a lowpass filter, and meets the specifications shown below in Table 1.

<table width="495">

 <tbody>

  <tr>

   <td width="163">Passband Cutoff (ω<sub>p</sub>)</td>

   <td width="331"><em> </em></td>

  </tr>

  <tr>

   <td width="163">Passband Ripple</td>

   <td width="331">±0.1 dB or less</td>

  </tr>

  <tr>

   <td width="163">Stopband Frequency (ω<sub>s</sub>)</td>

   <td width="331">1.2ω<sub>p</sub></td>

  </tr>

  <tr>

   <td width="163">Stopband Attenuation</td>

   <td width="331">70 dB or more</td>

  </tr>

  <tr>

   <td width="163">Phase Constraints</td>

   <td width="331">|max grpdelay ‐ min grpdelay| ≤ 720 in the passband</td>

  </tr>

 </tbody>

</table>

Table 1

If your sampling rate conversion system functions properly, you should meet the specifications in Table 1, and when you play the output audio signal at 24,000 Hz, it should sound the same as the input audio signal played at 11,025 Hz.

You should get a rough estimate of the efficiency of your design by determining how much computation was required to perform the sampling rate conversion on the Wagner.wav signal. The method which you will use to count the number of operations will be described shortly.

You are to write a MATLAB function srconvert such that the command srconvert(in) takes the input signal in with an associated sampling rate of 11,025 Hz, and returns an output signal at a sampling rate of 24,000 Hz. (See the section on writing MATLAB functions.)  Once your function

srconvert.m is finalized, run the command y=srconvert([1 zeros(1,3000)]);. This will produce a vector y which contains the response of your system to a unit impulse. Then call verify(y) to verify that your design meets the design specification.

<h1>The Files</h1>

The project zip file contains the audio files and MATLAB functions which you will need for this project. To access the zip file, click on the link below where you found this file on the web site.

In testing your system, you may find it helpful to first use a unit impulse as input to your system and then later try using real audio signals as inputs and listening to the outputs.  To load a sound file into a vector x, type x=wavread(′filename.wav′);. To write a sound file for the MATLAB vector x into the current directory, type wavwrite(x,sampfreq, ′filename.wav′). You may test your system using any of the audio signals, although you should use the Wagner.wav signal to benchmark your system performance.

<h1>MATLAB Utilities</h1>

You may find the following MATLAB functions useful for your design.

examlpf(h, wp, vs) ‐ allows you to examine the passband ripple, the group delay, as well as the stopband attenuation of the lowpass filter whose impulse response is h. It generates three simultaneous plots. The first one zooms in on the passband with cutoff frequency wp. The second plot measures the group delay in the passband, and the third plot is simply the magnitude response over the entire frequency range <em>(-</em>π<em>,</em>π). The passband (wp) and stopband (ws) cutoff frequencies are normalized by π/2. That is, a cutoff frequency at π/2 should be entered as 0.5.

poly1(h, M) ‐ returns a matrix E whose <em>i</em><sup>th</sup>  row corresponds to the <em>i</em><sup>th</sup>  polyphase components of the FIR filter h, obtained via type I decomposition with downsampling factor M.

fftfilt(h , x) ‐ convolves the signal x with the filter h using the FFTs. Beware that this command might not always leads to a more efficient implementation than conv(h ,x), depending on the length of the signals. upsample(h ,L) ‐ returns an upsampled version of h by a factor L, without any interpolation. downsample(h ,M) ‐ downsamples h by M.

<h1>Computation Counts</h1>

MATLAB does not automatically record the number of floating point operations (flops) in your algorithm, so to measure your computational efficiency, you’ll have to determine the required number of multiplies and adds yourself.  You can make this process somewhat automated by inserting a counter to be updated each time the filter function is called, but you should also do a hand calculation to double check.

Do not include in the flops count the computation involved in designing any filters you need.

<h1>Example MATLAB Function</h1>

To give you an example of how to write a MATLAB function, here is the code from the file poly1.m.

function E=poly1(h,M)

%

% Performs type I polyphase decomposition of h in M components. % The ith row of E corresponds to the ith polyphase component. % Assumes that the first point of h is index 0.

<em>% </em>h = [h zeros(1, ceil(length(h)/M)*M-length(h))];

E = reshape(h, M, length(h)/M);

<h1>Tips</h1>

The old homework problem in the Appendix might be useful as a guideline to efficient implementation of an interpolation filter. You may also find useful page 85 of the Vaidyanathan paper cited below.

<ol start="1990">

 <li>P. Vaidyanathan, “Multirate digital filters, filter banks, polyphase networks, and applications: A tutorial,” Proceedings of the IEEE, vol. 78, pp. 56‐92, Jan. 1990. <a href="http://www.systems.caltech.edu/dsp/ppv/papers/ProcIEEEmultirateTUTExtra.pdf">http://www.systems.caltech.edu/dsp/ppv/papers/ProcIEEEmultirateTUTExtra.pdf</a> (Available 7/08)</li>

</ol>

<strong>Appendix – An Old Homework Problem</strong><strong><sub>:</sub></strong>




Let <em>x</em>[ ]<em>n</em> represent a signal which is obtained by sampling a continuous time audio signal <em>x</em><em><sub>c </sub></em>( )<em>t</em> using an ideal C/D

converter shown in Figure A1. Assume that the sampling rate is 1/<em>T</em>= 44.1 kHz .




<em>T</em>




Figure A1




We wish to design a 4x (4 times) oversampling digital FIR interpolation filter. One way to do this is to use the single‐stage design of System 1 shown in Figure A2.




<h2>x[ ]n</h2>







Figure A2:  System 1




The filter <em>hn</em>[ ] with Fourier transform <em>H e</em>( <em><sup>j</sup></em><sup>ω</sup>) is designed using the window method with a Kaiser window.

Suppose the filter <em>hn</em>[ ] is designed to meet Specification 1:

Specification 1:

1−δ≤ <em>H e</em>( <em><sup>j</sup></em><sup>ω</sup>) ≤ +1 δ ω ω, ≤ <sub>1</sub>




<em>H e</em>( <em><sup>j</sup></em><sup>ω</sup>) ≤δ,           ω ω<sub>2 </sub>≤ ≤π

where ω<sub>1 </sub>= 0.23π, ω<sub>2 </sub>= 0.27π, and δ=10<sup>−</sup><sup>4 </sup>.

<ul>

 <li>Using a Kaiser window, estimate the length of the filter <em>hn</em>[ ] which meets Specification 1.</li>

 <li>Using the filter length estimate for <em>h n</em>[ ] in part(a), estimate the number of multiplications per second</li>

</ul>

required in System 1 if the system is implemented in polyphase form using four polyphase components.




An alternate way to design the 4x oversampling interpolation filter is to use the two‐stage design of System 2 shown in Figure A3.




Stage 1                                                                   Stage 2




Figure A3: System 2




The filters <em>h n</em><sub>1</sub>[ ]and <em>h n</em><sub>2</sub>[ ] with frequency responses <em>H e</em><sub>1</sub>( <em><sup>j</sup></em><sup>ω</sup><sup>)</sup> and <em>H e</em><sub>2</sub>( <em><sup>j</sup></em><sup>ω</sup>) , respectively, are designed using the window method with Kaiser windows. Suppose that these filters are designed to meet Specification 2.




Specification 2:

1−δ′ ≤ <em>H e</em><sub>1</sub>( <em><sup>j</sup></em><sup>ω</sup>) ≤ +1 δ ω′,  ≤ 2ω<sub>1</sub>

<em>H e</em><sub>1</sub>( <em><sup>j</sup></em><sup>ω</sup>) ≤δ′,              2ω ω π<sub>2 </sub>≤  ≤




1−δ′ ≤ <em>H e</em><sub>2</sub>( <em><sup>j</sup></em><sup>ω</sup>) ≤ +1 δ′, ω ≤ω<sub>1 </sub><em>H e</em><sub>2</sub>( <em><sup>j</sup></em><sup>ω</sup>) ≤δ′, π ω ω π− <sub>1 </sub>≤ ≤

where δ′ =     1+δ−1,ω<sub>1 </sub>, ω<sub>2 </sub>, and δare given previously.




<ul>

 <li>Show that System 2 is equivalent to System 1 with</li>

</ul>

<h2>                                                                           H e( jω)=H e H e1( jω) 2( jω)</h2>

<ul>

 <li>Show that if the filters <em>h n</em><sub>1</sub>[ ]and <em>h</em><sub>2</sub>[ ]<em>n</em> are designed using Kaiser windows to meet Specification 2, then the resulting equivalent system in System 1 will have a filter <em>H e</em>( <em><sup>j</sup></em><sup>ω</sup>) which meets Specification 1. (You may assume that the filters designed have magnitude responses which are monotonically decreasing in the transition band from the passband to the stopband).</li>

 <li>Using Kaiser windows, estimate the lengths of the filters <em>h n</em><sub>1</sub>[ ]and <em>h</em><sub>2</sub>[ ]<em>n </em>which meet Specification 2.</li>

 <li>Using the filter length estimates for <em>h n</em><sub>1</sub>[ ]and <em>h n</em><sub>2</sub>[ ]in part (e), estimate the number of multiplications per second required in System 2 if each stage is separately implemented in polyphase form using two polyphase components. What are the computational savings in multiplications, if any, over System 1?</li>

</ul>


