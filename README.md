This repo contains the CASA-based imaging pipeline for uGMRT data. This is especially tuned to carry out imaging in subbands and estimate the flux density of the source at the phase centre.

This is based on the original pipeline obtained from Ruta and Ishwar which is hosted here: https://github.com/ruta-k/uGMRT-pipeline.git

There are some parameters that need to be set-up and/or fine tuned in order to carry out imaging with the data acquired in different bands of the uGMRT.

The subband_stable script is the latest working version and the mps_dev is the script under developement and testing.

Installation:

git pull https://github.com/mpsurnis/gmrt_casa_pipeline.git


Using the pipeline:

There are many steps in using the pipeline on the data. Following is an example of how to use it on tapti in NCRA

1. Run listscan on the lta file:

   /Data/astrosoft/bin/listscan <lta file>

2. Edit the log file to exclude scans that are offset positions of the flux cal and the non-target fields by putting an * before the scan name. Also put the FLAG file name in the place of the online flags in the log file.

3. Run gvfits on the log file

   /Data/astrosoft/bin/gvfits <log file>

4. Typically, this will produce a fits file by the name 'TEST.FITS'. We let it be like that. 

5. Grab the latest stable pipeline python file from the repo.

   cp /home/mpsurnis/gmrt_casa_pipeline/my-pipeline-V15-uf_mps_subband_stable.py .

6. Grab the VLA calibrator name list as well from the repo.

   cp /home/mpsurnis/gmrt_casa_pipeline/vla-cals.list .

7. Now, we need to change a few parameters for the pipeline to work properly. They are as follows:
   a. myminbeamfrac (line 53) which defaults to 0.3. This is good enough for most of the cases. Change it to 0.1 if there are less than 20 antennae in a subarray
   b. mynpix (line 54) which defaults to 20. This is again, usually good enough if there is a point source at the phase centre. Make it larger for subarray observations with a larger beam size
   c. nsub (line 56) which defaults to 4. This would be enough most of the times unless that source is stronger than a couple of hundred mJy :)
   d. scaloops (line 75) which defaults to 5. That is usually enough. This is 4 phase only self cals plus one amplitude and phase self cal.
   e. mycell (line 78) which defaults to 1.0 arcsec. That value is good for band4 and band3. For band4, change it to 0.5 arcsec if the need be. This is the value of the cell size for re-gridding the visibilities.
   f. myimsize (line 79) which defaults to 5000 pixels. This is the final image size in pixels (square). This corresponds to a field size of about 1.38 deg. Make usre to give a sensible value based on mycell
   g. uvrascal (line 86) which defaults to >2klambda. That is the value for band4. For band3, change it to >1klambda and for band5, change it to >5klambda. This is a parameter to exclude the central square antennae while self-calibrating. Leave this blank if there are ONLY central suqare antennae in the subarray.

8. Now, we can run the pipeline. Just make sure that TEST.FITS, vla-cals.list and my-pipeline-V15-uf_mps_subband_stable.py are in your current working directory. To run the pipeline, just type:

   nohup /Data/astrosoft/casa/casa-release-5.4.0-68.el7/bin/casa --nogui -c my-pipeline-V15-uf_mps_subband_stable.py >& nohup.log

9. If everything goes as planned, there should be <target name>selfcalimg0-5.fits and <target name>selfcalimgsub0-3.fits in the current directory. They are the final images. <target name>selfcalimg5.fits should be the final full-band image and all the sub0-3 fits images are the subband images generated from the last calibrated visibility file.

10. The flux densities with errors in mJy and frequencies in MHz can be found by doing:

    grep RES nohup.out
