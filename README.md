# Plethysmography Respiratory Data Analysis

This R code package, implemented in the R Markdown file "QB_Breathing_Tag.Rmd," is designed to process plethysmography respiratory data from an Excel input file. It categorizes breathing behaviors into Quiet Breathing (QB), Sighs, Sniffs, Apneas, Post-sigh Apneas, and Hypopneas, while also quantifying irregularities through variability metrics. The package generates detailed summaries and visualizations of the analyzed data.

**Prerequisites**

To run this code, ensure you have an R environment with the following packages installed:

readxl: For reading Excel files
dplyr: For data manipulation
gridExtra: For creating graphical tables
grid: For graphical object handling
lubridate: For date/time operations (if applicable)
openxlsx: For writing styled Excel files
ggplot2: For saving graphical outputs
stringr: For string manipulation
purrr: For functional programming tools

You can install these packages using the provided install_if_missing function in the code, which automatically installs and loads any missing packages.


**Input Data**

The input must be an Excel file containing the following required columns:

Te (msec): Expiratory time in milliseconds
Ti (msec): Inspiratory time in milliseconds
MV: Minute ventilation
TV: Tidal volume
PIF: Peak inspiratory flow
PEF: Peak expiratory flow
RT (msec): Relaxation time in milliseconds
P (msec): Pause time in milliseconds
f (bpm): Frequency in breaths per minute
Penh: Enhanced pause
Protocol Type: Indicates the protocol type (e.g., "Baseline" for analysis)

The code validates the presence of these columns and stops execution if any are missing.


**Usage**
_Run the R Markdown File:_
Open "QB_Breathing_Tag.Rmd" in an R environment (e.g., RStudio) and execute it by knitting the document or running all chunks sequentially.

_Select Input File:_
When prompted, use the file chooser to select your Excel file containing the respiratory data.

_Enter Rat Light/Protocol Type:_
Provide the "Rat Light/Protocol Type" (e.g., "LD" for light-dark cycle or "pre/post_TBI" for injury type) when prompted. This input adjusts the output file names if not "LD".


**Processing Steps:**
The code processes the data through the following steps:

_Data Loading and Validation:_
Loads the Excel file and checks for required columns.

_Helper Functions:_
resolve_multiclass: 
Resolves multiple breathing tags (e.g., "0,3.1") into a single numeric class based on priority: Post-sigh Apnea (3.1) > Hypopnea (4) > Apnea (3) > Sigh (1) > Sniff (2) > QB (0).

recalc_qb_tags_and_averages: 
Recalculates QB tags and computes averages for metrics (e.g., Ti, Te, TV) within each QB session, plus dataset-wide QB averages.

recalculate_qb_variability: 
Calculates QB Variability (% change in Ttotal from the previous breath) and QB_Var_Dev (absolute difference in QB Variability from the previous breath).

_QB Tagging and Iterative Refinement:_
Identifies QB sessions in "Baseline" data where frequency ≤ 250 bpm and Ttotal variability < 40%.
Iteratively refines QB sessions (up to 25 iterations) by removing breaths with QB_Var_Dev > 40 or zero Ttotal/TV values, recalculating variability and averages each time.

_Breathing Classification:_
Classifies breaths using thresholds based on dataset-wide QB averages:

QB (0): Assigned to breaths in refined QB sessions.
Sigh (1): TV ≥ 2.5 × average QB TV.
Sniff (2): Frequency ≥ 2 × average QB frequency.
Apnea (3): Te ≥ 2 × average QB Te.
Post-sigh Apnea (3.1): Te ≥ 2 × average QB Te and TV ≥ 2.5 × average QB TV.
Hypopnea (4): TV ≤ 0.7 × average QB TV and Te ≥ 2 × average QB Te.

_QB Session Adjustment:_
Splits QB sessions if gaps exceed 5 breaths.
Removes sessions with fewer than 10 breaths and renumbers remaining sessions.

_Output Generation:_
Computes averages for each breathing class.
Generates a multi-sheet Excel workbook, a highlighted Excel file, a grob table, and a classification summary.


**Outputs**
The code produces the following outputs in subfolders within the working directory:

_Processed Data Excel File:_
Location: "Circadian QB Filter" folder
File Name: <Rat_Light_Type>_<input_file_name>_QBfilter.xlsx (e.g., "LD_data_QBfilter.xlsx")
Content: Original data with added columns for QB Tag, QB Variability, QB_Var_Dev, averages, and Breathing Class.
Highlighting:
QB (0): Light yellow (#FFFF99)
Sigh (1): Light blue (#ADD8E6)
Sniff (2): Light red/pink (#FFB6C1)
Apnea (3): Light green (#90EE90)
Post-sigh Apnea (3.1): Spring green (#00FF7F)
Hypopnea (4): Light purple/violet (#E6E6FA)

_Average QB Values Table:_
Location: "Circadian QB Avg Tables" folder
File Name: <Rat_Light_Type>_<input_file_name>_QB_AVG.png
Content: A PNG image of a table showing dataset-wide average values for QB breaths (e.g., Average Ti, Te, TV).

_Breathing Analysis Summary Excel File:_
Location: "Classification Count" folder
File Name: <Rat_Light_Type>_<input_file_name>_BreathingAnalysis.xlsx
Sheets:
Total Counts: Counts of Sighs, QB Sessions, Apneas (Total, General, Post-sigh), Hypopneas, plus QB averages.
Sighs: List of sigh events with indices and average metric values.
Quiet Breathing: Summary of QB sessions with start/end indices and average metrics.
Apneas: List of apnea events with indices, types (General/Post-Sigh), and averages.
Hypopneas: List of hypopnea events with indices and average metric values.


**Notes**
Data Assumptions: The input Excel file must contain all required columns with valid data. Missing or malformed data may cause errors.
Iterative Refinement: The process may require multiple iterations to stabilize QB sessions, capped at 25 iterations.
Thresholds: Classification thresholds depend on QB averages, so accurate QB session identification is critical.
Rat Light Type: If not "LD", this prefix is added to output file names for easy identification.
Error Handling: The code stops if required columns are missing or if QB averages cannot be computed.


This package provides a robust tool for respiratory data analysis, offering detailed insights into breathing patterns and irregularities. For further assistance, review the inline comments in "QB_Breathing_Tag.Rmd" or contact the package author.


Support: For assistance, email [el.kaye@ufl.edu].

License and Citation





License: Distributed under the MIT License.



Citation: If used in research, please cite: "Quiet Breathing Respiratory Analysis, [Ethan Leonard Kaye], 2024."
