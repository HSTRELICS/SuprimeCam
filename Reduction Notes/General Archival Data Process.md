Basic Proceedure

* Get the raw science exposure data from the SMOKA archive
* Get the flats for each chip from JVO
* Reduce the data with the correct



Perform an advanced search of the Subaru SMOKA archive:

http://smoka.nao.ac.jp/fssearch.jsp

In the Instruments/Filters section

Under Instruments just check Suprime-Cam

Under Observation Mode just check IMAG

Under Data Type just check OBJECT

Under Observation Band select FILTER

Under Maximum number of hits select 5000

Click the Search button

Mark all
Uncheck any frames that have FOCUSING in the DATA_TYPE column

Datarequest

There should be no need to Search for Calibration Frames

Are you OK? click OK

# Flats
## First Check JVO for available flats

Download the flats from JVO (http://jvo.nao.ac.jp/portal/subaru/spcam.do) for the associated band and date.

There is one file for each of the SuprimeCam CCD’s. Make sure to download flats for each chip using a consistent number of total exposures.

The downloaded files have names like: SUPR096015DC08F530.fits

I ran the following two commands to rename these files to something like: H050928object055_w67c1.fits which includes the ccd name.

ls -1 SUP*.fits > namechange.lis
namechange.csh namechange.lis

After that I replace the H050928object055 portion of the name with skyflat_i or skyflat_[band] where [band] is the corresponding band.

for filename in H050928object055*; do echo mv $filename ${filename//H050928object055/skyflat_i}; done | /bin/bash

## If JVO flats don't exist then will have to build from SMOKA
Perform an advanced search of the Subaru SMOKA archive:

http://smoka.nao.ac.jp/fssearch.jsp

In the `Observation Data` box get all the data within ~ +/-30 days of observation,
e.g. if you want to create a flat for observations taken on 2009-Sep-20 type:
2009-09-01..2009-10-31

In the `Exp Time` box type >70 to get rid of focus frames.

In the `Filter lists / Wavelength` box type the name of the desired filter,
e.g.:
W-S-I+

In the Instruments/Filters section

Under Instruments just check Suprime-Cam

Under Observation Mode just check IMAG

Under Data Type just check OBJECT

Under Observation Band select FILTER

Under Maximum number of hits select 1000, since this is the most you can
download at one time.

Click the Search button

Mark all
Uncheck any frames that have FOCUSING in the DATA_TYPE column

Datarequest

There should be no need to Search for Calibration Frames

Are you OK? click OK
