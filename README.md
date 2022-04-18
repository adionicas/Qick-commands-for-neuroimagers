# Qick-commands-for-neuroimagers
This is a collection of AFNI and FSL commands for image manipulation that run in bash.


Put dicom data in BIDS (func)

```
old_name="WM14_005"
new_name="ACAP8005"
mkdir -p BIDS_fmri_ACAP8/sub-"$new_name"/func
dcm2niix \
-o BIDS_fmri_ACAP8/sub-"$new_name"/func \
-x n -b y \
-f sub-"$new_name"_task-rest_bold \
-z y \
$old_name/fMRI*PRE*/*
```

Check voxelsize with FSL [each dimension is: x, y, z, TR, scan length]

```
fslval $img pixdim1
fslval $img pixdim2
fslval $img pixdim3
fslval $img pixdim4
fslval $img dim4
```

Extract FD and dummy FD spikes

```
fsl_motion_outliers -i sub-${1}_func_img.nii.gz \
-o sub-${1}_outliers_dummy.txt \
-s sub-${1}_FD_RMS_vect.txt \
-p sub-${1}_plot \
--fdrms --thresh=0.2 -v

```

Binarize image (FSL)

```
fslmaths img_wei.nii.gz -bin img_bin.nii.gz
```

Probability threshold - for example for pvals randomise output or for segmentation (FSL)

```
fslmaths "$1"/anat/"$1"_space-MNIPediatricAsym_cohort-5_label-CSF_probseg.nii.gz \\
-thrp 99 "$1"/anat/"$1"_space-MNIPediatricAsym_cohort-5_label-CSF_probseg_thr.nii.gz
```

Erode a mask (FSL)

```
fslmaths "$1"/anat/"$1"_space-MNIPediatricAsym_cohort-5_label-CSF_probseg_thr-bin.nii.gz \
-kernel gauss 3 -ero "$1"/anat/"$1"_space-MNIPediatricAsym_cohort-5_label-CSF_probseg_ERO.nii.gz
```

Intrsect two images (FSL)

```
fslmaths MIST/"$parc"_MNIPediatricAsym_cohort-5_res-2.nii.gz \
-mul ../derivs_M3/fmriprep/sub-"$1"/func/sub-"$1"_task-rest_space-MNIPediatricAsym_cohort-5_res-2_desc-brain_mask.nii.gz \
MIST/intersected_ROIs/sub-"$1"_ses-M3_"$parc"_MNIPediatricAsym_cohort-5_res-2.nii.gz
```

Extract time-sries from parcellation (AFNI)

```
3dROIstats -mask MIST/intersected_ROIs/sub-"$1"_ses-B0_MIST_325_MNIPediatricAsym_cohort-5_res-2.nii.gz \
-roisel eligible_ROIs_MIST325.txt -quiet \
../denoising_B0/sub-"$1"/sub-"$1"_denoised_"$2".nii.gz> actual_TS/sub-"$1"_TS_MIST325_"$2".txt
```

Resample an image (for example parcellation to func size) in AFNI

```
3dresample -master derivatives/fmriprep/sub-"$sub"/ses-01/func/sub-"$sub"_ses-01_task-5000scenes_run-01_bold_GLM_6MO_FD_FD-dv.nii.gz \
-prefix derivatives/fmriprep/sub-"$sub"/ses-01/func/sub-"$sub"_v1_mask_GM_res.nii.gz \
-input derivatives/fmriprep/sub-"$sub"/ses-01/func/sub-"$sub"_v1_mask_GM.nii.gz
```

Obtain derivatives (AFNI)

```
1d_tool.py -infile denoising_B0/sub-"$1"/sub-"$1"_raw_regressors_36p.txt \
-derivative \
-write denoising_B0/sub-"$1"/sub-"$1"_raw_regressors_36p_deriv.txt
```

Remove linear and ^2 trends from .txt file vect (AFNI)

```
3dDetrend -DAFNI_1D_TRANOUT=YES \
-prefix - \
-polort 2 \
denoising_B0/sub-"$1"/sub-"$1"_final_regressors_36p.1D\' \
> denoising_B0/sub-"$1"/sub-"$1"_final_regressors_36p_detrended.txt
```

Segmentation example (ANTS)

```
antsAtroposN4.sh \
-d 3 \
-a ../sub-ACAP1003_desc-preproc_T1w.nii.gz \
-x ../sub-ACAP1003_desc-brain_mask.nii.gz \
-c 6 \
-w 0.25 \
-o test_tmp_output
```

Extract time-series from image (FSL)

```
fslmeants -i "$sub"/func/"$sub"_mo_deriv_spkreg.nii.gz \
-o V1_TS/"$sub"_V1_mo_deriv_spkreg.txt \
--label="juelich_prob_GM_Visual_cortex_V1_BA17_both_fsl_thr_bin2funcdim.nii.gz"
```

