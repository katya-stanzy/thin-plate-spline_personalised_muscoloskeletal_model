# Personalizing an OpenSim Model with TPS and 3DSlicer

**Files in this repository create a pipeline for personalisation of an OpenSim model.**

## Data Availability
- **Example dataset**: Full worked example in `worked_example/` folder
- **Template files**: OpenSim model templates in `example/templates/`
- **Test data**: Anonymized MRI and motion capture data available upon request

To get full information please refer to the publication.

The folder 'worked_example' contains an individual with full set of results. The folder 'example' contains everything to repeat 'worked_example'.

## Prerequisites

### Software & Skills
Users should have:
- Basic knowledge of OpenSim musculoskeletal modeling
- Familiarity with Jupyter notebooks and Python programming
- Understanding of MRI image formats (DICOM)
- Experience with 3D Slicer, particularly the Markups module
  - [3D Slicer Documentation](https://slicer.readthedocs.io/en/latest/)
  - [Markups Module Tutorial](https://www.youtube.com/watch?v=xrly9atx7xQ)

### Required Data
To use this pipeline, you need:
- A static trial with reflective skin markers placed on the participant
- An MRI scan of the participant

### Example Implementation
We provide a complete example using the Rajagopal et al. (2016) full-body model [DOI: 10.1109/TBME.2016.2586891], where we personalize the pelvis, femora, patellae, and tibiae segments.

<div align="center">
   <img src="flowchart.png">
</div>

1. Programs and environment:
   - (a) install an environment manager, such as miniconda "https://docs.conda.io/projects/conda/en/latest/user-guide/install/windows.html#install-win-silent"
   - (b) ensure environment.yml is in your project folder, from a commandline run 
            <pre>```cd <project_path>```</pre>
            <pre>```conda env create -f environment.yml```</pre>
   - (c) Install an older version of 3D Slicer: https://slicer-packages.kitware.com/#collection/5f4474d0e1d8c75dfc70547e/folder/5f4474d0e1d8c75dfc705482 . Installation instructions can be found on the official website: https://slicer.readthedocs.io/en/latest/user_guide/getting_started.html . 3D Slicer Jupyter extension is not available in the newest Slicer 3D version. Therefore, use the recommended older version (Slicer 5.6.2).
   - (d) Install Jupyter extension in 3D Slicer. This should be done within the software with the help of Exension Manager https://slicer.readthedocs.io/en/latest/user_guide/extensions.html. Once Jupyter extension was installed, close the current window of the software and re-start Slicer. Locate Jupyter in 'Developers Tools' from the drop-down "Modules:" menue, set a location for notebook directory and click 'Start Jupyter Server'. This will start Slicer Python kernel. Close 3D Slicer.
   - (e) Slicer Python kernel should now be detectable from your chosen Jupyter IDE. On launching the kernel, a managed version of 3D Slicer starts automatically. Visual Studio Code (https://code.visualstudio.com/), one of many one possible IDEs, was used by the authors of the publication.

2. Example directory structure:
<pre>
   ├── example
      ├── final_results
      ├── model_update
      │   ├── Geometry
      │   ├── logs_optimisation
      │   ├── 1_extract_static_C3D.ipynb
      │   ├── 2.0_use_mri_data.ipynb
      │   ├── 2.1_after_controlling_mri_data.ipynb
      │   ├── 3_scale_generic_model.ipynb
      │   ├── 4_update_generic_model_with_mri_data.ipynb
      │   ├── 5_optimize_muscles.ipynb
      │   ├── fibre_scale_script.py
      │   ├── momentArms_plot.py
      │   ├── optimization_grid.py
      │   ├── rotation_utils.py
      │   ├── simFunctions.py
      │   ├── stan_utils.py
      │   ├── tps_scripts.py
      │   └── wrap_scripts.py
      ├── motion_lab
      │   └── static
      │       └── Static03
      │           ├── Static03.c3d
      │           ├── df_steps.csv
      │           ├── task.trc
      │           └── task_grf.mot
      ├── mri
      │   ├── DICOM
      │   ├── control
      │   │   ├── LinearTransform.h5
      │   │   └── control.ipynb
      │   └── results
      │       ├── Bone_Landmarks_in_3DSlicer.pptx
      │       ├── create_template.ipynb
      │       ├── orientation_with_tps.ipynb
      │       └── tps_scripts.py
      └── templates
         ├── RajagopalModified_generic_copy.osim
         ├── RajagopalModified_generic_copy_with_bone_markers.osim
         ├── bone_markers_in_ground.csv
         ├── generic_skin_markers.xml
         ├── marker_placer.xml
         ├── markers_and_bone_markers_in_bodies.xml
         ├── point_order.csv
         └── scaling_setting.xml
</pre>
3. Preliminary: 
   - (a) Check that a static trial has been extracted from the C3D file (follow the notebook **"model_update/1_extract_static_c3D.ipynb"**). Please note the path and the name of this file. It will be required in following notebooks.
   - (b) Create a template with all landmarks in the ground frame. To do so, use the notebook **"mri/results/create_template.ipynb"**. This step is needed to be done only once provided that the template folder is copied to/used for all individuals.
   - (c) Create a Python environment using the supplied .yml file. This environment will be needed for those notebooks that do not depend on the 3DSlicer Jupyter kernel.

4. VS Code is able to detect 3DSlicer Jupyter kernel installation after it has been closed and opened again. The kernel, once loaded, will aitomatically open a managed 3DSlicer window. 

Open notebook **"mri/results/orientation_with_tps.ipynb"**.  Once 3DSlicer kernel is activated, please run 'pip install thin-plate-spline' to install this library in the kernel. Please note, that you can only close this window if you close the managing notebook.

The notebook will guide your actions step-by-step interactively. You can find instructions for using Markups module in 3D Slicer and landmark collection here: https://slicer.readthedocs.io/en/latest/user_guide/modules/markups.html. 3D Slicer project that you will have created needs to be saved via File -> Save Data -> "Save Scene and Unsaved Data" window. Please do not create a zip folder for the project, as its files will have to be accessible for Jupyter IDE.

5. Once all bone landmarks were collected for the individual, use the notebook **"model_update/2.0_use_mri_data.ipynb"** to create bone surfaces, mucle points and wrapping surfaces locations for the individual.

6. On completing (3) you need to check and correct muscle locations. To do so, use **"mri/control/control.ipynb"** notebook. Once open, the notebook needs to work with the 3DSlicer kernel. The kernel will aitomatically open a managed 3DSlicer window, where you load all bones and transform htem with the help of LinearTRansform.h5. Follow the instructions in the file.

7. On completing control.ipynb, you should proceed with **"model_update/2.1_after_controlling_mri_data.ipynb"**.

8. File **"model_update/3.1_scale_generic_model.ipynb"** scales the generic Rajagopal 2016 template to fit individuals static trial. Make sure you change personal details (height, weight etc) and the location of the static trial .trc file. Please note, this file includes an option to adjust muscle fibre scale using Modenese et al. (2016) pipeline. Please uncomment it if you want to run it.
9. File **"model_update/4_update_generic_model_with_mri_data.ipynb"** takes the generic scaled model with joint centers as markers from the step 3.1 and updates muscle paths, joint locations in parent, locations of wrapping surfaces, skin markers and radii of some of the wrapping surfaces. There is also an option to scale muscle fibres using Modenese et al, (2016) script, which us currently commented out. After each update the model is saved with a different name so one can compare results if needed. Please note that one needs to output an .xml file with skin markers after the first update (*'tps_transformed.osim'*): follow instructions in the notebook.
10. File **model_update/5_optimize_muscles.ipynb** helps to analyse muscle moment arms of the personalised model. The first part plots muscle moment arms. The second part optimizes a list of muscles that you think need to be more like moment arms in the generic scaled model. Please note that the optimisation script may fail to locate a better solution. 
