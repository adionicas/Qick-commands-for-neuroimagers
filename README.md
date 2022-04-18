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
