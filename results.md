
# DIA Analysis using OpenSwathWorkflow

***note**:* *Here I will go through the data analysis of DIA mass spectrometry data of human and e-coli peptides and I will get first impressions on which of the spiked samples might contain higher amounts of Ecoli peptides.* I*n a different study, I will perform indepedently the generation fo the DIA library  that will be used here.*
## Questions:


-   How to analyze data independent acquisition (DIA) mass spectrometry (MS) data using OpenSwathWorkflow?
    
-   How to detect different spiked amounts of Ecoli in a HEK-Ecoli Benchmark DIA dataset?

## Objectives:

-   Analysis of HEK-Ecoli Spike-in DIA data in Galaxy
    
-   Understanding DIA data principles and characteristics
    
-   Using OpenSwathworkflow to analyze HEK-Ecoli Spike-in DIA data

## Theoretical background:

In bottom-up proteomics, proteins are digested with a specific protease into peptides and the measured experimentally peptides are in silico reassembled into the corresponding proteins. Inside the mass spectrometer, not only the peptides are measured (MS1 level), but the peptides are also fragmented into smaller peptides which are measured again (MS2 level). This is referred to as tandem-mass spectrometry (MS/MS). Identification of peptides is performed by peptide spectrum matching of the theoretical spectra generated from the input protein database (fasta file) with the measured MS2 spectra. Peptide quantification is most often performed by measuring the area under the curve of the MS1 level peptide peaks, but special techniques such as TMT allow to quantify peptides on MS2 level. Nowadays, bottom-up tandem-mass spectrometry approaches allow for the identification and quantification of several thousand proteins.

In clinical proteomic studies often two or more conditions should be compared against each other, thus focusing on the proteins which were found in all conditions. Using the *data dependent acquisition (DDA) approach* could lead to ***limited numbers*** of cumulative proteins, due to method instrinsic dependencies (e.g. if the same peptides is selected for fragmentation in all measurements). Over the last decade Data independent acquisition (DIA) circumvents these limitations. In a DIA method, **precursor ions are isolated into pre-defined isolation windows and fragmented; all fragmented ions in each window are then analyzed by a high-resolution mass spectrometer**. ([Ludwig  _et al._  2018](https://training.galaxyproject.org/training-material/topics/proteomics/tutorials/DIA_Analysis_OSW/tutorial.html#Ludwig2018)). 



intro_1.png

Using the same m/z windows for all MS2 measurements, results in more reproducible fragmentation and potential identification across multiple measurements. But the resulting MS2 spectra from these matrices contain fragments from multiple peptides and are often more complex and direct MS1-MS2 alignment of a single m/z  is not possible.
intro_2.png
In order to perform the identification of peptides in those sophisticated MS2 spectra, we use spectral libraries from experimental data  (from previous DDA measurements). In more recent approaches the MS2 spectra can be predicted based on theoretical peptide sequences (e.g. from a protein database).
intro_3.png
OpenSwath_  ([Röst  _et al._  2014](https://training.galaxyproject.org/training-material/topics/proteomics/tutorials/DIA_Analysis_OSW/tutorial.html#Rost2014)), is an open-source software option for DIA data analysis which requires a spectral library, retention time alignment peptides as well as the DIA MS data.

The dataset in this tutorial consists of two different spiked mixtures of human and Ecoli peptides, with an increase of the Ecoli peptides in one of the spikes. For each Spike-in there a four replicate measurements to allow for statistical analysis later on. Here I will go through the data analysis of DIA mass spectrometry data and will get first impressions on which of the Spike-ins might contain higher amounts of Ecoli peptides.

## Bioinformatics Tools:

msConvert, OpenSwathWorkflow, Pyprophet


## Table of contents of the workflow

###  1. Get data

 - Creating a new **history**   
 - **Importing** data via links   
 - Creating a **dataset collection**

### 2.  Raw File conversion with msconvert

 - Converting vendor specific **raw** to open **mzML** format

### 3.  DIA analysis using OpenSwathWorkflow

 - Combining osw results using **PyProphet merge**

 ### 4.  False Discovery Rate estimation using **PyProphet score**

 - Apply scores with **PyProphet peptide**

 - Apply scores with **PyProphet protein**

 - Exporting the results with **PyProphet export**
## Get data

Firstly, we create a new history in galaxy platform and we import the fasta and raw files as well as the sample annotation and the iRT Transition file (contains information about the transitions of the Indexed Retention Time (iRT) standard peptides) from [Zenodo](https://zenodo.org/record/4307762). These peptides are a set of synthetic peptides with well-defined and stable retention times across different liquid chromatography-mass spectrometry (LC-MS) systems.

-   Copy the link location
-   Click  galaxy-upload  **Upload Data**  at the top of the tool panel
    
-   Select  galaxy-wf-edit  **Paste/Fetch Data**
-   Paste the link(s) into the text field
    
-   Press  **Start**
    
-   **Close**  the window

Once the files are imported, I rename the sample annotation file in ‘Sample_annotation’, the spectral library in ‘HEK_Ecoli_lib’, the iRT transition file in ‘iRTassays’ and the raw files in ‘Sample1.raw’, ‘Sample2.raw’, ‘Sample3.raw’, ‘Sample4.raw’, ‘Sample5.raw’, ‘Sample6.raw’, ‘Sample7.raw’ and ‘Sample8.raw’
get_data3a

-   Click on the  galaxy-pencil  **pencil icon**  for the dataset to edit its attributes
-   In the central panel, change the  **Name**  field
-   Click the  **Save**  button
get_data3b
Then, we generate a collection for all .raw files (and name it DIA_data)
by clicking on galaxy-selector  **Select Items** at the top of the history pane
get_data4
We check all the datasets and create advanced build list.
get_data5
We choose flat list 
get_data6

Then we cleanup prefixes/suffixes from the names by double clicking on the file names to edit. For example, remove file extensions or common prefix/suffixes to cleanup the names.
get_data7


## raw File conversion with  **msconvert**

Converting vendor specific raw to open mzML format
1.  **msconvert Convert and/or filter mass spectrometry files**  
    -   param-collection  _“Input unrefined MS data”_:  `DIA_data`
    -   _“Do you agree to the vendor licenses?”_:  `Yes`
    -   _“Output Type”_:  `mzML`
    -   In  _“Data Processing Filters”_:
        -   _“Apply peak picking?”_:  `Yes`
        -   _“Demultiplex overlapping or MSX spectra”_:  `Yes`
            -   _“Optimization”_:  `Overlap only`
    -   In  _“General Options”_:
        -   _“SIM as Spectra”_:  `Yes`
        -   _“Ignore unknown instrument error”_:  `Yes`
    -   In  _“Output Encoding Settings”_:
        -   _“Intensity Encoding Precision”_:  `64`

Output image: msconvert.png

## DIA analysis using  **OpenSwathWorkflow**
OpenSWATH [[1]](https://openswath.org/en/latest/docs/openswath.html#id12) is a proteomics software that allows analysis of LC-MS/MS DIA (data independent acquisition) data using the approach described by Gillet et al. [[2]](https://openswath.org/en/latest/docs/openswath.html#id13)

It provides a complete, integrated analysis tool without the need to run multiple tools consecutively.

**OpenSwathWorkflow** executes the following steps in order:

 -   Reading of the raw input file (provided as mzML, mzXML or sqMass) and RT normalization transition list
 -   Computing the retention time transformation using RT normalization peptides
 -   Reading of the transition list
 -   Extracting the specified transitions
 -   Scoring the peak groups in the extracted ion chromatograms (XIC)
 -   Reporting the peak groups and the chromatograms
 
The *spectral library* `tr` is a spectral library either in `.tsv`, `.TraML` or `.PQP` format 

The *input file* `in` is generally a single `mzML`, `mzXML` or `sqMass` file (converted from a raw vendor file format using [ProteoWizard](http://proteowizard.sourceforge.net/)).

The *retention time normalization* peptides are provided using the optional parameter `tr_irt` in TraML format.

The OpenSwathWorkflow produces two types of *output*:

-   identified peaks
-   extracted chromatograms

the identified peaks can be stored in tsv format using  `-out_tsv`  , but here we will choose to produce output in SQLite format using  `-out_osw`
 
**OpenSwathWorkflow Complete workflow to run OpenSWATH**  (  Galaxy version 3.1+galaxy0)

-   param-collection  _“Input files separated by blank”_:  `DIA_data`  (output of  **msconvert**  tool)
-   param-file  _“transition file (‘TraML’,’tsv’,’pqp’)”_:  `HEK_Ecoli_lib`
-   param-file  _“transition file (‘TraML’)”_:  `iRTassays`
-   _“Extraction window in Thomson or ppm (see mz_extraction_window_unit)”_:  `10.0`
-   _“Extraction window used in MS1 in Thomson or ppm (see mz_extraction_window_ms1_unit)”_:  `10.0` ***comment*** *Here we analyze data acquired on a QExactive Plus MS instrument which uses an Orbitrap and generates high resolution data.* *Therefore, we allow for 10 ppm mass tolerance for both the MS1 and the MS2 level*
-   In  _“Parameters for the RTNormalization for iRT petides”_:
    -   _“Which outlier detection method to use (valid: ‘iter_residual’, ‘iter_jackknife’, ‘ransac’, ‘none’)”_:  `none`
    -   _“Whether the algorithms should try to choose the best peptides based on their peak shape for normalization”_:  `Yes`
    -   _“Minimal number of bins required to be covered”_:  `7`***comment*** *This number can be set to lower values if for some reasons fewer iRT peptides were found in some of the measurements.*
-   In  _“Scoring parameters section”_:
    -   In  _“TransitionGroupPicker”_:
        -   _“Minimal peak width (s), discard all peaks below this value (-1 means no action)”_:  `5.0`
        -   _“Tries to compute a quality value for each peakgroup and detect outlier transitions”_:  `Yes`
    -   In  _“Scores”_:
        -   _“Use the MI (mutual information) score”_:  `No`
-   _“Advanced Options”_:  `Show Advanced Options`
    -   _“Output an XIC with a RT-window by this much large”_:  `100.0`
    -   _“Extraction window used for iRT and m/z correction in Thomson or ppm (see irt_mz_extraction_window_unit)”_:  `10.0`
    -   _“Whether to run OpenSWATH directly on the input data, cache data to disk first or to perform a datareduction step first”_:  `cacheWorkingInMemory`
    -   _“Use the retention time normalization peptide MS2 masses to perform a mass correction (linear, weighted by intensity linear or quadratic) of all spectra”_:  `regression_delta_ppm`
-   _“Optional outputs”_:  `out_osw`

