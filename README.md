# OFDM vs OCDM vs OTFS vs AFDM BER Comparison

## Overview

This repository provides a MATLAB-based BER comparison of four multicarrier waveforms:

* OFDM — Orthogonal Frequency Division Multiplexing
* OCDM — Orthogonal Chirp Division Multiplexing
* OTFS — Orthogonal Time Frequency Space
* AFDM — Affine Frequency Division Multiplexing

The comparison is performed under two channel settings:

1. AWGN channel
2. High-mobility multipath Rayleigh fading channel

The main goal of this repository is to visualize how different waveform structures behave in terms of bit error rate under simple noise-limited and mobility-limited wireless channel assumptions.

## Important Scope Note

This project is a BER-level analytical/Monte Carlo comparison.

For the AWGN case, the script uses unitary waveform transformations. Since unitary transforms preserve Euclidean distance and noise statistics, OFDM, OCDM, OTFS, and AFDM produce the same BER under AWGN when the same modulation and energy normalization are used.

For the high-mobility Rayleigh fading case, the script uses literature-inspired effective diversity assumptions rather than full physical receiver implementations for every waveform. The model assumptions are:

* OFDM is Doppler-sensitive and suffers from inter-carrier interference.
* OCDM benefits from chirp spreading and is modeled with partial diversity.
* OTFS benefits from delay-Doppler processing and is modeled with near full path diversity.
* AFDM benefits from DAFT-domain path separation and is modeled as a full-diversity reference.

Therefore, the Rayleigh-fading section should be interpreted as a performance-level abstraction, not a complete waveform-specific detector simulation.

## Features

* BER comparison of OFDM, OCDM, OTFS, and AFDM
* AWGN and high-mobility Rayleigh channel selection
* BPSK modulation
* Monte Carlo BER estimation
* Theoretical BER curves
* OFDM unitary IFFT/FFT simulation
* OCDM Fresnel-type unitary transform simulation
* AFDM DAFT-based unitary transform simulation
* OTFS AWGN-equivalent unitary placeholder
* Rayleigh fading diversity abstraction
* Doppler ICI penalty modeling for OFDM and OCDM
* Full-diversity modeling for OTFS and AFDM
* Automatic plot generation and PNG export
* Reproducible random seed




## Quick Start

Clone the repository:

OR

Open MATLAB and run:

run .mlx file

To simulate the high-mobility multipath Rayleigh fading channel, set:

channelType = 1;
0 otherwise

## User Parameters

The main parameters are defined at the top of the script:

matlab

channelType = 0;          % 0 = AWGN, 1 = high-mobility multipath Rayleigh

N = 64;                   % Number of symbols per frame

numFrames = 2e4;          % Number of Monte Carlo frames

EbN0dB = 0:2:30;          % Eb/N0 range in dB

savePlot = true;          % Save result plot as PNG

AFDM chirp parameters:

c1 = 1 / (2 * N);

c2 = 1 / (2 * N);

High-mobility Rayleigh assumptions:

numPaths = 4;

ocdmDiversity = 2;

ofdmIciCoeff = 0.04;

ocdmIciCoeff = 0.008;

otfsLossDb = 0.8;

afdmLossDb = 0.0;

## Channel Models

### AWGN Channel

The AWGN channel adds complex Gaussian noise to the transmitted waveform:

y = x + n

### High-Mobility Multipath Rayleigh Channel

The high-mobility Rayleigh case is modeled using effective instantaneous SNR distributions.

OFDM is modeled as a single effective fading branch with Doppler-induced ICI:

gamma_eff = gamma / (1 + alpha_OFDM * gamma)

OCDM is modeled with partial diversity and reduced ICI:

gamma_eff = sum(|h_l|^2) * gamma / (1 + alpha_OCDM * gamma)

OTFS is modeled with full path diversity and a practical SNR loss:

gamma_eff = sum(|h_l|^2) * gamma * 10^(-loss_OTFS/10)

AFDM is modeled as an ideal full-diversity DAFT-domain reference:

gamma_eff = sum(|h_l|^2) * gamma * 10^(-loss_AFDM/10)

## Waveform Modeling

### OFDM

OFDM uses an IFFT operation at the transmitter and FFT operation at the receiver:

txOfdm = ifft(symbols, [], 1) * sqrt(N);
rxOfdmSymbols = fft(rxOfdm, [], 1) / sqrt(N);

### OCDM

OCDM is modeled using a unitary Fresnel-type transform:

C = FresnelLeft * F' * FresnelRight;
txOcdm = C' * symbols;
rxOcdmSymbols = C * rxOcdm;

### OTFS

For AWGN, OTFS is represented by an equivalent unitary placeholder because unitary domain transformations do not change BER in AWGN:

txOtfs = symbols;
rxOtfsSymbols = txOtfs + noise;

In the Rayleigh fading abstraction, OTFS is modeled with full delay-Doppler path diversity and a small practical SNR loss.

### AFDM

AFDM is modeled using the DAFT matrix:

A = LambdaC2 * F * LambdaC1;
txAfdm = A' * symbols;
rxAfdmSymbols = A * rxAfdm;

The chirp parameters c1 and c2 control the affine Fourier structure. In a full AFDM receiver, these parameters are selected based on the maximum delay and Doppler spread to separate channel paths in the DAFT domain.

## Theoretical BER

The repository includes theoretical BER references for:

### BPSK over AWGN

BER = 0.5 * erfc(sqrt(Eb/N0))

### BPSK over Rayleigh Fading

BER = 0.5 * (1 - sqrt(gamma / (1 + gamma)))

### BPSK with MRC Diversity

The Rayleigh diversity curves are computed using a closed-form MRC expression for a selected diversity order.

## Output

The plot includes:

* Simulated OFDM BER

* Simulated OCDM BER

* Simulated OTFS BER

* Simulated AFDM BER

* Theoretical OFDM BER

* Theoretical OCDM BER

* Theoretical OTFS BER

* Theoretical AFDM BER

## Suggested Future Improvements

* Add QPSK, 16-QAM, and 64-QAM support
* Add cyclic prefix modeling for OFDM and OCDM
* Add chirp-periodic prefix modeling for AFDM
* Implement a full OTFS delay-Doppler modulator and detector
* Implement a physical time-varying multipath channel
* Add pilot-based channel estimation
* Add ZF and MMSE equalizers
* Add runtime complexity comparison
* Export BER data to CSV and MAT files
* Add automated MATLAB unit tests
* Add separate scripts for AWGN and Rayleigh experiments

## Limitations

This repository does not yet implement complete physical-layer transceivers for all four waveforms under time-varying multipath fading.

The Rayleigh fading comparison uses effective diversity and ICI assumptions. These assumptions are useful for high-level BER comparison, but they should not be interpreted as a substitute for complete waveform-specific receiver simulations.

## Citation

If you use this repository in academic work, please cite it as:

@article{ahmaddual,
  title={Dual-Mode Chirp-Pair Indexing for AFDM-ISAC: 4-Bit Embedding With Fixed-Reference Sensing},
  author={Ahmad, Muneeb and Ahmed, Raza and Shin, Soo Young}
}

@ARTICLE{11322797,
  author={Ahmad, Muneeb and Baig, Sobia and Alqwaifly, Nawaf A.},
  journal={IEEE Open Journal of the Communications Society}, 
  title={Radar-Centric AFDM Waveform With Chirp-Domain Index Modulation for ISAC}, 
  year={2026},
  volume={7},
  number={},
  pages={844-857},
  doi={10.1109/OJCOMS.2025.3650729}}

## License

This project is released under the GPL License.
