# rhythm-acoustics-cadlin
Scripts and data for the paper "Envelope-Based Metrics Reveal Rhythmic Development Tendencies in Brazilian L2 English Learners". 

# Envelope-based metrics across L1 and L2 systems

This repository contains scripts and data used in the study submitted to *Cadernos de Linguística (CadLin)*, 
analyzing speech rhythm across L1 and L2 corpora using chunk-based segmentation and envelope-based metrics.

## 📁 Structure
- `data/` — preprocessed chunk-level datasets (`chunk_metrics.csv`, `chunk_metrics_grid_intervals.csv`)
- `scripts/` — R and MATLAB scripts used to process chunks and extract metrics. 

## ⚙️ Requirements
- R ≥ 4.3  
  Required packages: `rPraat`, `dplyr`, `readr`, `tidyverse`
- Praat ≥ 6.3.0

## 🚀 Usage
1. Run `processTextGrid_Strict.R` to generate chunk-based TextGrids with VC and VV tiers.  
2. Run the Praat script `MetricsAcousticsExtractor.txt` on those TextGrids to extract rhythm and acoustic metrics.
3. Merge outputs into your analysis dataset.

## 🧩 Authors
- Leonardo Antonio Silva Teixeira – Universidade Federal do Ceará (UFC) / Cornell University / IFCE
- Scripts adapted from Sam Tilsen’s workflow and Silva Jr. & Barbosa (2019–2020).

## 📜 Citation
If you use these scripts, please cite:
> SILVA Jr., L.; BARBOSA, P. A. *MetricsAcousticsExtractor* (v1.7.7). Praat Script, 2019.

## 🪪 License
MIT License — see `LICENSE` file.
