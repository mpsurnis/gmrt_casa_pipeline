This repo contains the CASA-based imaging pipeline for uGMRT data. This is especially tuned to carry out imaging in subbands and estimate the flux density of the source at the phase centre.

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
   a. scaloops (line 75) which defaults to 5. That is usually enough. This is 4 phase only self cals plus one amplitude and phase self cal.
   b. mycell (line 78) which defaults to 0.5 arcsec. That is the value for band4. For band3, change it to 1.0 arcsec. This is the value of the cell size for re-gridding the visibilities.
   c. myimsize (line 79) which defaults to 9000 pixels. This is the final image size in pixels (square). This corresponds to a field size of about 0.7 deg at band4 and about 1.3 deg at band3.
   d. uvrascal (line 86) which defaults to >2klambda. That is the value for band4. For band3, change it to >1klambda. This is a parameter to exclude the central square antennae while self-calibrating.

8. Now, we can run the pipeline. Just make sure that TEST.FITS, vla-cals.list and my-pipeline-V15-uf_mps_subband_stable.py are in your current working directory. To run the pipeline, just type:

   nohup /Data/astrosoft/casa/casa-release-5.4.0-68.el7/bin/casa --nogui -c my-pipeline-V15-uf_mps_subband_stable.py >& nohup.log

9. If everything goes as planned, there should be <target name>selfcalimg0-5.fits and <target name>selfcalimgsub0-3.fits in the current directory. They are the final images. <target name>selfcalimg5.fits should be the final full-band image and all the sub0-3 fits images are the subband images generated from the last calibrated visibility file.

10. The flux densities with errors in mJy and frequencies in MHz can be found by doing:

    grep RES nohup.out
