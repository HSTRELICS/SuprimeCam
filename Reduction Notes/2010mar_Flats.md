I downloaded 1000 exposures of the W-J-V bands taken during the nights between:
2010-03-01..2010-4-28
from SMOKA.
/sandbox/subaru/rawdata/2010mar/flats/ V

Setup the flat folder architecture.

```
cd /sandbox/subaru/flats
mkdir 2010mar
cd 2010mar
mkdir V
```

# V-band

## Run SDFRED flat pipeline
### Link to the raw data
There are 1000 separate exposures for the skyflat.

```
cd V
ln -s ../../../rawdata/2010mar/flats/V/SUPA*.fits .
```
### rename the data files in each band folder
```
ls -1 SUPA*.fits > namechange.lis
namechange.csh namechange.lis
```
### perform subtraction of overscan and bias in each band folder
```
ls -1 H*.fits > ovserscansub.lis
overscansub.csh ovserscansub.lis
```

### Skyflat option:
```
ls -1 To_RH*.fits > mkflat.lis
mask_mkflat_HA.csh mkflat.lis skyflat_V 0.5 1.3
```
### Check that the flats look reasonable
e.g. with:

```
ds9 -mosaic wcs skyflat*.fits
```
### Clean up working files to save disk space
```
rm To_*
rm tmp*
rm ssbtmp*
rm mnah*
rm blank*
```


# The same as above but just the commands for easy running
```
cd /sandbox/subaru/flats
mkdir 2010mar
cd 2010mar
mkdir V
cd V
ln -s ../../../rawdata/2010mar/flats/V/SUPA*.fits .
ls -1 SUPA*.fits > namechange.lis
namechange.csh namechange.lis
ls -1 H*.fits > ovserscansub.lis
overscansub.csh ovserscansub.lis
ls -1 To_RH*.fits > mkflat.lis
mask_mkflat_HA.csh mkflat.lis skyflat_V 0.5 1.3
ds9 -mosaic wcs skyflat*.fits
```