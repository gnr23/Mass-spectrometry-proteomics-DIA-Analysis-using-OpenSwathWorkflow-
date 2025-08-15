# DIA Analysis using OpenSwathWorkflow

**note***:* *Here I will go through the data analysis of DIA mass spectrometry data of human and e-coli peptides and I will get first impressions on which of the spiked samples might contain higher amounts of Ecoli peptides.* I*n a different study, I will perform indepedently the generation fo the DIA library that will be used here.*

## Questions:

-   How to analyze data independent acquisition (DIA) mass spectrometry (MS) data using OpenSwathWorkflow?
-   How to detect different spiked amounts of Ecoli in a HEK-Ecoli Benchmark DIA dataset?

## Objectives:

-   Analysis of HEK-Ecoli Spike-in DIA data in Galaxy
-   Understanding DIA data principles and characteristics
-   Using OpenSwathworkflow to analyze HEK-Ecoli Spike-in DIA data

## Theoretical background:

In bottom-up proteomics, proteins are digested with a specific protease into peptides and the measured experimentally peptides are in silico reassembled into the corresponding proteins. Inside the mass spectrometer, not only the peptides are measured (MS1 level), but the peptides are also fragmented into smaller peptides which are measured again (MS2 level). This is referred to as tandem-mass spectrometry (MS/MS). Identification of peptides is performed by peptide spectrum matching of the theoretical spectra generated from the input protein database (fasta file) with the measured MS2 spectra. Peptide quantification is most often performed by measuring the area under the curve of the MS1 level peptide peaks, but special techniques such as TMT allow to quantify peptides on MS2 level. Nowadays, bottom-up tandem-mass spectrometry approaches allow for the identification and quantification of several thousand proteins.

In clinical proteomic studies often two or more conditions should be compared against each other, thus focusing on the proteins which were found in all conditions. Using the *data dependent acquisition (DDA) approach* could lead to **limited numbers** of cumulative proteins, due to method instrinsic dependencies (e.g. if the same peptides is selected for fragmentation in all measurements). Over the last decade Data independent acquisition (DIA) circumvents these limitations. In a DIA method, **precursor ions are isolated into pre-defined isolation windows and fragmented; all fragmented ions in each window are then analyzed by a high-resolution mass spectrometer**. ([Ludwig *et al.* 2018](https://training.galaxyproject.org/training-material/topics/proteomics/tutorials/DIA_Analysis_OSW/tutorial.html#Ludwig2018)).

intro_1.png

Using the same m/z windows for all MS2 measurements, results in more reproducible fragmentation and potential identification across multiple measurements. But the resulting MS2 spectra from these matrices contain fragments from multiple peptides and are often more complex and direct MS1-MS2 alignment of a single m/z is not possible. intro_2.png In order to perform the identification of peptides in those sophisticated MS2 spectra, we use spectral libraries from experimental data (from previous DDA measurements). In more recent approaches the MS2 spectra can be predicted based on theoretical peptide sequences (e.g. from a protein database). intro_3.png OpenSwath\_ ([Röst *et al.* 2014](https://training.galaxyproject.org/training-material/topics/proteomics/tutorials/DIA_Analysis_OSW/tutorial.html#Rost2014)), is an open-source software option for DIA data analysis which requires a spectral library, retention time alignment peptides as well as the DIA MS data.

The dataset consists of two different spiked mixtures of human and Ecoli peptides, with an increase of the Ecoli peptides in one of the spikes. For each Spike-in there a four replicate measurements to allow for statistical analysis later on. Here I will go through the data analysis of DIA mass spectrometry data and will get first impressions on which of the Spike-ins might contain higher amounts of Ecoli peptides.

## Bioinformatics Tools:

msConvert, OpenSwathWorkflow, Pyprophet

## Table of contents of the workflow

### 1. Get data

-   Creating a new **history**
-   **Importing** data via links
-   Creating a **dataset collection**

### 2. Raw File conversion with msconvert

-   Converting vendor specific **raw** to open **mzML** format

### 3. DIA analysis using OpenSwathWorkflow

-   Combining osw results using **PyProphet merge**

### 4. False Discovery Rate estimation using **PyProphet score**

-   Apply scores with **PyProphet peptide**
-   Apply scores with **PyProphet protein**
-   Exporting the results with **PyProphet export**

## Get data

Firstly, we create a new history in galaxy platform and we import the fasta and raw files as well as the sample annotation and the iRT Transition file (contains information about the transitions of the Indexed Retention Time (iRT) standard peptides) from [Zenodo](https://zenodo.org/record/4307762). These peptides are a set of synthetic peptides with well-defined and stable retention times across different liquid chromatography-mass spectrometry (LC-MS) systems.

-   Copy the link location
-   Click galaxy-upload **Upload Data** at the top of the tool panel
-   Select galaxy-wf-edit **Paste/Fetch Data**
-   Paste the link(s) into the text field
-   Press **Start**
-   **Close** the window

Once the files are imported, I rename the sample annotation file in ‘Sample_annotation’, the spectral library in ‘HEK_Ecoli_lib’, the iRT transition file in ‘iRTassays’ and the raw files in ‘Sample1.raw’, ‘Sample2.raw’, ‘Sample3.raw’, ‘Sample4.raw’, ‘Sample5.raw’, ‘Sample6.raw’, ‘Sample7.raw’ and ‘Sample8.raw’ get_data3a

-   Click on the galaxy-pencil **pencil icon** for the dataset to edit its attributes
-   In the central panel, change the **Name** field
-   Click the **Save** button get_data3b Then, we generate a collection for all .raw files (and name it DIA_data) by clicking on galaxy-selector **Select Items** at the top of the history pane get_data4 We check all the datasets and create advanced build list. get_data5 We choose flat list get_data6

Then we cleanup prefixes/suffixes from the names by double clicking on the file names to edit. For example, remove file extensions or common prefix/suffixes to cleanup the names. get_data7

## \# raw File conversion with **msconvert**

Converting vendor specific raw to open mzML format<https://training.galaxyproject.org/training-material/topics/proteomics/tutorials/DIA_Analysis_OSW/tutorial.html#hands-on-converting-vendor-specific-raw-to-open-mzml-format>

1.  **msconvert Convert and/or filter mass spectrometry files**
    -   param-collection *“Input unrefined MS data”*: `DIA_data`
    -   *“Do you agree to the vendor licenses?”*: `Yes`
    -   *“Output Type”*: `mzML`
    -   In *“Data Processing Filters”*:
        -   *“Apply peak picking?”*: `Yes`
        -   *“Demultiplex overlapping or MSX spectra”*: `Yes`
            -   *“Optimization”*: `Overlap only`
    -   In *“General Options”*:
        -   *“SIM as Spectra”*: `Yes`
        -   *“Ignore unknown instrument error”*: `Yes`
    -   In *“Output Encoding Settings”*:
        -   *“Intensity Encoding Precision”*: `64`
