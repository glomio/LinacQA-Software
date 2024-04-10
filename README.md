
<h1>LinacQA Software</h1>

## Table of contents:
1. [Introduction](#introduction)<br>
2. [Installation and settings](#installation)<br>
3. [Citation and distribution](#citationanddistribution)<br>
4. [Software Tabs](#softwaretabs):<br>

    4.1 [Field Analysis](#fieldanalysis-1)<br>
    4.2 [Isocenter](#isocenter-2)<br>
    4.3 [EPID Sag](#epidsag-3)<br>
    4.4 [MLC](#MLC-4)<br>
    - 4.4.1 [Leaf Position Accuracy](#leafpositionaccuracy-41)<br>
    - 4.4.2 [Leaf Position Repeatibility](#leafpositionrepeat-42)<br>
    - 4.4.3 [Abuttment Fields Junction](#abuttmentfieldsjuction-43)<br>
    - 4.4.4 [Leaf Travel Speed](#leaftravelspeed-44)<br>
    
    4.5 [Dynamic Delivery](#dynamicdelivery-5)<br>
    4.6 [LINAC Overview](#linacoverview-6)<br>
    4.7 [Elekta Movie to DICOM conversion](#elektamovietodicom-7)<br>
5. [References](#references)<br>
6. [Contacts](#contacts)<br>

# <a id="introduction"> 1.Introduction </a>

LinacQA is a software for evaluating several AAPM reports <sup>(1)(2)(3)</sup> parameters from EPID images, based on the free python package Pylinac <sup>(4)(5)</sup>. The software has a user friendly GUI and can be run from Windows computers, without the need of any installation. Acceptance criteria and machine specifications can be set in configuration files. Results are automatically stored in excel files, to be easily accessible outside the software.<br>
<br>
The program performs different analysis:<br>
- The first tab evaluates field size, flatness and symmetry (consistency check for test 2.D.1.14<sup>(2)</sup>);<br>
- The second tab analyses Winston-Lutz (WL) images to assess gantry, collimator and couch isocenter sizes and the coincidence between mechanical/radiation or CBCT/radiation isocenters (tests 2.D.2<sup>(2)</sup>, in particular 2.D.2.1<sup>(2)</sup>, 2.D.2.2<sup>(2)</sup>, 2.D.2.3<sup>(2)</sup> and 2.D.2.5<sup>(2)</sup>. It can be used for 2.F.3.3<sup>(2)</sup> as well; or tests M11<sup>(3)</sup> and MLC4<sup>(3)</sup>;<br>
- The third tab calculates EPID sag;<br>
- The fourth tab assesses MLCs performances: leaf position accuracy (tests 2.F.2.1<sup>(2)</sup>, 2.F.2.2<sup>(2)</sup>, 2.F.2.4<sup>(2)</sup> or test MLC2<sup>(3)</sup>) and repeatability (2.F.3.2<sup>(2)</sup>), abutting fields junctions (2.F.1.1<sup>(2)</sup>), leaf travel speed (2.F.2.3<sup>(2)</sup>);<br>
- The fifth tab can perform Dynamic Delivery control (DDC) tests D5<sup>(3)</sup>. To evaluate DCC we used an IBA Matrixx detector.<br>
- LINAC performances over time are shown in the sixth tab.<br>
- The last tab converts Elekta Movie format to a DICOM series to be able to evaluate Leaf Travel Speed;<br>

# <a id="installation"> 2.Installation and settings </a>

The LinacQA.exe file can be run on any Windows computer without the need of installation.<br>
<br>
The following files must be in the same folder as the executable:<br>
- settings_file.csv<br>
- acceptance_criteria.csv<br>
- fields_delivery_order_junction.csv<br>
- fields_delivery_order_MLC.csv<br>
- fields_delivery_order_speed.csv<br>
- fields_delivery_order_WL.csv<br>

The images must be in the **images** folder where the executable is:<br>

- AbuttmentFieldsJunction.png<br>
- DDC.png<br>
- FieldAnalysis.png<br>
- IEC61217.png<br>
- LeafPositionAccuracy.png<br>
- LeafTravelSpeed.png<br>

The LinacQA.py file runs on Python version 3.8.13.<br>
All packages required are listed in:<br>

    requirements_linacQA.txt
    
The project is stored in:<br>

    https://github.com/glomio/LinacQA-Software
***
The **settings_file.csv** file defines the machine specific settings needed for the analysis, together with the default parameters values.<br>
        **save_file_name** defines the excel file where storing the results. An excel file for each machine will be automatically created by running the first analysis. The complete filename for each machine is save_file_name underscore machine_name. The machine name is read from the analyzed DICOM file: DICOM TAG (0008,1010) and compared to the **machine_list**. Make sure the Station Name DICOM tag includes the machine name as specified in machine_list<br>
        Results are automatically saved in the above mentioned file each time an analysis is run except for the Dynamic Delivery tab, where the user can select or deselect the automatic saving.<br>
        **energy_options** is the list of machines supported energies.<br>
        **field_size_options** is the list of possible open fields size analysis in the Field Analysis and Dynamic Delivery tabs.<br>
        **MLC_arrangement** describes the LINAC MLC details, specifying the number of leaves and leaves width. It reads tuples of values.<br>
        **MLC_nominal_speed** describes the vendor defined nominal leaves speed.<br>
        **DDC_analysis_list** describes the names of dynamic delivery control tests run on the DDC tab. This names are used for correctly saving results.<br>
        **machineSAD** defines the SAD, while **imageSID** defines the EPID SID. **imagepixelspacing** defines the Image Pixel Spacing DICOM tag of the Elekta movie format. These last 3 settings are used for correctly converting Elekta Movie format into a DICOM series.<br>
        Defalut values appearing the the GUI are as well set in this file (see specific tabs).<br>
<br>
***
The **acceptance_criterias.csv** file defines the acceptance criterias for all LinacQA tests.<br>
**att_lev** and **act_lev** are the attention and action levels respectively. They should be set according to the acceptance criterias defined by the hospital Medical Physics team. The values entered in the uploaded example shouldn't be taken as a reference, every center should define their specific acceptance criterias.<br>
***
**fields_deliver_order** files describes the beam delivery order for a specific test. It is needed to correctly rename DICOM images before analysis.They must be adjusted accordingly to the actual beams delivered. Each file is further described in the the specific test section of this document.<br>

# <a id="citationanddistribution"> 3. Citation and distribution </a>

G. Miori, M. Chieregato, C. Rancan, M. Galelli. PD-01.44 - DEVELOPMENT AND VALIDATION OF AN EPID BASED SOFTWARE FOR LINAC QA BUILT ON A FREE PYTHON PACKAGE,Physica Medica, Volume 115, Supplement 1,2023,103018,<br>
https://doi.org/10.1016/j.ejmp.2023.103018.
<br>
# <a id="softwaretabs"> 4. Software Tabs </a>

## <a id="fieldanalysis-1"> 4.1 Field Analysis </a>

<img src='./images/field_size_image.png' align='center' width = '500'>

The field analysis tab evaluate Field size, Flatness and Symmetry of open fields. Flatness and Symmetry has the Elekta definition.<br>
This tab can be used to perform a quick and frequent consistency check of AAPM TG198 test 2.D.1.1.<br>
In the **acceptance_criterias** file, **field_size_acc**, **fla_acc** and **sym_acc** parameters must be defined as acceptance criterias for field size accuracy, flatness and symmetry respectively.<br>
In **Open file** select one DICOM image to analyse.<br>
**Invert image** should be used in case the algorithm has difficulties in identifying the field (typically for large field size). The software pops up a warning in case this checkbox should be selected/deselected<br>
Select **FFF** for FFF images (Flatness is not computed).<br>
Select energy and image nominal field size in the scroll down menus to correctly save the results.<br>
**Image info** displays:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>
- Series Description as in the DICOM TAG (0008,103E)<br>

Press **Analyze** to run the test.<br>
Results are automatically stored in the correct linac excel file in the **Field_Analysis** spreadsheet. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="isocenter-2"> 4.2 Isocenter </a>

<img src='./images/WL_image.png' align='center' width = '500'>

Isocenter tab runs a Winston-Lutz (WL) test on acquired DICOM images.<br>
This tab can be used to evaluate AAPM TG198 tests 2.D.2, in particular 2.D.2.1, 2.D.2.2, 2.D.2.3 and 2.D.2.5. It can be used for 2.F.3.3 test by selecting the appropriate delivery beams.<br>
In the **acceptance_criterias** file, **G3Diso_acc** parameter must be defined as the acceptance criteria for Gantry 3D isocenter diameter, while  **iso2D_acc** as the acceptance criteria for all other results.<br>
In **Open Folder** select the folder with WL acquired DICOM images.
The following information are printed:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>

By clicking **Rename Files** the files in the selected folder are renamed based on the **fields_deliver_order_WL** list. **fields_deliver_order_WL** is the list of beam delivered with specific gantry, collimator and couch angles used, as in the example. The name syntax is mandatory for the software to run the test with **Use filenames**.<br>
Select the **Analysis options** you want to run and choose the energy in the scroll down menu to correctly save the results.<br>
Press **Analyze** to run the test.<br>
Summary results displayed in the GUI are automatically stored in the correct linac excel file in the **ISO_Analysis** spreadsheet, while deviations for each image are saved in the **ISO_dev_GANTRY**, **ISO_dev_COLLIMATOR** or **ISO_dev_COUCH** spreadsheet for each energy. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="epidsag-3"> 4.3 EPID Sag </a>

<img src='./images/epid_sag_image.png' align='center' width = '500'>

Isocenter tab runs a Winston-Lutz (WL) test on acquired DICOM images showing results for EPID sag in X and Y as a function of gantry angle. The test calculates X and Y EPID sag and X and Y EPID sag normalized to the gantry 0 image. <br>
No acceptance criterias are defined for the EPID sag analysis.<br>
In **Open Folder** select the folder with WL acquired DICOM images.
The following information are printed:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>

By clicking **Rename Files** the files in the selected folder are renamed based on the **fields_deliver_order_WL** list. **fields_deliver_order_WL** is the list of beam delivered with specific gantry, collimator and couch angles used, as in the example. The name syntax is mandatory for the software to run the test with **Use filenames**.<br>
Press **Analyze** to run the test.<br>
Results are saved in the correct linac excel file in the **EPID_sag** spreadsheet. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="MLC-4"> 4.4 MLC </a>

The MLC tab runs several AAPM TG 142/198 tests to evaluate MLCs performances.<br> In particular, we refer to AAPM TG198 2.F tests on MLCs.<br>

## <a id="leafpositionaccuracy-41"> 4.4.1 Leaf Position Accuracy </a>

<img src='./images/MLC_image.png' align='center' width = '500'>

Leaf Position Accuracy test analyses both relative and absolute MLC position errors;<br>
For the MLCs analysis, we modified the Pylinac algorithm to calculate absolute leaves positions (as recommended by TG-142) and single leaf error analysis. Absolute leaves positions are calculated from the radiation field center, identified by the use of 4 additional small square fields images at different collimator positions. Relative leaves positions to the median leaves position are also performed. 
This tab can be used to evaluate AAPM TG198 2.F.2.1, 2.F.2.2, 2.F.2.4 tests on MLCs or test MLC2<sup>(3)</sup>.<br>
<br>
In the **acceptance_criterias** file, MLC_rel_pos_perc_failed, MLC_rel_pos_max_error refers to relative position errors acceptance criterias, while MLC_abs_pos_perc_failed and MLC_abs_pos_max_error refers to absolute position errors. MLC_abs_banks_offset is the acceptance criteria for Y2 Offset and Y1 Offset in the GUI, while MLC_mean_picket_width_acc is the acceptance criteria for Measured Picket Width. MLC_skew is the acceptance criteria for MLC apparent skew in the GUI.<br>
In **Open Folder** select the folder with MLC acquired DICOM images and the 4 additional small square fields images, if absolute error analysis is done. It is important to start delivering the MLC beams being sure gantry and collimator angle = 0.00 degrees. Deliver the 4 small square fields images with different collimator positions at the end of the test.<br>
The following information are printed:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>

By clicking **Rename Files** the files in the selected folder are renamed based on the **fields_deliver_order_MLC** list. **fields_deliver_order_MLC** is the list of beam delivered, at least one file including the word MLC must be saved for MLC analsysis. Absolute error analysis requires the 4 additional square fields images named *isoC_*, where *_* is the collimator angle.The name syntax is mandatory to run the test.<br>
Enter the correct *Nominal gap* and *Gantry angle* used for the measurement if different from the default ones. The default *Nominal gap* value in the GUI is set on the *default_value_nominal_gap* in the *settings_file*.<br>
Select the analysis and display options you want to run and choose the energy in the scroll down menu to correctly save the results.<br>
Press **Analyze** to run the test.<br>
Summary results displayed in the GUI are automatically stored in the correct linac excel file in the **MLC_Pos_Acc** spreadsheet, while deviations for each MLC leaf are saved in the **MLC_Pos_errors** spreadsheet for each energy. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="leafpositionrepeat-42"> 4.4.2 Leaf Position Repeatibility </a>

Leaf Position Repeatibility tab can be used to evaluate AAPM TG198 2.F.3.2 test.<br>
MLC images should be taken by positioning/moving/repositioning the MLCs acquisition of the previous test. A single beam delivery can be used for both Leaf Position Accuracy and Leaf Position Repeatibility.<br>
Relative leaves positions is calculated as in the previous tab for each MLC image, and leaf by leaf difference within images is performed.<br>
In the **acceptance_criterias** file, MLC_repeat_err refers to different images position errors acceptance criterias, while MLC_repeat_perc_failed is the percentage of leaves that can fail. <br>
In **Open Folder** select the folder with MLC acquired DICOM images. At least 2 files including the word MLC must be saved in the selected folder.<br>
The following information are printed:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>

Enter the correct *Gantry angle* used for the measurements if different from the default one, and choose the energy in the scroll down menu to correctly save the results.<br>
Press **Analyze** to run the test.<br>
Summary results displayed in the GUI are automatically stored in the correct linac excel file in the **MLC_Pos_Repeat** spreadsheet, while deviations for each MLC leaf are saved in the **MLC_Rep_errors** spreadsheet for each energy. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="abuttmentfieldsjuction-43"> 4.4.3 Abuttment Fields Junction </a>

<img src='./images/IMRT_image.png' align='center' width = '500'>

Abuttment Fields Junction tab can be used to evaluate AAPM TG198 2.F.1.1 test.<br>
It analyses field junctions and evaluates the percentage dose difference at junctions compared to the homogenous surrounding dose. It gives information on leaf tips match.<br>
Different MLC images should be taken in such a way that the MLC tips of one bank match the position of the MLC in the previous image. One image per MLC position should be acquired and ideally the images sum should be a flat profile.<br>
A background image can be used to normalize the picket and fence pattern.<br>
Field matching images are sum and the result image is analysed.<br>
In the **acceptance_criterias** file, MLC_abutt_junction_perc refers to the percentage dose difference acceptance criteria at the junction, in our test setting we saw that a 9% difference corresponds to ~ 0.5 mm displacement at the junction.<br>
In **Open Folder** select the folder with junctionimages.<br>
The following information are printed:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>

By clicking **Rename Files** the files in the selected folder are renamed based on the **fields_deliver_order_junction** list. **fields_deliver_order_junction** is the list of beam delivered. The field junction files name must include the word "junction" and an increasing number, if using the background normalization, a file including the word "background" is needed.<br>
Enter the correct *Gantry angle* used for the measurements if different from the default one, and choose the energy in the scroll down menu to correctly save the results. If using background normalization select the "Subtract background image" checkbox. <br>
Press **Analyze** to run the test.<br>
Summary results displayed in the GUI are automatically stored in the correct linac excel file in the **MLC_Abutt_fields_junctions** spreadsheet. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="leaftravelspeed-44"> 4.4.4 Leaf Travel Speed </a>

<img src='./images/speed_image.png' align='center' width = '500'>

Leaf Travel Speed tab can be used to evaluate AAPM TG198 2.F.2.3 test.<br>
Leaf travel speed images should be sequential DICOM images acquired in EPID movie mode. For Elekta movie format, the files must be first converted by using the *ElektaMovieToDICOM* tab. After the conversion, the analysis can be done by considering the exact image timing stored in the Elekta XML file or by entering a Frame/s value. The default *Frame/s* value in the GUI is set on the *default_value_framexs* in the settings_file.<br>

In the **acceptance_criterias** file, **MLC_speed** defines the accepted deviation from the nominal MLC speed value.<br>
In **Select 2 files** select 2 DICOM images from converted movie EPID files. Two consecutives or non-consecutives frames can be analysed if using the Elekta XML file, otherwise only 2 consecutive frames can be analysed.<br>
The following information are printed:<br>
- Station Name as in the DICOM TAG (0008,1010) - compared to the **machine_list**.<br>
- Date as in the DICOM TAG (0008,0020)<br>

Enter the correct *Gantry angle* used for the measurements if different from the default one, and choose the energy in the scroll down menu to correctly save the results.<br>
Choose if using Elekta Movie XML file or Frame/s. Enter the correct *Frame/s* if different from the default one.<br>
Press **Analyze** to run the test.<br>
Summary results displayed in the GUI are automatically stored in the correct linac excel file in the **MLC_Leaf_Speed** spreadsheet, while leaf travel speed for each MLC are saved in the **MLC_Leaf_Speed_all_leaves** spreadsheet. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="dynamicdelivery-5"> 4.5 Dynamic Delivery </a>

<img src='./images/DDC_image.png' align='center' width = '500'>

LINAC QA recommendations were updated in 2023 to emphasize the importance of testing DDC components, tests D5<sup>(3)</sup>. Dynamic Delivery tab can be used to test DDC parameters by designing different dynamic fields and varying each parameter individually. In particular, you can compare profiles of the same dynamic field by varying leaf speed only, dose-rate only, gantry speed only or a combination of them. Beams can be obtained by increasing MUs (MU linearity must be tested separately).<br>
Dynamic Delivery tab works on IBA MatriXX opg files.<br>
<br>
In the **acceptance_criterias** file, **DDC** defines the maximum accepted variation value between profiles measured at different dynamic parameters.<br>
In **Select Files** , select 2 or more .opg files. The profiles are normalized to the central axes value of the first file in alphabetical order. You should rename the files so that the first one in the folder is the reference one.<br>
The following information are printed:<br>
- Date as in the .opg Date/Time row<br>

Choose machine name, energy, nominal field size and the dynamic delivery control test name in the scroll down menus to correctly save the results. Enter the correct *Gantry angle* used for the measurements if different from the default one.<br>
Select the **Save Results** checkbox if you want the results to be saved. Choose if considering the Full Profile or a Percentage of the nominal field size. The default *Percentage Field Size* value in the GUI is set on the *default_value_exclude_penumbra* in the *settings_file*.<br>

Press **Analyze** to run the test.<br>
Summary results displayed in the GUI are automatically stored in the correct linac excel file in the **Dynamic_Delivery_Control** spreadsheet. Analysis figures are not automatically stored and must be manually saved by the **Save the figure** button, if wanted.<br>

## <a id="linacoverview-6"> 4.6 LINAC Overview </a>

<img src='./images/linac_trend_image.png' align='center' width = '500'>

LINAC overview tab shows LINAC performances over time. It retrieves information from the excel files where analysed data are automatically stored. <br>
Select machine name, energy and analysis type, then click **Select**.<br>
Select dates interval or any date checkbox, then click **Show**.<br>
A pdf file showing LINAC performaces over time for a specific analysis is printed. The file is automatically saved as *trend_plot.pdf* file.<br>

## <a id="elektamovietodicom-7"> 4.7 Elekta Movie to DICOM conversion </a>

<img src='./images/movie_conversion_image.png' align='center' width = '500'>

The tab converts Elekta Movie format files to a DICOM Images series. Elekta Movie format files are exported from iView database.<br>
In **Open Folder** select the folder where Elekta Movie EPID files are stores together with the Elekta *_Frame* XML file.<br>
Select machine name and measurement date, this information is added in the converted DICOM files.<br>
Click **Convert** to convert files. If the conversion is successful, a green box **Converted** appears.
If **Plot Converted Images** is selected, each DICOM converted frame is shown.<br>

# <a id="references"> 5. References </a>

(1) (Klein et al., E. E., (2009). Report No. 142 - Task Group 142 report: Quality assurance of medical accelerators. Medical Physics)<br>
(2) (Hanley et al.,J., (2021). Report No. 198 - AAPM Task Group 198 Report: An implementation guide for TG 142 quality assurance of medical accelerators. Medical Physics)<br>
(3) (Krauss et al.,R. F., (2023). AAPM Medical Physics Practice Guideline 8.b: Linear accelerator performance tests. Journal of Applied Clinical Medical Physics)<br>
(4) github.com/jrkerns/pylinac<br>
(5) (Kerns, J. R., (2023). Pylinac: Image analysis for routine quality assurance in radiotherapy. Journal of Open Source Software, 8(92), 6001)

# <a id="contacts"> 6. Contacts </a>
gloria.miori@gmail.com

=======
