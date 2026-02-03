 hey, have raw data from a wav file and a sample rate. i want to Plot the signal with time on the x-axis and magnitude on the y-axis using pyplot

    Code completed
    Response finalized

Python

import os

# List files in the current directory
files = os.listdir('.')
print(files)


Code output

[]

To plot a WAV signal with time on the x-axis, you need to convert the sample indices into time units using the sample rate (fs​). The formula for the time vector is:
t=fs​n​

where n is the sample index (0,1,2,…,N−1).

Here is the Python code to perform this using matplotlib and scipy:
Python

import matplotlib.pyplot as plt
import numpy as np
from scipy.io import wavfile

# 1. Load the data
# Replace 'your_file.wav' with your actual file path
sample_rate, data = wavfile.read('your_file.wav')

# 2. Handle multi-channel (stereo) data
# If the audio has two channels, we'll just take one for the plot
if len(data.shape) > 1:
    data = data[:, 0]

# 3. Create the time axis
length = data.shape[0] / sample_rate
time = np.linspace(0., length, data.shape[0])

# 4. Plotting
plt.figure(figsize=(10, 4))
plt.plot(time, data, color='blue')
plt.xlabel("Time (s)")
plt.ylabel("Magnitude")
plt.title("WAV Signal Magnitude vs Time")
plt.grid(True)
plt.tight_layout()

# Save or show the plot
plt.savefig('wav_plot.png')
plt.show()

Key Steps Explained:

    wavfile.read: This returns the sample rate (samples per second) and the raw data as a NumPy array.

    Time Vector: np.linspace(0., length, data.shape[0]) creates an array of time values starting from 0 to the total duration of the audio.

    Stereo vs. Mono: If your WAV file is stereo, the data array will be 2D. The code above selects the first channel (data[:, 0]) to keep the plot simple.

    Magnitude: The y-axis represents the instantaneous amplitude (magnitude) of the signal, which is usually represented by 16-bit integers (−32768 to 32767) or normalized floats (−1.0 to 1.0).

no, heres what i have: #%matplotlib widget

import matplotlib.pyplot as plt

import numpy as np


# import local library

import sys


# path for class directory (will have data and libraries)

class_dir = "/SI486H/"

sys.path.append(class_dir)


# import library for sound functions

from sound_lib import * # read in the signal rate and the data

# YOUR CODE HERE

sample_rate, data = grab_raw_data(path, format="wav")

Since you already have the sample_rate and data variables loaded, you just need to generate a time array that matches the length of your data.

To do this, you can use np.arange or np.linspace combined with your sample_rate. Here is the code to finish your script and generate the plot:
Python

# YOUR CODE HERE
# 1. Calculate the total number of samples
num_samples = len(data)

# 2. Create the time axis (Time = Index / Sample Rate)
# This creates an array from 0 to total_duration
time = np.arange(num_samples) / sample_rate

# 3. Plot the signal
plt.figure()
plt.plot(time, data)

# 4. Add labels and title for clarity
plt.xlabel("Time (seconds)")
plt.ylabel("Magnitude")
plt.title("Signal Waveform")
plt.grid(True)
plt.show()

Why this works:

    The X-axis: Each index in your data array represents a single "moment" in time. Since the sample_rate tells you how many samples occur in one second, dividing the index by the rate gives you the exact timestamp in seconds.

    %matplotlib widget: Since you have this at the top of your script, your plot should be interactive, allowing you to zoom in on specific parts of the waveform to see individual oscillations.

A Quick Tip on Data Shape:

If your data contains two columns (stereo), plt.plot(time, data) might try to plot both or throw an error depending on the dimensions. If you only want to see one channel, use data[:, 0] in the plot command: plt.plot(time, data[:, 0])

Would you like to know how to zoom into a specific time window (e.g., just the first 100ms) to see the wave shape more clearly?

Hey! Now having a problem playing the raw data. here is what I am getting: Expression 'paInvalidSampleRate' failed in '/home/conda/feedstock_root/build_artifacts/portaudio_1730363929490/work/src/hostapi/alsa/pa_linux_alsa.c', line: 2050Expression 'PaAlsaStreamComponent_InitialConfigure( &self->playback, outParams, self->primeBuffers, hwParamsPlayback, &realSr )' failed in '/home/conda/feedstock_root/build_artifacts/portaudio_1730363929490/work/src/hostapi/alsa/pa_linux_alsa.c', line: 2724Expression 'PaAlsaStream_Configure( stream, inputParameters, outputParameters, sampleRate, framesPerBuffer, &inputLatency, &outputLatency, &hostBufferSizeMode )' failed in '/home/conda/feedstock_root/build_artifacts/portaudio_1730363929490/work/src/hostapi/alsa/pa_linux_alsa.c', line: 2845

---------------------------------------------------------------------------PortAudioError Traceback (most recent call last)Cell In[4], line 3 1 # play the data 2 # YOUR CODE HERE----> 3 play_raw_data(data, sample_rate)File /SI486H/sound_lib.py:37, in play_raw_data(raw_data, sample_rate) 28 ''' 29 plays the signal with the given sample rate on the computer's speakers 30 input: (...) 34 none 35 ''' 36 # play using sounddevice library---> 37 sd.play(raw_data, sample_rate) 39 # Wait for playback to finish (important, or the script will quit early) 40 sd.wait()File ~/miniforge3/envs/si486h/lib/python3.13/site-packages/sounddevice.py:178, in play(data, samplerate, mapping, blocking, loop, **kwargs) 175 ctx.write_outdata(outdata) 176 ctx.callback_exit()--> 178 ctx.start_stream(OutputStream, samplerate, ctx.output_channels, 179 ctx.output_dtype, callback, blocking, 180 prime_output_buffers_using_stream_callback=False, 181 **kwargs)

...

2820 hosterror_info = host_api, info.errorCode, hosterror_text 2821 raise PortAudioError(errormsg, err, hosterror_info)-> 2823 raise PortAudioError(errormsg, err)PortAudioError: Error opening OutputStream: Invalid sample rate [PaErrorCode -9997]

Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings... when I run the following code: # read in the signal rate and the data

# YOUR CODE HERE

sample_rate, data = grab_raw_data(path, format="wav")

print(type(sample_rate))

print(type(data)) play_raw_data(data, sample_rate) here is the code for sound_lib.py: import sounddevice as sd

from pydub import AudioSegment

import numpy as np


def grab_raw_data(file_path, format="m4a"):

    '''

    input:

        file path

        format: default - "m4a"

    output: 

        sample rate

        raw data

    '''

    # 1. Load the "M4A" file using pydub

    # NOTE: This requires ffmpeg to be installed and in your system's PATH

    audio_segment = AudioSegment.from_file(file_path, format=format)

        

    # 2. Extract raw data as a NumPy array and get the sample rate

    sample_rate = audio_segment.frame_rate

        

    # pydub gives raw samples, usually 16-bit (2 bytes per sample)

    # We need to convert this buffer to a NumPy array of the correct dtype

    raw_data = np.array(audio_segment.get_array_of_samples())


    return sample_rate, raw_data


def play_raw_data(raw_data, sample_rate):

    '''

    plays the signal with the given sample rate on the computer's speakers

    input: 

        raw_data (array of sound samples)

        sample_rate

    output: 

        none

    '''

    # play using sounddevice library

    sd.play(raw_data, sample_rate)

        

    # Wait for playback to finish (important, or the script will quit early)

    sd.wait()

    print("Playback finished.")


def get_note_from_frequency(note_freq):

    """

    Converts a freq to its musical note name.

    A4 is set to 440 Hz as the reference.

    Input:

        note freq

    Output:

        note name

    """

    

    # Note frequencies relative to A4 (440 Hz)

    note_freqs = {

        -9: "C",

        -8: 'C#',

        -7: 'D',

        -6: 'D#',

        -5: 'E',

        -4: 'F',

        -3: 'F#',

        -2: 'G',

        -1: 'G#',

        0:'A',

        1: 'A#',

        2: 'B'

    }


    # Calculate semitone difference from A4

    semitone = np.log2(note_freq/440)*12

    

    # Calculate note from offset and octave

    octave = 4

    if semitone < -9.5:

        while semitone > 2.5 or semitone < -9.5:

            semitone += 12

            octave -= 1

    else:

        while semitone > 2.5 or semitone < -9.5:

            semitone -= 12

            octave += 1

    

    return note_freqs[round(semitone)] + f"{octave}"


# Note Dictionary

note_dict = {

    'C4':  261,

    'Cs4': 277,

    'D4':  293,

    'Ds4': 311,

    'E4':  329,

    'F4':  349,

    'Fs4': 369,

    'G4':  392,

    'Gs4': 415,

    'A4':  440,

    'As4': 466,

    'B4':  493

}

The error PortAudioError: Error opening OutputStream: Invalid sample rate [PaErrorCode -9997] usually happens for one of two reasons in your specific setup:

    Integer vs. Float: sounddevice expects the sample rate to be an integer. Sometimes pydub or other libraries return it as a float (e.g., 44100.0).

    Hardware Support: Your computer's sound card (or the ALSA driver in Linux) might not support the specific sample rate of your file (e.g., if it's 22050 Hz or 32000 Hz, some modern hardware only wants 44100 or 48000 Hz).

Quick Fixes

Try these steps in your notebook to identify and fix the issue:
1. Explicitly cast the sample rate to an integer

In your main script, change how you call the function:
Python

# Force integer sample rate
play_raw_data(data, int(sample_rate))

2. Check for Stereo/Mono mismatch

Your grab_raw_data function uses audio_segment.get_array_of_samples(). If the file is stereo, pydub flattens the data into a 1D array like [L1, R1, L2, R2, ...]. sounddevice expects stereo data to be a 2D array with two columns.

Update your play_raw_data function in sound_lib.py to handle this automatically:
Python

def play_raw_data(raw_data, sample_rate):
    # 1. Ensure sample_rate is an int
    sample_rate = int(sample_rate)

    # 2. Check if data is stereo but flattened (common with pydub)
    # If the array is 1D but the audio was actually stereo, 
    # we need to reshape it or sounddevice will play it at double speed/fail.
    # For now, let's just make sure it's 2D if needed:
    # (Assuming 2 channels, if it sounds fast, this is why)
    
    # 3. Normalize the data
    # sounddevice prefers floats between -1 and 1 or correct integer types.
    # Since pydub gives 16-bit ints, let's normalize to float32:
    data_normalized = raw_data.astype(np.float32) / 32768.0

    sd.play(data_normalized, sample_rate)
    sd.wait()
    print("Playback finished.")

3. Test with a Standard Rate

If it still fails, your hardware might be rejecting the file's native rate. Check what the rate is:
Python

print(f"Attempting to play at: {sample_rate} Hz")

If it's something unusual (like 32000), try forcing a standard rate just to see if it plays (it will sound higher or lower pitched, but it will confirm the driver issue):
Python

play_raw_data(data, 44100) 

Why the error happens (ALSA/PortAudio)

The logs mention pa_linux_alsa.c. On Linux systems, the audio driver (ALSA) is very strict. If your file is mono but the driver expects stereo, or if the sample rate isn't "native" to the hardware, it throws this specific Invalid sample rate error even if the rate itself is a standard one.

Would you like me to help you rewrite the grab_raw_data function so it correctly reshapes stereo files into the 2D format sounddevice expects?

now trying to make a spectogram of the signal i have obfuscated: print(sample_rate)

data_obf1 = data

print("original data sample:")

for i in range(24000,24020):

print(data[i])


#obfuscation

sign = np.random.choice([-1, 1])


print("obfuscated data sample:")

for i in range(0, len(data_obf1)):

data_obf1[i] = (data_obf1[i] + (data_obf1[i] * np.random.random() * 0.6 * sign))*0.2


for i in range(24000,24020):

print(data_obf1[i])


plt.figure()

plt.plot(time, data_obf1)



plt.xlabel("Time (seconds)")

plt.ylabel("Magnitude")

plt.title("Signal Waveform")

plt.grid(True)

plt.show()


#test it out

Audio(data_obf1, rate=sample_rate*2) # Plot the spectogram of the signal

#YOUR CODEprint(sample_rate)

data_obf1 = data

print("original data sample:")

for i in range(24000,24020):

    print(data[i])


#obfuscation

sign = np.random.choice([-1, 1])


print("obfuscated data sample:")

for i in range(0, len(data_obf1)):

    data_obf1[i] = (data_obf1[i] + (data_obf1[i] * np.random.random() * 0.6 * sign))*0.2


for i in range(24000,24020):

    print(data_obf1[i])


plt.figure()

plt.plot(time, data_obf1)



plt.xlabel("Time (seconds)")

plt.ylabel("Magnitude")

plt.title("Signal Waveform")

plt.grid(True)

plt.show()


#test it out

Audio(data_obf1, rate=sample_rate*2) HERE

times = np.linspace(0.0, len(data_obf1), len(data_obf1)*sample_rate, endpoint=False)

plt.plot(times, data_obf1)

plt.xlabel('Time (s)')

plt.ylabel('Amplitude')


plt.figure()

_,_, _, _= plt.specgram(data_obf1, NFFT=160, Fs=sample_rate, noverlap=128, cmap='viridis')

plt.colorbar(label='Powerprint(sample_rate)

data_obf1 = data

print("original data sample:")

for i in range(24000,24020):

    print(data[i])


#obfuscation

sign = np.random.choice([-1, 1])


print("obfuscated data sample:")

for i in range(0, len(data_obf1)):

    data_obf1[i] = (data_obf1[i] + (data_obf1[i] * np.random.random() * 0.6 * sign))*0.2


for i in range(24000,24020):

    print(data_obf1[i])


plt.figure()

plt.plot(time, data_obf1)



plt.xlabel("Time (seconds)")

plt.ylabel("Magnitude")

plt.title("Signal Waveform")

plt.grid(True)

plt.show()


#test it out

Audio(data_obf1, rate=sample_rate*2) (dB)')

plt.title('Spectrogram using plt.specgram()')

plt.xlabel('Time (s)')

plt.ylabel('Frequency (Hz)')

plt.ylim(0, sample_rate/2) # Show only up to the Nyquist frequency

plt.show()

It looks like your code got a bit scrambled during the copy-paste, and there are a few technical logic errors in how the time axis and data are being handled.

The biggest issue is data_obf1 = data. In Python, this doesn't create a copy; it creates a reference. When you modify data_obf1, you are accidentally overwriting your original data variable. Also, your times calculation for the plot is multiplying the length by the sample rate, which will create a massive array that doesn't match your data.

Here is the cleaned-up code to obfuscate the data and plot the spectrogram correctly:
Corrected Obfuscation and Spectrogram Code
Python

import matplotlib.pyplot as plt
import numpy as np
from IPython.display import Audio

# 1. Properly copy the data so we don't destroy the original
data_obf1 = data.copy().astype(float) 

# 2. Apply Obfuscation (Vectorized for speed)
sign = np.random.choice([-1, 1])
noise_factor = np.random.random(len(data_obf1)) * 0.6 * sign
data_obf1 = (data_obf1 + (data_obf1 * noise_factor)) * 0.2

# 3. Create the correct time axis
# Time = Index / Sample Rate
time_axis = np.linspace(0, len(data_obf1) / sample_rate, num=len(data_obf1))

# 4. Plot Waveform
plt.figure(figsize=(10, 4))
plt.plot(time_axis, data_obf1)
plt.xlabel("Time (seconds)")
plt.ylabel("Magnitude")
plt.title("Obfuscated Signal Waveform")
plt.grid(True)
plt.show()

# 5. Plot Spectrogram
plt.figure(figsize=(10, 6))
# NFFT: Window size (256-1024 is standard for speech/music)
# noverlap: How much the windows overlap
Pxx, freqs, bins, im = plt.specgram(data_obf1, NFFT=1024, Fs=sample_rate, noverlap=512, cmap='viridis')

plt.colorbar(im).set_label('Intensity (dB)')
plt.title('Spectrogram of Obfuscated Signal')
plt.xlabel('Time (s)')
plt.ylabel('Frequency (Hz)')
plt.ylim(0, sample_rate / 2)  # Nyquist frequency limit
plt.show()

# Test Audio (Using the original sample rate)
Audio(data_obf1, rate=sample_rate)

What was fixed:

    data.copy(): Using .copy() ensures that your original raw data remains untouched in case you want to try a different obfuscation method later.

    Vectorization: Instead of a for loop (which is slow in Python), I used NumPy's vectorized operations: data_obf1 * noise_factor. This is much faster for audio files with thousands of samples.

    Time Axis: Your previous times calculation was len(data_obf1) * sample_rate. If you had a 1-second clip at 44.1kHz, you were trying to create a 44,100-second long axis! The correct length is samples / sample_rate.

    NFFT Size: In your snippet, NFFT=160 is quite small. This gives you great time resolution but very poor frequency resolution (the spectrogram will look "blurry" vertically). Increasing this to 1024 usually makes the musical notes or speech patterns much clearer.

Would you like to try a different obfuscation technique, such as adding White Noise or a Low-Pass Filter to see how it changes the spectrogram?

does this spectrogram demonstrate harmonics? why or why not?

Based on the spectrogram you provided, the signal does demonstrate harmonics, though they are somewhat obscured by the noise introduced during your obfuscation process.
Why it demonstrates harmonics:

    Parallel Horizontal Bands: If you look at the bottom section of the plot (roughly between 0 and 2000 Hz), you can see multiple distinct, horizontal layers of brighter color (higher power). These layers represent different frequency components occurring simultaneously.

    Integer Multiples: Harmonics appear as stacked "rungs" on a ladder. The lowest bright line is the fundamental frequency (f0​). The lines above it represent 2f0​, 3f0​, and so on. In your image, there is a very strong concentration of energy below 500 Hz, with repeating faint "ghost" bands visible at roughly 1000 Hz and 1500 Hz.

    Temporal Correlation: Notice how the bright spots "dance" together vertically. When the frequency shifts slightly or the power increases at second 20, all the stacked bands change in unison. This is a classic sign of a complex harmonic sound (like a musical instrument or a human voice).

Why they are hard to see:

The "fuzziness" or vertical smearing across the higher frequencies (3000 Hz to 6000 Hz) is the result of your obfuscation code.

    Random Scaling: By multiplying each sample by a random value (np.random.random() * 0.6), you essentially applied amplitude modulation with noise.

    Spectral Splatter: In the frequency domain, this creates "sidebands" and broadens the peaks. Instead of sharp, thin lines for each harmonic, you get wide, blurry smudges.

    Low Window Size: You mentioned using NFFT=160. As I noted earlier, a small NFFT provides poor frequency resolution. This makes it harder to distinguish between two harmonics that are close together.

Would you like to try increasing your NFFT to 1024 or 2048 to see if those horizontal harmonic lines become sharper and more defined?

trying to add a signal at a specific frequency: def create_signal(A,w,phi,t):

    return A*np.sin(2*np.pi*w*t + phi)


# create a signal with the frequency(ies) that should have noise added to it

# NOTE: the signal should be the same length as the data

# HINT: duration = len(data)//sr; times = np.linspace(0, duration, num=len(data))

# YOUR CODE HERE

duration = len(data)//sample_rate

times = np.linspace(0, duration, num=len(data))


signal = []

i = 0

for time in times:

    signal.append(create_signal(A=20000,w=300,phi=0,t=time))

    #signal.append(data[i])

    i += 1



plt.figure()

plt.plot(times, signal)



plt.xlabel("Time (seconds)")

plt.ylabel("Magnitude")

plt.title("Signal Waveform")

plt.grid(True)

plt.show()


print(signal.shape)

It looks like you're running into a common NumPy pitfall: Python lists versus NumPy arrays. When you use signal = [] and append(), you are creating a standard Python list. Python lists don't have a .shape attribute, which is why your print(signal.shape) will likely throw an error.

Instead of using a for loop, you can take advantage of vectorization. NumPy allows you to pass the entire times array into your function at once. This is significantly faster and automatically returns a NumPy array with the correct shape.
Optimized Code
Python

# 1. Define the function (no changes needed here)
def create_signal(A, w, phi, t):
    return A * np.sin(2 * np.pi * w * t + phi)

# 2. Calculate duration and create the times array
# Using float division (/) for duration is more precise than floor division (//)
duration = len(data) / sample_rate
times = np.linspace(0, duration, num=len(data))

# 3. Create the signal (Vectorized!)
# We pass the whole 'times' array into the function
signal = create_signal(A=20000, w=300, phi=0, t=times)

# 4. Plotting
plt.figure(figsize=(10, 4))
plt.plot(times, signal)
plt.xlabel("Time (seconds)")
plt.ylabel("Magnitude")
plt.title("Generated 300 Hz Sine Wave")
plt.grid(True)
plt.show()

# 5. This will now work correctly
print(f"Signal shape: {signal.shape}")

Why this is better:

    Performance: The for loop in Python is quite slow for audio data (which often has 44,100+ samples per second). The vectorized version runs in optimized C code under the hood.

    Compatibility: By passing the array times into np.sin(), the output signal is automatically a NumPy array, which is exactly what plt.plot and sd.play expect.

    Precision: Using duration = len(data) / sample_rate (single /) ensures you don't lose fractional seconds, keeping your signal perfectly aligned with the original audio length.

Adding the signals together

If your goal is to add this 300 Hz "noise" or tone to your existing data, you can now simply do:
Python

# Ensure they are the same shape and type
combined_signal = data.astype(float) + signal

# Play the result
play_raw_data(combined_signal, sample_rate)

Would you like to see how to add multiple frequencies at once (like a chord) to create a more complex "masking" noise?

now i wanna add some random gaussian white noise using numpy.random.ramdon

To add Gaussian white noise, you should actually use np.random.normal instead of np.random.random.

While np.random.random produces "Uniform" noise (values strictly between 0 and 1), Gaussian (White) Noise follows a bell curve (normal distribution) centered at zero. This sounds much more like natural "static" or "hiss."

Here is how to implement it correctly using NumPy:
Python

# 1. Define the intensity of the noise (Standard Deviation)
# Since your signal magnitude is ~20,000, start with a smaller value like 500-1000
noise_amplitude = 1000 

# 2. Generate Gaussian White Noise
# loc=0 (centered at 0), scale=amplitude (spread), size=matching your data
white_noise = np.random.normal(loc=0, scale=noise_amplitude, size=len(data))

# 3. Add it to your signal
# Make sure to use .astype(float) to prevent integer overflow issues
data_with_noise = data.astype(float) + white_noise

# 4. Plot the result
plt.figure(figsize=(10, 4))
plt.plot(times, data_with_noise, color='gray', alpha=0.7, label='Signal + Noise')
plt.plot(times, data, color='blue', alpha=0.5, label='Original')
plt.legend()
plt.title("Signal with Added Gaussian White Noise")
plt.xlabel("Time (s)")
plt.ylabel("Magnitude")
plt.show()

Key Differences:

    np.random.normal: Generates values that can be positive or negative, centered at 0. This is the standard definition of "White Noise."

    np.random.random: Only generates positive values between 0 and 1. If you added this to your audio, it would create a "DC offset," which can cause a loud "pop" at the start of playback and potentially distort your speakers.

How to tune the noise:

    Too quiet? Increase the scale (amplitude).

    Too loud? Decrease it.

    Check the Spectrogram: If you run your plt.specgram code on this new data_with_noise, you will see a solid "fog" of color across all frequencies, which is the hallmark of white noise.

Would you like to see how to create "Pink Noise," which sounds more like falling rain and is less harsh on the ears than White Noise?

i want random.random

If you specifically want to use np.random.random, keep in mind that it generates values between 0.0 and 1.0. Because audio signals oscillate around 0, simply adding these values will shift your entire waveform upward (creating a DC offset), which can cause a "pop" sound or clipping.

To get a "centered" white noise using np.random.random, you need to shift and scale the range so it spans from negative to positive.
How to use np.random.random for Noise:

To center the noise at zero with a specific amplitude, use this formula:
Noise=(np.random.random(size)−0.5)×2×Amplitude

Here is the code implementation:
Python

# 1. Define your noise level
# (Adjust this based on your data's magnitude)
noise_amplitude = 5000 

# 2. Generate random values [0.0, 1.0)
raw_random = np.random.random(len(data))

# 3. Shift to [-0.5, 0.5) and scale to [-noise_amplitude, noise_amplitude)
white_noise = (raw_random - 0.5) * 2 * noise_amplitude

# 4. Add to your existing signal
data_obf2 = data.astype(float) + white_noise

# 5. Plot to see the result
plt.figure(figsize=(10, 4))
plt.plot(times, data_obf2)
plt.title("Signal with Noise (via np.random.random)")
plt.xlabel("Time (s)")
plt.ylabel("Magnitude")
plt.show()

Why we do the math:

    raw_random - 0.5: This moves the range from [0,1] to [−0.5,0.5]. Without this, your speakers would try to push the cone "out" and stay there, which isn't good for the hardware.

    * 2: This makes the span exactly 1.0 unit wide (from −0.5 to 0.5).

    * noise_amplitude: This stretches the noise so it's actually audible against your signal.

A Note on the Sound:

Using np.random.random creates Uniform White Noise. It sounds very similar to the Gaussian noise we discussed earlier, but technically it has a "flatter" distribution. In a spectrogram, both will appear as a consistent "wall" of energy across all frequencies.

Would you like to see how to check the "Signal-to-Noise Ratio" (SNR) to see how much your obfuscation is actually covering the original signal?

# create frequency graph - spectogram of your created signal (with noise) + original data

# YOUR CODE HERE

data_obf2 = []

for i in range(0, len(data)):

    data_obf2[i] = data[i] + noisy_signal[i] # create frequency graph - spectogram of your created signal (with noise) + original data

# YOUR CODE HERE

data_obf2 = []

for i in range(0, len(data)):

data_obf2[i] = data[i] + noisy_signal[i]

You are running into a "list index out of range" error with that for loop because data_obf2 = [] creates an empty list, and you can't assign to data_obf2[i] until that index exists.

However, since you are using NumPy, you should avoid the loop entirely. Adding arrays together in NumPy is "element-wise" and incredibly fast.
The Efficient Way (Vectorized)

Assuming noisy_signal is the 300Hz sine wave or the random noise you created earlier, here is the corrected code:
Python

# 1. Add the signals directly (Ensure they are floats to avoid overflow)
data_obf2 = data.astype(float) + noisy_signal

# 2. Create the spectrogram
plt.figure(figsize=(10, 6))

# NFFT=1024 provides a good balance between time and frequency resolution
Pxx, freqs, bins, im = plt.specgram(data_obf2, NFFT=1024, Fs=sample_rate, noverlap=512, cmap='magma')

# 3. Add details
plt.colorbar(im).set_label('Intensity (dB)')
plt.title('Spectrogram: Original Data + Custom Noise Signal')
plt.xlabel('Time (s)')
plt.ylabel('Frequency (Hz)')

# Only show up to Nyquist frequency
plt.ylim(0, sample_rate / 2) 

plt.show()

What to look for in this Spectrogram:

    The Constant Line: You should see a very distinct, solid horizontal line at 300 Hz (if that was your w value). This represents the pure sine wave you generated.

    The "Background" Fog: If you added the np.random.random noise as well, the entire background will have a consistent grainy texture or "heat" across all frequencies.

    The Original Signal: You should still see the original audio features (like vertical speech bars or harmonic rungs) "underneath" the noise you added.

Why use astype(float)?

WAV data is often stored as 16-bit integers (int16). If you add a value to a 16-bit integer and it exceeds 32,767, it "wraps around" to -32,768, creating horrible digital distortion. Converting to float first prevents this and allows for smooth addition.

Would you like to try making the added signal "sweep" from a low frequency to a high frequency (a chirp) to see how that looks on the spectrogram?

on to the next part: # load the signal car_down.npy from the directory lab2 inside the class_dir using the function np.load

# YOUR CODE HERE

path = class_dir + "sound_files/lab2/car_down.npy"

car_signal = np.load(path)


# visualize the data on a time/amplitude plot - you may have to play with the boundaries a little bit

# YOUR CODE HERE ---------------------------------------------------------------------------

FileNotFoundError                         Traceback (most recent call last)

Cell In[55], line 4

      1 # load the signal car_down.npy from the directory lab2 inside the class_dir using the function np.load

      2 # YOUR CODE HERE

      3 path = class_dir + "sound_files/lab2/car_down.npy"

----> 4 car_signal = np.load(path)

      6 # visualize the data on a time/amplitude plot - you may have to play with the boundaries a little bit

      7 # YOUR CODE HERE


File ~/miniforge3/envs/si486h/lib/python3.13/site-packages/numpy/lib/_npyio_impl.py:454, in load(file, mmap_mode, allow_pickle, fix_imports, encoding, max_header_size)

    452     own_fid = False

    453 else:

--> 454     fid = stack.enter_context(open(os.fspath(file), "rb"))

    455     own_fid = True

    457 # Code to distinguish from NumPy binary files and pickles.


FileNotFoundError: [Errno 2] No such file or directory: '/SI486H/sound_files/lab2/car_down.npy'

It looks like you're hitting a FileNotFoundError because the path string doesn't perfectly match where the file is sitting on the server. Since your previous code used class_dir = "/SI486H/", the current path you're building evaluates to /SI486H/sound_files/lab2/car_down.npy.

Often in these lab environments, the "sound_files" folder might be missing from the path or named slightly differently.
1. Fix the Path

Try checking if lab2 is directly under class_dir. Change your path to this and see if it loads:
Python

# Try removing "sound_files" if the lab instructions suggest lab2 is a top-level folder
path = class_dir + "lab2/car_down.npy" 
# Or check if it's just: path = class_dir + "car_down.npy"

car_signal = np.load(path)
print(f"Loaded signal with {len(car_signal)} samples.")

    Pro Tip: If you aren't sure where the file is, run !find /SI486H -name "car_down.npy" in a cell to find the exact absolute path.

2. Visualize with Time/Amplitude

Once you get it loaded, you'll need a time axis. Since .npy files don't store the sample rate like WAV files do, you'll likely need to use the sample_rate from your previous part (assuming it's the same, e.g., 44100 or 22050).
Python

# Assuming the same sample_rate from earlier
duration = len(car_signal) / sample_rate
car_times = np.linspace(0, duration, num=len(car_signal))

plt.figure(figsize=(12, 4))
plt.plot(car_times, car_signal, color='darkred')

# "Playing with boundaries" usually means zooming in on a specific part
# Let's look at the first 0.5 seconds
plt.xlim(0, 0.5) 

plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.title("Car Down Signal (First 0.5s)")
plt.grid(True)
plt.show()

3. Why the "Boundaries" matter

Raw signals like a car passing by or an engine downshifting often have very quiet starts or ends. If you plot the whole thing, it might just look like a thin solid line.

    plt.xlim(start, end) lets you "zoom in" on the X-axis.

    plt.ylim(min, max) lets you "zoom in" on the Y-axis if the amplitude is very small.

Would you like me to show you how to find the specific sample rate of this .npy file if it wasn't provided in the instructions?

python exponent

In Python, there are three primary ways to calculate exponents.
1. The ** Operator (Most Common)

The most "Pythonic" and frequently used method is the double-asterisk operator. It is built directly into the language and works with integers, floats, and negative numbers.
Python

# 2 raised to the power of 3
result = 2 ** 3  # Output: 8

# Floating point exponents (Square root)
sqrt_result = 16 ** 0.5  # Output: 4.0

2. The pow() Function

Python has a built-in pow(base, exp) function. While it performs the same calculation as **, it includes an optional third argument for modular exponentiation, which is much more efficient than calculating the exponent first and then using the modulo operator.
baseexp(modmod)
Python

# Basic power
result = pow(2, 3)  # Output: 8

# Modular exponentiation: (2^3) % 3
mod_result = pow(2, 3, 3)  # Output: 2

3. The math.pow() Function

If you are working with the math library, you can use math.pow(). Unlike the other two methods, this function always returns a float, even if the inputs are integers.
Python

import math

result = math.pow(2, 3)  # Output: 8.0

Comparison Table
Method	Result Type	Best Use Case
**	Integer or Float	General purpose and readability
pow(x, y, z)	Integer or Float	Cryptography or competitive programming (modulo)
math.pow()	Always Float	When consistent float output is required for math pipelines

Would you like me to show you how to handle roots (like cube roots) using these exponent methods?

how do i determine the frequency of a signal for which i have the data, sampling rate, and duration?

To determine the frequency of a signal from raw data, you typically move from the Time Domain (amplitude over time) to the Frequency Domain (amplitude over frequency). The most common tool for this is the Fast Fourier Transform (FFT).

Since you have the data and the sample_rate, you can use numpy.fft.
1. The Mathematical Approach

If your signal is a simple, clean sine wave, you can sometimes estimate the frequency by counting cycles over the duration:
f≈Duration (s)Number of Cycles​

However, for real-world data (like your car signal), you need the FFT to see which frequencies are actually present.
2. Implementation using FFT

The FFT identifies the "building block" frequencies of your signal. Here is how to code it:
Python

import numpy as np
import matplotlib.pyplot as plt

# 1. Compute the FFT
# n is the number of samples
n = len(car_signal)
fft_values = np.fft.fft(car_signal)

# 2. Get the frequencies corresponding to the FFT values
# This creates an array of frequency "bins" based on the sample_rate
frequencies = np.fft.fftfreq(n, d=1/sample_rate)

# 3. Take the absolute value (magnitude) of the FFT
# We only care about the positive frequencies (first half of the array)
magnitude = np.abs(fft_values)

# 4. Find the dominant frequency
# Find the index of the highest peak in the magnitude array
peak_index = np.argmax(magnitude[:n//2])
dominant_freq = frequencies[peak_index]

print(f"The dominant frequency is: {abs(dominant_freq):.2f} Hz")

# 5. Plot the result
plt.figure(figsize=(10, 4))
plt.plot(frequencies[:n//2], magnitude[:n//2])
plt.title("Frequency Spectrum (Magnitude vs Frequency)")
plt.xlabel("Frequency (Hz)")
plt.ylabel("Magnitude")
plt.grid(True)
plt.show()

3. Key Concepts to Remember

    The Nyquist Limit: You can only detect frequencies up to half of your sample_rate. If your sample rate is 44,100 Hz, the maximum frequency you can see is 22,050 Hz.

    Resolution: The frequency resolution (how "precise" your frequency measurement is) depends on the duration. Longer duration leads to finer frequency bins:
    Δf=Duration1​

    Magnitude vs. Power: np.abs(fft_values) gives you the magnitude. If you want the Power Spectrum, you would square it (magnitude2).

Applying this to your car signal

Since you just loaded car_down.npy, running the code above will tell you the pitch of the engine or the wind noise. If the "Car Down" signal involves a shift in pitch (Doppler effect), you might want to use the Spectrogram code we wrote earlier, as it shows how those frequencies change over time.

Would you like me to help you find the specific "Peak Frequency" within a certain time window of that car signal?

trying to jam the signal by adding a "one" signal whenever it is a zero value: #YOUR CODE HERE to help you answer the above questions


## used Gemini to determin the frequency:

import numpy as np

import matplotlib.pyplot as plt


# 1. Compute the FFT

# n is the number of samples

n = len(car_signal)

fft_values = np.fft.fft(car_signal)


# 2. Get the frequencies corresponding to the FFT values

# This creates an array of frequency "bins" based on the sample_rate

frequencies = np.fft.fftfreq(n, d=1/sample_rate)


# 3. Take the absolute value (magnitude) of the FFT

# We only care about the positive frequencies (first half of the array)

magnitude = np.abs(fft_values)


# 4. Find the dominant frequency

# Find the index of the highest peak in the magnitude array

peak_index = np.argmax(magnitude[:n//2])

dominant_freq = frequencies[peak_index]


print(f"The dominant frequency is: {abs(dominant_freq):.2f} Hz")



one_signal = create_signal(A=1,w=dominant_freq,phi=0,t=car_times)


jammed_car_signal = car_signal.copy()


for i in range(0, len(car_signal)):

if abs(car_signal[i]) < 0:

jammed_car_signal[i] = one_signal[i]


plt.figure(figsize=(10, 4))

plt.plot(car_times, jammed_car_signal)

plt.xlabel("Time")

plt.ylabel("Magnitude")

plt.title("Car Signals")

plt.grid(True)

plt.show()

I see what you're trying to do with the "jamming" logic, but there are two main issues in your current code that will prevent it from working as intended: the conditional logic and the signal magnitude.
1. The Logic Error (abs(car_signal[i]) < 0)

The condition abs(car_signal[i]) < 0 will never be true. The absolute value of any number is always 0 or greater. If you want to detect when the signal is "zero" (or very close to it), you should check if the value is less than a very small threshold.
2. The Amplitude Mismatch

You created one_signal with an amplitude A=1. If your car_signal has magnitudes in the thousands (like your earlier data), adding a value of 1 is like throwing a pebble into an ocean—you won't even see it on the plot.
Corrected Jamming Code

Here is the optimized, "NumPy-style" (vectorized) way to do this without using a slow for loop:
Python

# 1. Fix the amplitude to match the car signal's scale
# Let's use the maximum value of the car signal to ensure the "one" signal is visible
jamming_amplitude = np.max(np.abs(car_signal))
one_signal = create_signal(A=jamming_amplitude, w=dominant_freq, phi=0, t=car_times)

# 2. Copy the signal
jammed_car_signal = car_signal.copy().astype(float)

# 3. Apply the jamming logic
# We use a small threshold (e.g., 1e-5) because floating point '0' is rarely exactly 0
threshold = 0.01 
zero_indices = np.abs(car_signal) <= threshold

# Whenever the car signal is "zero", replace it with the one_signal value
jammed_car_signal[zero_indices] = one_signal[zero_indices]

# 4. Visualize
plt.figure(figsize=(10, 4))
plt.plot(car_times, jammed_car_signal, label="Jammed Signal", alpha=0.8)
plt.plot(car_times, car_signal, label="Original", alpha=0.5, linestyle='--')
plt.xlabel("Time (s)")
plt.ylabel("Magnitude")
plt.title(f"Jammed Car Signal (Replacing Zeros with {abs(dominant_freq):.2f} Hz)")
plt.legend()
plt.grid(True)
plt.show()

What's happening here?

    Thresholding: Since digital audio often has tiny amounts of noise, == 0 almost never works. Using np.abs(x) <= threshold catches all the "silent" parts.

    Vectorized Indexing: jammed_car_signal[zero_indices] tells NumPy to "only look at the spots where my condition was True." This replaces the need for the if statement and the for loop entirely.

    Amplitude Matching: By setting A to the max amplitude of the car, your 300Hz (or dominant freq) tone will actually be loud enough to "jam" the silence.

Would you like to see how this looks on a spectrogram? It should show your original car signal with "bursts" of the dominant frequency appearing whenever the car was silent.

how should i center it around 1hz? # Before you submit your signal, gnuradio automatically applies the carrier signal to the file.

# This means, you should center your signal around 1 Hz.

# That is, if your signal has a sinusoid at 20Hz, put it back to 1 Hz and the carrier signal will bring it back up to 20 Hz.


car_signal.astype("complex").tofile("smith.dat")

To center your signal around 1 Hz for GNURadio, you are essentially performing a frequency shift (down-conversion). If your current dominant frequency is at fdominant​ (e.g., 20 Hz) and you want it to be at 1 Hz, you need to shift the entire spectrum by the difference.

The formula to shift a signal in the frequency domain is to multiply it by a complex exponential in the time domain:
sshifted​(t)=soriginal​(t)⋅e−j2πΔft

where Δf=fdominant​−1.
Implementation

Here is how to calculate the shift and apply it to your car_signal:
Python

# 1. Calculate the frequency shift needed
# We want: Current_Freq - Shift = 1 Hz -> Shift = Current_Freq - 1
f_shift = abs(dominant_freq) - 1

# 2. Create the complex phasor for the shift
# We use a negative sign to shift the frequency DOWN
phasor = np.exp(-1j * 2 * np.pi * f_shift * car_times)

# 3. Apply the shift to the signal
# This converts the real signal into a complex (I/Q) signal
centered_signal = car_signal * phasor

# 4. Save to file as complex64 (standard for GNURadio)
centered_signal.astype(np.complex64).tofile("smith.dat")

print(f"Signal shifted from {abs(dominant_freq):.2f} Hz to 1.00 Hz")

Why This Works

    Complex Representation: GNURadio typically works with I/Q data (complex numbers). By multiplying by e−jωt, you are rotating the phase of the signal samples, which effectively "drags" the peaks in the frequency spectrum to the left.

    The 1 Hz Target: By subtracting the dominant frequency minus 1, you ensure that the primary energy of your car signal now sits exactly at 1 Hz relative to the "center" of the GNURadio flowgraph.

    np.complex64: This is the standard data type for GNURadio "Complex" blocks. It uses 4 bytes for the Real (I) part and 4 bytes for the Imaginary (Q) part.

Verification

If you plot the FFT of centered_signal using the same FFT code from earlier, you should now see the highest peak sitting right at the 1 Hz mark on the x-axis.

Would you like me to show you how to visualize the complex I/Q components (Real vs. Imaginary) of your shifted signal?

