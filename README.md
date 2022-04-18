# Qick-commands-for-neuroimagers
This is a collection of AFNI and FSL commands for image manipulation that run in bash.


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
