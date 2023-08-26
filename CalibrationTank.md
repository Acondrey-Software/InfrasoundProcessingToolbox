# Calibration Tank:  Main Commands

Original Author: Carrick L. Talmadge, Ph.D.

# Contents

---

[Overview](#overview)

[Signal Generation Scripts](#signal-generation-scripts)


# Overview

---

All of the scripts described below support the command-line parsing. The
`-usage` flag will always give the most recently supported flags and variables.
In most of the shell scripts, the `+T` flag causes three clicks, 1-second apart,
to be played prior to the signal. This flag helps to precisely time align each signal presentation 
in cases where the digitizer clock is not GPS locked and cannot be accurately (e.g.: using TELNET operations).   
(Precise time alignment is not necessary for reference calibration measurements).

If the shell script is run with the `–n` option, no signal will be generated.
This option allows for “dry runs” to see exactly what signal generation commands
would actually be run from the shell script.

All of the shell scripts starting with “runScreen” will generate files of the
form `Readme.sig.txt` (n.b.: the sig “extension” can be modified via a command
line argument). We will refer to these scripts as “screening” commands. This
records the details of the runs in such a format that allows the actual data
segments of the continuously recorded signal to be extracted into separate
directories. It also saves a copy of the signal generation (regardless of the
directory it was run in), in the directory `/usr/local/infrasound/log/signal`.
These latter files are stored by `YYYYMMDD.txt` format (YYYY=year, MM=month and DD
=day).

Further more, for each screening command, a “run name” must be provided.
Typically this is of the format `sensor1-sensor2-sensor3-sensor4-sensor5-sensor6`.
Typically the sensor names are model name of the sensor followed by a serial
number. If there are multiple output channels, a second “.” followed by a
description of the channel used will be appended. E.g., the sensor name
MB2005.7034.p12 would refer to the MB2005 sensor with serial number 7034, and
the output channel “p12” would refer to the pins 1&2 output.

In some cases, such as with Hyperion sensors, the serial number uniquely specifies
the sensor, so the model name is dropped. Also some Hyperion sensors have
acceleration and pressure channels. The extensions `.pres` and `.acc` will be
sued for these sensors. However, only the pressure channel is being recorded, no
`.pres` extension is provided, and the output will default to the pressure
channel.

The `+` character is used to specify further details about a particular sensor
configuration. For example, `SETRA278.4909+ws` applies to the Model 278 Setra
sensor with serial number 4909, where a wind-screen has been attached to the end
of the sensor. Knowledge of the hardware configuration is required in order to
relate the amplitude of the signal (in its internal units) to the corresponding
pressure amplitude in the calibration tank.

For typical applications at the NCPA, currently “0.01 units ~ 2.5 Pa peak” for
tones, and “0.015 units ~ 4 Pa rms for noise.

# Signal Generation Scripts

---

The following sections describe the various signal generation scripts. These
scripts are divided into three categories: 1) high-level screening scripts, 2)
lower-level signal generation scripts, and 3) GEOTECH scripts. Geotech scripts
are likely to be of limited use outside of the NCPA, but are included for
completeness.

## runScreen.sh

The shell script `runScreen.sh` provides the higher-level functionality needed
for routine screening of infrasound sensors. This screening typically involves
the playing of one or more tones (900-seconds duration, ~ 10 Pa peak amplitude),
followed by filtered noise (4000-seconds duration, ~ 4 Pa RMS), followed by one
or more post-noise tones.

### Usage

Use `+P` to enable post-noise stimulus testing. By default, a single post-noise
tone is played, with its frequency given by `pfreq`. Using the `+F` flag causes
all of the pre-noise stimulation frequencies to be played.

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| P      | run post-noise stimulation                           |
| F      | run all screening frequencies post-noise stimulation |
| n      | play signals (turn off for debugging)                |
| N      | run noise                                            |
| S      | save signal                                          |
| T      | do trigger                                           |
| v      | verbose output                                       |

#### Variables:

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| run          | None          | run name                            |
| ext          | None          | extension name                      |
| sig          | sig           | signal extension name               |
| amp          | 0.04          | amplitude of tone                   |
| namp         | 0.015         | amplitude of noise                  |
| tau          | 0.1           | on/off time                         |
| fstart       | 0.001         | starting frequency of noise filter  |
| fstop        | 10            | ending frequency of noise filter    |
| npoles       | 1             | number of poles in noise filter     |
| pamp         | 0.04          | post-noise amplitude of tone        |
| pfreq        | 0.5           | post-noise frequency of tone        |
| duration     | 904           | set duration for remaining signals  |
| nduration    | 4004          | set duration for noise              |
| pduration    | 904           | set duration for post-noise signals |
| ochannel     | 0             | output channel                      |
| outputDevice | 5             | output outputDevice                 |

## runScreenTwoTonesFixedF2.sh
This routine generates a fixed-frequency two-tone signal.

Usage: `runScreenTwoTonesFixedF2.sh run_name freq1_list`

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | play signals (turn off for debugging)                |
| T      | do trigger                                           |

#### Variables:

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| f2           | 0.5           | frequency of tone 2                 |
| amp1         | 0.04          | amplitude of tone 1                 |
| amp2         | 0.04          | amplitude of tone 2                 |
| duration     | 900           | duration of tone                    |
| minCycles    | 10            | min cycles of difference tone       |
| sig  | twotone.fixed.f2.sig |  signal name |
| sleep        | 15            | sleep duration between signals      |
| logDir       | /usr/local/infrasound/log/signal | log directory |



## runScreenTwoTonesFixedRatio.sh

Usage: `runScreenTwoTonesFixedRatio.sh run_name ratio_list`

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | play signals (turn off for debugging)                |
| T      | do trigger                                           |

#### Variables:
| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| ratio        | 1.1           | ratio of f2/f1                      |
| amp1         | 0.04          | amplitude of tone 1                 |
| amp2         | 0.04          | amplitude of tone 2                 |
| duration     | 900           | duration of tone                    |
| minCycles    | 10            | min cycles of difference tone       |
| sleep        | 15            | sleep duration between signals      |
| logDir       | /usr/local/infrasound/log/signal | log directory |

## runScreenTwoTonesVarA2.sh
Usage: `runScreenTwoTonesVarA2.sh run_name amp_list`

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | play signals (turn off for debugging)                |
| T      | do trigger                                           |

#### Variables:
| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| f1           | 0.4           | frequency of tone 1                 |
| f2           | 0.5           | frequency of tone 2                 |
| amp1         | 0.04          | amplitude of tone 1                 |
| amp2         | 0.04          | amplitude of tone 2                 |
| duration     | 900           | duration of tone                    |
| minCycles    | 10            | min cycles of difference tone       |
| sleep        | 15            | sleep duration between signals      |
| logDir       | /usr/local/infrasound/log/signal | log directory |


## runScreenQuiet.sh

Note: This routine is provided for systematic recording of the measurement
period for “quiet” screenings (that is, recording periods with no external
stimulus). As with other screenings, the results of the screen are written to
the “sig” file.

Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | if false, do not sleep for duration               |


### Variables:

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| run          | None          | run name                            |
| ext          | None          | extension name                      |
| sig          | sig           | signal extension name               |
| duration     | 904           | set duration for remaining signals  |
| logDir       | /usr/local/infrasound/log/signal | log directory |

## runLinearSweep.sh

This routine generates a linear sweep from “f1” to “f2”. Here “duration”
controls the sweep duration for each sweep, and “nreps” controls the total
number of sweeps in the presentation.

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | play signals (turn off for debugging)                |
| T      | do trigger                                           |
| v      | verbose output                                       |

#### Variables:

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| run          | None          | run name                            |
| ext          | None          | extension name                      |
| sig          | sig           | signal extension name               |
| amp          | 0.01          | amplitude of tone                   |
| f1           | 0.1           | starting frequency of noise filter  |
| f2           | 50            | ending frequency of noise filter    |
| duration     | 30            | set duration for remaining signals  |
| nreps        | 10            | number of sweeps                    |
| outputDevice | 5             | output outputDevice                 |
| ochannel1    | 0             | output channel                      |

## runScreenLogSweep.sh
This routine generates a log-frequency sweep from “f1” to “f2”. Here “duration”
controls the sweep duration for each sweep, and “nreps” controls the total
number of sweeps in the presentation.

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | play signals (turn off for debugging)                |
| T      | do trigger                                           |
| v      | verbose output                                       |

#### Variables:

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| run          | None          | run name                            |
| ext          | None          | extension name                      |
| sig          | sig           | signal extension name               |
| amp          | 0.01          | amplitude of tone                   |
| f1           | 0.1           | starting frequency of noise filter  |
| f2           | 50            | ending frequency of noise filter    |
| duration     | 30            | set duration for remaining signals  |
| nreps        | 10            | number of sweeps                    |
| outputDevice | 5             | output outputDevice                 |
| ochannel1    | 0             | output channel                      |
| logDir       | /usr/local/infrasound/log/signal | log directory |

# Lower level signal generation scripts

---

The lower level signals are intended as a simplified interface to the binary
executables.  

 ## runTonesMIII.sh
Low-level script used to generate one or more sequential tones. This script
calls the playTone executable binary in order to generate tonal signals in
real time.

The frequency is fixed by the freq=freq argument. One or more amplitudes are
provided in order to generate tones. Note that “amax” controls the maximum
amplitude that can be safely provided to the amplifier. Change this with
care.

Usage: `runTonesMIII.sh options amp1 amp2 ...`

#### Options:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | if true, actually run tone (otherwise dry run)       |
| T      | do trigger                                           |

#### Variables

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| run          | None          | run name                            |
| amp          | 0          | amplitude of tone                   |
| amax         | 0.15          | maximum amplitude                   |
| freq         | 0.5           | frequency of tone                   |
| duration     | 904           | duration of tone                    |
| ochannel     | 0             | output channel                      |
| outputDevice | 5             | output outputDevice                 |
| sleep        | 1             | amount to pause between presentations |

## runTwoTonesMIII.sh
Primitive used to generate one or more sequences of two tone stimuli. This
script calls the playTone2 executable to generate two-tone signals.

The frequency is fixed by the freq1=freq1 and freq2=freq2 argument. The verb
“go” must be provided to actually invoke the playTone2 executable. Note that
“amax” controls the maximum amplitude that can be safely provided to the
amplifier. Change this with `runTwoTonesMIII.sh options amp1 amp2 go`

#### Options:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | if true, actually run tone (otherwise dry run)       |

#### Variables

| Variable     | Default value | Description                         |
|--------------|---------------|-------------------------------------|
| run          | None          | run name                            |
| amp1         | 0.006          | amplitude of tone 1                 |
| amp2         | 0.006          | amplitude of tone 2                 |
| amax         | 0.15          | maximum amplitude                   |
| f1        | 0.5           | frequency of tone 1                 |
| f2        | 0.01           | frequency of tone 2                 |
| duration     | 904           | duration of tone                    |
| sleep| 1 | amount to pause between presentations |

## runNoiseMIII.sh

Primitive used to generate broad band noise. This script invokes the runNoise
executable in order to generate band-pass noise in real time.
The fstart, fstop and npoles options can be used to set the band start and band
stop frequencies as well as the number of poles. Providing the numerical
amplitude “amp” will generate a signal with an amplitude “amp” in internal
units. Note that “amax” controls the maximum amplitude that can be safely
provided to the amplifier. Change this with

Usage: `runNoiseMIII.sh options amp1 amp2 …`

#### Flags:

| Option | Description                                          |
|--------|------------------------------------------------------|
| n      | if true, actually run tone (otherwise dry run)       |
| T      | do trigger                                           |

#### Variables:

| Variable     | Default Value | Description                 |
|--------------|---------------|-----------------------------|
| run          | ""            | run name                    |
| fstart       | 0.001         | starting frequency of filter|
| fstop        | 10            | ending frequency of filter |
| npoles       | 4             | number of poles in filter  |
| amax         | 0.15          | maximum amplitude           |
| ochannel     | 0             | channel to play in          |
| duration     | 4002          | how long to play            |
| outputDevice | 5             | output device               |
| outputFile   | noise.bin     | output signal name          |

# Geotech Scripts

---

These scripts are provided as informational only. With the proper tweaking,
these scripts should function on any UNIX/LINUX/DARWIN/CYGWIN system, it is
not expected that these will work without some modification on different
platforms.

## spinGeotechDownload.sh

This is the main script that downloads data froma GEOTECH using ftp. By default,
this process checks the /d/data directory on the GEOTECH every five minutes.
After each file has been confirmed as downloaded, the remote file is deleted
from the GEOTECH.

## unbundle.awk

This script parses the “sig” files (e.g., Readme.sig.txt) and extracts the sets
of measurements. It parses the sig file, then calls grabDataL.awk to perform
this task for each measurement found..

## grabDataL.awk

Use the syntax grabDataL.sh directory from_date from_time to_date to_time. Here
from_date and to_date are in the format “YYYYMMDD” (year, month, day in
numerical format) and the from_time and to_time are in the format “hhmmss” (
hour, minutes, seconds in numerical format).

## extractDir.sh

This extracts the run directories according to the specifications given below.

extractDir.sh options directory_list
Variables:
poff=5: seconds to add to pasted value
ref1=5: reference for GEOTECH 1
ref2=2: reference for GEOTECH 2 (if found)
tlen=+900: length of snd to generate
tmax=1e38: maximum seconds to sndcat for plotted data

## genSnd.sh

Note genSnd.sh is a very low level script. It assumes that the cd11 files in the
Full directory have been converted to “snd” files. Use this command to make the
conversion:

(cd Full; cd11tosnd *.cd11)

Standard forms for the genSnd.sh:

`genSnd.sh tfrom tto`
`genSnd.sh tfrom +length`
`genSnd.sh p+n +length`

Use `+length` to specify the length of the recording from `tfrom` or `p+n`.
Use `p+n` ot specify the copy the value for tfrom from the paste board.

The `+n` adds `n seconds` to the pasteboard value. This is useful for a pulse
train to position the start of the snd extraction inside of the interval with
stimuli present.

# Sensor Analysis

---

Sensitivity and Spectral Estimation Scripts

## analyzeTone.sh
Usage: `analyzeTone [options] frequency_list`

#### Options:

| Option | Description                                          |
|--------|------------------------------------------------------|
| F      | fit frequency                                        |
| S      | don't regenerate spectra                             |
| v      | verbose output                                       |

#### Variables:

| Variable | Default Value | Description                              |
|----------|---------------|------------------------------------------|
| oroot    | Results       | output root                              |
| oext     | ""            | output extension                         |
| odir     | Spec          | output directory for spectra             |
| fmax     | 50            | maximum frequency                        |
| zeroPad  | 2             | zero pad factor                          |
| nOverlap | 2             | overlap factor                           |
| N        | 0             | total number of points to process        |
| tlen     | 0             | length of signal to process in seconds (overrides N) |
| tskip    | 0             | time to skip from beginning              |
| nskip    | 0             | number points to skip from beginning     |
| nfilt    | 0             | points per window (overrides nwin)       |
| nwin     | 2             | window size, otherwise automatically chosen |
| iref     | 4             | sensor number to use as reference        |
| cref     | 0.1           | sensor calibration reference value       |
| istart   | 4             | initial channel to analyze               |
| iend     | 9             | final channel to analyze                 |

## genPa.sh
Converts text files into pascal files using the sensitivity file generated by
analyzeTone.sh.

Note if you use genPa.sh Spec/*.pspec, these are assumed to be power spectra
generated by genPSpec.new.sh (see below for command documentation). In this
case, the program will automatically use the square of the sensitivity to
correctly convert the results into Pa2/Hz.

Usage: `genPa.sh [Results file] text_files`

#### Variables:

| Variable | Default Value           | Description               |
|----------|-------------------------|---------------------------|
| ext      | sens                    | output extension          |
| sensFile | Results.f0.5.sens.txt   | file to use for conversion|

## genRSpec.new.sh
Generates amplitude spectra. Typically called from analyzeTone.sh. This saves
the spectra to Spec directory by default. Note that this command expects the
format root.cn.snd for the files, where root is the root name for the run being
processed, and n is an integer. The variables “istart” and “iend” control the
range of channels to be processed.
Usage: `genSpec.new.sh [args] [files]`

#### Flags:

| Option | Default Value | Description                              |
|--------|---------------|------------------------------------------|
| M      | False         | compute median spectrum                  |
| v      | True          | verboseFlag output                       |

#### Variables:

| Variable | Default Value | Description                              |
|----------|---------------|------------------------------------------|
| odir     | Spec          | output directory                         |
| oext     | ""            | output extension                         |
| nfilt    | 0             | filter size (otherwise compute using file size, nwin & nskip) |
| nskip    | 0             | skip amount                              |
| tskip    | 0             | skip amount in seconds (over-rides nskip) |
| tlen     | 0             | duration to process (overrides N)        |
| N        | 0             | number of points to process              |
| winType  | 2             | window type                              |
| nwin     | 2             | number of windows in spectral analysis   |
| nOverlap | 2             | number of windows to overlap             |
| fmin     | 0             | minimum frequency to output              |
| fmax     | 50            | maximum frequency to output              |
| istart   | 4             | initial channel to analyze               |
| iend     | 9             | final channel to analyze                 |

## genPSpec.new.sh

Generates power-spectral density files. This saves the spectra to Spec directory
by default. Note that this command expects the format root.cn.snd for the files,
where root is the root name for the run being processed, and n is an integer.
The variables “istart” and “iend” control the range of channels to be processed.
Usage: `genPSpec.new.sh [args] [files]`

#### Flags:

| Option | Default Value | Description                              |
|--------|---------------|------------------------------------------|
| T      | False         | apply threshold cuts                     |


#### Variables:

| Variable | Default Value | Description                              |
|----------|---------------|------------------------------------------|
| odir     | Spec          | output directory                         |
| oext     | ""            | output extension                         |
| nfilt    | 0             | filter size (otherwise compute using file size, nwin & nskip) |
| nskip    | 0             | skip amount                              |
| tskip    | 0             | skip amount in seconds (over-rides nskip) |
| tlen     | 0             | duration to process (overrides N)        |
| N        | 0             | number of points to process              |
| winType  | 2             | window type                              |
| nwin     | 2             | number of windows in spectral analysis   |
| nOverlap | 2             | number of windows to overlap             |
| fmin     | 0             | minimum frequency to output              |
| fmax     | 50            | maximum frequency to output              |
| istart   | 4             | initial channel to analyze               |
| iend     | 9             | final channel to analyze                 |

## genAdaptNP.sh
Power least-square-fit script that computes the amplitude of a tone (and its N
harmonics) as a function of time. Note: This program is called by
`genRDiff.long.sh`

Usage: `genAdaptN.sh [options] [file list]`

#### Variables:

| Variable | Default Value      | Description                                             |
|----------|--------------------|---------------------------------------------------------|
| root     | ""                 | root of sound files (use to select which files to read) |
| oroot    | ""                 | root of outputs (default is based on input snd file name) |
| f        | 0.5                | fundamental to analyze                                  |
| N        | 10                 | total number of harmonics to analyze                    |
| oext     | ""                 | extension for output                                    |
| nfilt    | 1600               | output filter size                                      |
| nstep    | 10                 | output filter step size                                 |
| filtDir  | Filt               | directory to write (intermediate) filtered files to     |

# Sensitivity and Spectral Estimation Binaries

---

## dfft

DFFT (which stands for double-precision fast-Fourier transform) is a
general-purpose tool for performing spectral analysis on data. This tool relies
on the FFTW library. It stores the “wisdom” from individual runs to the user’s
home directory `~/.wisdom`, which allows faster spectra analyzes (by using `-Z`,
FFTW wisdom can be avoided, and the FFTW “best guess” is used instead).

The main outputs generated are amplitude spectra (performed with the `-P`
option), appropriately normalized for peak amplitude, and PSD spectra (performed
with the `+P` option). A variety of other capabilities are present, including
the capability of performing threshold rejection of noisy data (use the `+T` flag
together with the “threshold” variable). By default, PSD estimates use the
normalized equivalent noise bandwidth. For narrow band signals, include the `-n`
flag, which will produce “power spectra” estimates instead. The `+N` flag can be
used to normalize the PSD output to the true root-mean square of the signal (
using the correct normalization from the associated window function). 

[See [this paper on spectrum and spectral density](http://hdl.handle.net/11858/00-001M-0000-0013-557A-5 "Heinzel, G., Rüdiger, A., & Schilling, R. (2002). Spectrum and spectral density estimation by the Discrete Fourier transform (DFT), including a comprehensive list of window functions and some new at-top windows.") for more details on the various weighting factors as well as specifications for various window functions.]

The `+M` flag will compute the median rather than the root-mean square (or
mean-square) value. This is useful for signal data, rather than power or
amplitude estimations. The `+Z` option gives the lowest value in each bin, which
is again useful for signal detection. The AWSUMk processor can also be invoked
using the “+K” option. Finally, a range of amplitudes can be analyzed using the
+F flag together with the “fracStart” and “fracEnd” variables.
The currently implemented window functions are the rectangular, Welch, Hamming,
Hann, Parzen (triangular), Blackman and modified Gaussian widows, which can be
selected using the “winType” variable. The overlap fraction can be controlled
via `nOverlap`, and the windows can be zero padded using `zeroPad`.
Using the flags “+w8”, the spectra for individual windows can be output either
as complex-valued amplitude (with `-P`) or complex-valued linear spectral
density (with `+P`).


Documentation for these tools will be provided in a future release of this
document:
1. `sndavg`
2. `sndavgfilter`
3. `sndbutterworth2`
4. `sndenv`
5. `sndpeaks`

# Transfer Function and Self-Noise Estimation Scripts

---

## genwdir.cal.sh
This tool is used to generate “wdir” (windowed FFT) directories for all “.snd”
files in the current directory. There are a limited number of options available:
Flags
u:True: update directory (do not overwrite existing wdir directories)

#### Variables:

| Variable | Default Value | Description                   |
|----------|---------------|-------------------------------|
| twin     | 1000          | time window length in seconds |
| winType  | hann          | window type for analysis      |

Use `-u` to force recreating all wdir directories. For typical infrasound
applications, `twin=1000` is the appropriate choice.

## genSegStats.sh

This shell script is used to statistically analyze window segments generated by
the wdirmike2rseg and wdirmike2rseg_quad binaries.

Transfer Function and Self-Noise Estimation Binaries
Currently only the wdir* binaries are available for distribution. A more
streamlined set, which compute the FFTs inline, are in development. MATLAB
versions are also under development as well.

**Note that numerical issues occur when the signal-to-noise ratio is too large and
can result in numerical underflow.** Quad precision versions of the code are
provided that helps alleviate this problem, **but requires a quad-precision
enabled compiler.** The quad versions end with `_quad`.

## wdirmike2

This is the standard reference microphone method. See e.g., MATLAB’s tfestimate
for similar code. This produces biased output when the signal-to-noise is poor.

Typical calling syntax is:

`wdirmike2 +v  *.{c4,c5,c6,c7,c8,d9}.wdir`
The first sensor listed will be used as the reference.

## wdirmike2r

This is an implementation of the two-microphone regression method. This assumes
the two microphones are similar. 

Typical calling syntax is:

`wdirmike2r +v  *.{c4,c5,c6,c7,c8,d9}.wdir`

The first two sensors listed will be used as the references by default.

## wdirmike2r_quad
Quadruple precision of the two-microphone regression method

## wdirmike2rseg
Two-microphone regression method with “segments”.. This version writes out
averages of subsets of the total number of windows. Use “navg” to control how
many windows are averaged in each subset. 

Typical syntax is: `wdirmike2rseg +v odir=Mike2r.segs *.{c4,c5,c6,c7,c8,d9}.wdir`

This method allows ordinary statistical analysis to be performed on the “wdir”
directories in order to arrive at uncertainty estimates for the transfer
function and self noise values.  
Running genSegStats.sh on the segments directory, e.g.: `genSegStats.sh Mike2r.segs`
will produce a “.stats” directory containing the mean values and uncertainty
estimates for the transfer function and self noise values.

## wdirmike2rseg_quad

Quadruple precision of the two-microphone regression method with segments.

## wdirmike2seg
The standard reference microphone method with “segments”.

## wdirmike3
Sleeman’s 3-microphone method. Typical syntax is:
`wdirmike3 +v odir=Mike3 *.{c4,c5,c6,c7,c8,d9}.wdir`
The first two sensors listed will be used as the references by default.

## wdirmike3_quad
Quad-precision version of the Sleeman 3-microphone method.

