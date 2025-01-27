# E-field Dosing
## Dependencies and where to download them
- **SimNIBS** and any additional dependencies (depending on version): https://simnibs.github.io/simnibs/build/html/installation/simnibs_installer.html
  - The **SimNIBS example dataset** is found at https://simnibs.github.io/simnibs/build/html/dataset.html
- **FreeSurfer**: https://surfer.nmr.mgh.harvard.edu/fswiki/DownloadAndInstall
  - For Windows users, it is possible to download and use FreeSurfer through the Linux subsystem by following the instructions here: https://surfer.nmr.mgh.harvard.edu/fswiki/FS7_wsl
## Checking the FreeSurfer integration with SimNIBS 4
1. In the Linux subsystem / Terminal, navigate to the folder where the NifTI files are located in the SimNIBS example dataset
2. In the Linux subsystem / Terminal, run `recon-all -subjid ernie -all -i org/ernie_T1.nii.gz -T2 org/ernie_T2.nii.gz`
    1. This should generate a `fs_ernie` folder in the `SUBJECTS_DIR` you specified during the FreeSurfer Installation
    2. If you get a permission denied error, add `export SUBJECTS_DIR=<path to acceessable folder>` to the same file you added `source $FREESURFER_HOME/SetUpFreeSurfer.sh` while setting up freesurfer
3. In the Linux subsystem / Terminal, run `charm ernie org/ernie_T1.nii.gz org/ernie_T2.nii.gz --fs-dir <path to fs_ernie folder>`
## Checking the TAP installation
1. In MATLAB, Open “run_subj.m”
3. In MATLAB, change the filepaths between `%%%%% CHANGE TAP PARAMETER HERE: Begin %%%%%` and `%%%%% CHANGE TAP PARAMETER HERE: End %%%%%` as needed
    1. Set `Scale_Efield_to_Value` to the desired E-field value in V/m (can use 100 to test)
    2. If not running on a Linux cluster, comment out the line starting with `setenv`
4. Change `subjects/Ernie_charm` or the subjects subfolder corresponding to the appropriate pipeline to `subjects/ernie` and copy the `ernie` folder from the example dataset to `subjects/ernie`
5. Copy `M1_mask_TMS_target.nii.gz` from `TAP/subjects/Ernie_headreco` into “subjects/ernie”
6. In MATLAB, run `run_subj('ernie', 'subjects', 'M1', 'M1_mask_TMS_target.nii.gz', 1)`
    1. This should generate a `ernie_M1` folder, a `ernie_M1_scirun` folder, and at least one `M1_HTX.X` folder
## Quality control
1. For the SimNIBS head mesh, see https://simnibs.github.io/simnibs/build/html/tutorial/advanced/fix_affine_registration.html for quality control
2. Open `subjects/ernie/M1_HT0.0/ernie_TMS_optimize_MagVenture_Cool-B65.msh`
    1. Check that the target is at the expected location
    2. Check that the coil is oriented such that the primary E-field induced by the coil points into the target gyrus
        - Many coils have arrows on them indicating the current direction
        - For *monophasic* pulses, the primary E-field points in the *opposite* direction as the current
        - For *biphasic* pulses, the primary E-field points in the *same* direction as the current

# TAP: TMS_Targeting_And_Analysis_Pipeline
Custom MATLAB code to determine an optimal scalp placement of a TMS coil on the computational model of the subject's head. (1) Optimal coil placement is chosen based on maximizing the TMS-induced electric field in a brain region of interest which is derived from individual fMRI peak activity and computed prospectively for neuronavigation-supported TMS experiments. (2) The code's functionality allows also for accuracy assessment of the coil placement recorded during and analyzed after a TMS session.  
  
Dependencies:  
- SimNIBS 3.2.X  
- 'Tools for NIfTI and ANALYZE image' for loading structural MRI data sets (nii files) version 1.27.0.0 (from MATHWORKS MATLAB Central)  
  
For SimNIBS version < 3.2.5 users, the option to control the smoothness of the scalp surface (scalp_normals_smoothing_steps) to allow changing the scalp tangential plane from MATLAB is not available. However, there is a simple patch you can do yourself and described here: https://github.com/simnibs/simnibs/issues/42
and change smooth=1 to smooth=20 for example.
