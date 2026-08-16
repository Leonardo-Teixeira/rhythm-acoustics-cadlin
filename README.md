# rhythm-acoustics-cadlin

Scripts and derived data for the paper **“Envelope-Based Metrics Reveal Speech Rhythm Development Tendencies in Brazilian L2 English Learners.”**

These materials were developed as part of the PhD research project *Towards a Multidimensional Model of L2 Speech Rhythm*.

## Overview

This repository contains the scripts and shareable derived data used in the study submitted to *Cadernos de Linguística (CadLin)*. The study analyzes speech rhythm across native Brazilian Portuguese (BP-L1), native English (EN-L1), and four longitudinal stages of Brazilian learners’ English (EN-L2-1 to EN-L2-4) using chunk-based segmentation and envelope-based rhythm metrics.

The preprocessing pipeline first identifies continuous speech regions using Voice Activity Detection (VAD). Analysis chunks are then generated within these speech regions using sliding windows with a target duration of 2.5 s and a step of 1.25 s. Consequently, chunks do not cross VAD-defined silence boundaries, but their internal boundaries are determined by the sliding-window procedure rather than by syntactic or prosodic annotation.

## Repository structure

- `data/` — derived and preprocessed datasets used in the workflow, including:
  - `chunk_metrics.csv` — final chunk-level envelope-based measurements used in the analyses;
  - `all_chunks_info.csv` — onset, offset, duration, and associated metadata for the analysis chunks;
  - `all_speech_activity_intervals.csv` — speech and silence intervals identified during Voice Activity Detection;
  - `chunk_metrics.xlsx` — spreadsheet version of the chunk-level measurement table;
  - `Grids/` — Praat TextGrids representing the detected speech/silence intervals.

- `1_extract_envelope.R` — amplitude-envelope extraction.
- `2_extract_intervals.R` — Voice Activity Detection and speech/silence interval extraction.
- `3_extract_chunks.R` — generation of analysis chunks within detected speech intervals.
- `4_1_extract_env_metrics.m` — extraction of envelope-based rhythm metrics.
- `4_2_gen_voiceactivity_grids.m` — generation of Praat TextGrids from speech-activity intervals.

## Requirements

* R ≥ 4.3
* R packages used by the preprocessing scripts: `seewave`, `signal`, and `ggplot2`
* MATLAB
* `EMD_amplitude_envelope` MATLAB functions used for envelope-based metric extraction
* Praat ≥ 6.3.0 for inspection and use of the generated TextGrids

## Analysis workflow

The preprocessing and metric-extraction workflow consists of three R scripts followed by two MATLAB scripts. The parameters described below correspond to those used for the analyses reported in the manuscript.

### 1. Amplitude Envelope Extraction

**Script:** `1_extract_envelope.R`

Each `.wav` recording is processed to extract its amplitude envelope. The script:

1. normalizes the waveform;
2. applies a fourth-order Butterworth bandpass filter from 80 to 10,000 Hz;
3. performs full-wave rectification by taking the absolute value of the filtered signal;
4. applies a fourth-order Butterworth low-pass filter at 10 Hz;
5. normalizes the resulting envelope; and
6. resamples the envelope to 100 Hz using interpolation.

The resulting envelopes are stored in the `envelope_data` object and used for speech-activity detection.

### 2. Speech Activity Detection

**Script:** `2_extract_intervals.R`

Speech activity is detected from the amplitude envelopes using a fixed normalized-amplitude threshold of 0.05.

The script:

1. identifies transitions between speech and silence;
2. calculates the duration of each detected interval;
3. applies a minimum-duration tolerance of 0.1 s to both speech and silence intervals;
4. removes short intervals and merges consecutive intervals of the same type; and
5. calculates onset (`t0`) and offset (`t1`) times for the resulting intervals.

The resulting intervals are classified as `speech` or `sil` and exported to `all_speech_activity_intervals.csv`, with a corresponding R data file.

These VAD-defined intervals determine the continuous speech regions within which analysis chunks are generated in the next stage.

### 3. Chunk Segmentation

**Script:** `3_extract_chunks.R`

Only intervals classified as `speech` are used for chunk generation. Within each speech interval, the script generates sliding windows using the following parameters:

* target chunk duration: **2.5 s**;
* step between consecutive window onsets: **1.25 s**;
* overlap between consecutive full-length windows: **50%**;
* minimum retained chunk duration: **greater than 1 s**.

Each window extends for up to 2.5 s. When less than 2.5 s remains before the end of a VAD-defined speech interval, the window is truncated at that boundary. Windows with durations of 1 s or less are discarded.

Thus, VAD determines the speech regions eligible for analysis and prevents chunks from crossing detected silence boundaries. Within those speech regions, the final analysis-chunk boundaries are determined by the sliding-window procedure. Chunk boundaries are not based on syntactic or prosodic annotation.

The resulting chunk metadata contain the onset, offset, and duration of each retained chunk and are used for subsequent acoustic analysis.

### 4. Spectral and Rhythm Metric Extraction

**Script:** `4_1_extract_env_metrics.m`

The MATLAB script extracts the waveform segment corresponding to each previously defined analysis chunk and calculates envelope-based rhythm metrics using functions from the `EMD_amplitude_envelope` package.

For each chunk, the script:

1. extracts the corresponding interval from the original audio recording;
2. resamples the audio to 44.1 kHz when the original sampling rate is lower;
3. applies `envm_metrics_batch()` with a 10-Hz low-pass parameter; and
4. combines the resulting measurements with the chunk metadata.

The resulting `chunk_metrics.csv` file contains one row per analyzed chunk and the envelope-based measurements used in the statistical analyses reported in the manuscript.

The principal metrics analyzed in the paper are:

* `sbpr_1` — Spectral Band Power Ratio;
* `scntr_1` — Spectral Centroid;
* `imf_ratio21` — IMF₂/IMF₁ power ratio.

### 5. Voice Activity TextGrid Generation

**Script:** `4_2_gen_voiceactivity_grids.m`

This auxiliary MATLAB script converts the detected speech-activity intervals into Praat TextGrids.

The script:

1. reads `all_speech_activity_intervals.csv`;
2. identifies the intervals associated with each audio recording;
3. labels intervals according to their speech-activity classification;
4. fills gaps with silence labels where required; and
5. exports one `.TextGrid` file per recording to the `Grids/` directory.

These TextGrids provide an inspectable representation of the VAD-based speech and silence segmentation.

## Data availability

The repository contains derived measurement tables and other shareable research materials needed to document and reproduce the reported analytical workflow. Original audio recordings are not included because human voice recordings constitute potentially identifiable participant data and are subject to ethical restrictions.

The archived version of the research materials is available through Zenodo:

**DOI:** 10.5281/zenodo.18818757

The GitHub repository serves as the development mirror of the archived materials.

## Authors

* **Leonardo Antonio Silva Teixeira** — Universidade Federal do Ceará (UFC) / Instituto Federal de Educação, Ciência e Tecnologia do Ceará (IFCE)
* **Sam Tilsen** — Cornell University

Scripts were developed from and adapted from Sam Tilsen’s envelope-analysis workflow.

## Citation

If you use the R scripts, please cite:

> TEIXEIRA, L. A. S.; TILSEN, S. *EnvelopeMetricsL2Rhythm* (v1.0.0). R Script, 2025.

If you use the MATLAB scripts, please cite:

> TILSEN, S. *EnvelopeMetrics*. MATLAB Script, 2025.

## License

Code is released under the MIT License. See the `LICENSE` file.

Shareable derived datasets and research materials are released under CC BY 4.0.
