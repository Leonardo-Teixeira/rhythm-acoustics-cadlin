# rhythm-acoustics-cadlin
Scripts and data for the paper "Envelope-Based Metrics Reveal Rhythmic Development Tendencies in Brazilian L2 English Learners". These scriopts and data were developed as part of my PhD research "Towards a Multidimensional Model of L2 Speech Rhythm".

# Envelope-based metrics across L1 and L2 systems

This repository contains scripts and data used in the study submitted to *Cadernos de Linguística (CadLin)*, 
analyzing speech rhythm across L1 and L2 corpora using chunk-based segmentation and envelope-based metrics.

## Structure
- `data/` — preprocessed chunk-level datasets (`chunk_metrics.csv`, `all_chunk_info.csv`, `all_speech_activity_intervals.csv`, `chunk_metrics_grid_intervals.csv`)
- `scripts/` — R and MATLAB scripts used to process chunks and extract metrics. 

## Requirements
- R ≥ 4.3  
  Required packages: `rPraat`, `dplyr`, `readr`, `tidyverse`
- Praat ≥ 6.3.0

## Usage
The pipeline is composed of five main scripts — three in R and two in MATLAB — and its final output is the table chunk_metrics.csv, which contains the metrics for each speech segment (chunk).
1.1. Amplitude Envelope Extraction (R)
Script: `1_extract_envelope.R`
In this script, each .wav file is processed to extract its amplitude envelope. The sequence of operations includes:
●	Normalizing the signal to range from -1 to 1;
●	Bandpass filtering (80–10,000 Hz) to isolate the vocal range; (We can use 3000 or 4000 to avoid bumps of fricatives; 400 - 4000 can be used as in Tilsen and Arvaniti, 2013)
●	Taking the absolute value and applying a lowpass filter (10 Hz) to smooth the signal;
●	Resampling to 100 Hz using interpolation.
The resulting envelope is stored as a named list (envelope_data) linked to each audio file1_extract_envelope.

1.2. Speech Activity Detection (R)
Script: `2_extract_intervals.R`
 From the envelopes obtained in the previous step, this script detects speech and silence segments based on a fixed threshold (0.05) (it could be worth changing it to 0.1). The process includes:
●	Detecting activity transitions (speech vs. silence);
●	Filtering out short silence/speech segments shorter than 0.1s;
●	Calculating onset and offset times (t0, t1) for each interval;
●	Saving all intervals into `all_speech_activity_intervals.csv` and a corresponding .RData file.
Each row represents a continuous sequence of speech or silence, which is used in the next step to extract chunks.

1.3. Chunk Segmentation (R)
Script: `3_extract_chunks.R`
This script loads the speech intervals and divides them into ~2.5 second chunks with 50% overlap. (it might be changed to not allow for the overlap with the caveat of having less data points) 
The steps are:
●	Selecting segments of type "speech";
●	Generating sliding windows (t0, t1) with a target duration (targ_dur) of 2.5s and a step of 1.25s;
●	Excluding any windows shorter than 1s;
●	Generating the file chunk_info.RData with metadata for each chunk (start, end, duration)3_extract_chunks.

1.4. Spectral and Rhythm Metrics Extraction (MATLAB)
Script: `4_1_extract_env_metrics.m`
 The MATLAB script processes the previously defined chunks, applying functions from the EMD_amplitude_envelope package to extract envelope-based metrics. It performs the following:
●	Extracts audio segments based on the time windows in chunk_info.csv;
●	Resamples to 44.1 kHz if needed;
●	Applies the envm_metrics_batch() function with a 10 Hz lowpass filter;
●	Consolidates results in the `chunk_metrics.csv` table, with one row per chunk and multiple spectral metrics (e.g., sbpr_1, scntr_1, imf_ratio21, pow_imf1, pow_imf2)

1.5. Generating Voice Activity TextGrids (MATLAB)
Script: `4_2_gen_voiceactivity_grids.m`
After detecting intervals of speech and silence, this script creates Praat TextGrids marking those segments. It uses the file all_speech_activity_intervals.csv and performs the following steps:
●	Loads the CSV file and identifies all unique audio file names;
●	For each file, extracts the corresponding intervals (speech, sil);
●	Assigns labels to the intervals and inserts gap labels where necessary (to avoid overlap or discontinuities);
●	Uses pl_write_textgrid() to export the tier to a .TextGrid file in the Grids/ folder.

This step is essential to prepare structured TextGrids with a single tier (speech/sil) for each audio file. These TextGrids will be filtered and aligned to chunks in the next stage of the pipeline.

## Authors
- Leonardo Antonio Silva Teixeira – Universidade Federal do Ceará (UFC) / Instituto Federal do Ceará (IFCE) / Cornell University 
- Sam Tilsen - Cornell University
- Scripts adapted from Sam Tilsen’s workflow.

## Citation
If you use the R scripts, please cite:
> TEIXEIRA, L.A.S.; TILSEN, S. *EnvelopeMetricsL2Rhythm* (v1.1.2). R Script, 2025.
If you use the MATLAB scripts, please cite:
> TILSEN, S. *EnvelopeMetrics* MATLAB Script, 2025. 

## License
MIT License — see `LICENSE` file.
