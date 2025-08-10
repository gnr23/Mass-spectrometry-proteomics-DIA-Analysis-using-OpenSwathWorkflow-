\# DIA Analysis Using OpenSwathWorkflow (Galaxy Tutorial)

This repository contains my hands-on learning experience following the \*\*Galaxy Training Network\*\* tutorial on \*\*Data Independent Acquisition (DIA) proteomics analysis\*\* using \*\*OpenSwathWorkflow\*\* and \*\*PyProphet\*\*.

\---

\#\# Overview

This tutorial walks through the end-to-end analysis of DIA mass spectrometry data using Galaxy. It demonstrates how to:

\- Convert raw files to mzML

\- Perform peptide identification and quantification using \*\*OpenSwathWorkflow\*\*

\- Merge results and estimate false discovery rates (FDR) with \*\*PyProphet\*\*

\- Export identification and quantification results for downstream statistical analysis

The dataset used is a \*\*HEK–E. coli spike-in benchmark\*\*, where varying amounts of E. coli proteins are spiked into a human cell background.

\---

\#\# Objectives

\- Understand principles of \*\*DIA proteomics\*\* and how it differs from DDA

\- Learn how to use \*\*OpenSwathWorkflow\*\* with a spectral library in Galaxy

\- Apply \*\*semi-supervised scoring\*\* (XGBoost-based) in \*\*PyProphet\*\*

\- Export final peptide/protein quantification results, suitable for tools like \*\*MSstats\*\*

\---

\#\# ​ Tutorial Snapshot

\| Step \| Description \|

\|------\|-------------\|

\| 1 \| \*\*Get Data:\*\* Import raw files, spectral library (PQPs), iRT assays, and sample annotation from Zenodo \|

\| 2 \| \*\*Convert files:\*\* Use \`msconvert\` to turn vendor files into mzML, with peak picking and demultiplexing \|

\| 3 \| \*\*Run DIA analysis:\*\* Use \`OpenSwathWorkflow\` on mzML files with spectral library and iRT data \|

\| 4 \| \*\*Merge results:\*\* Combine runs using \`PyProphet merge\` \|

\| 5 \| \*\*Score & estimate FDR:\*\* Apply \`PyProphet score\` (XGBoost classifier), then peptide- and protein-level scoring \|

\| 6 \| \*\*Export outputs:\*\* Generate quantification tables using \`PyProphet export\`, optionally with \`swath2stats\` for statistical downstream analysis \|

\---

\#\# Why It Matters

DIA approaches, like SWATH-MS, provide reproducible and comprehensive proteome quantification without being limited by real-time selection of precursor ions. This makes DIA increasingly popular in clinical and large-scale proteomics.

\---

\#\# My Notes and Outputs

I've captured the steps, parameter choices, and some insights in:

\- \`notebooks/\`: Markdown or Jupyter notebooks with my workflow, challenges, and practical commentary

\- \`results/\`: Exported files (e.g., protein quant tables, intermediate results) and screenshots of key steps

\- \`README.md\`: This file summarizing the exercise

\---

\#\# Next Steps

\- Explore downstream statistical analysis (e.g., using \*\*MSstats\*\*)

\- Integrate visualization of differential E. coli spike-in levels

\- Adapt this workflow to other DIA datasets or spectral libraries

\---

\#\# References

\- Tutorial: \*\*DIA Analysis using OpenSwathWorkflow\*\*, Galaxy Training Network (Last updated: May 2025)

:contentReference[oaicite:0]{index=0}

\- Röst et al. (2014): \*OpenSWATH enables automated, targeted analysis of DIA MS data\*

:contentReference[oaicite:1]{index=1}

\---

\*\*Note:\*\* This tutorial content is licensed under CC-BY 4.0.
