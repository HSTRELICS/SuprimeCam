### Setup the folder architecture and cluster specific inputs
```
cd /sandbox/subaru/
mkdir a1763
cd a1763
mkdir i V V2009 V2010
```


Some of the following commands are duplicated since it is assumed they will be
run in parallel.

# i-band (Deep band)
This must be run before the other bands.
```
basename=a1763
deepband=i
fieldcenter=203.821,41.0
cd ../i
band=i
EXPTIME=240
zp=0
ln -s ../../rawdata/Abell1763/SUPA0108319?.fits .
ln -s ../../rawdata/Abell1763/SUPA0108320?.fits .
ln -s ../../rawdata/Abell1763/SUPA010850[2-8]?.fits .
```

# V-band (2009 data)
```
basename=a1763
deepband=i
fieldcenter=203.821,41.0
cd ../V2009
band=V
EXPTIME=240
zp=0
ln -s ../../rawdata/Abell1763/SUPA010832[2-5]?.fits .
```

# V-band (2010 data)
```
basename=a1763
deepband=i
fieldcenter=203.821,41.0
cd ../V2010
band=V
EXPTIME=240
zp=0
ln -s ../../rawdata/Abell1763/SUPA011993[5-9]?.fits .
ln -s ../../rawdata/Abell1763/SUPA011994[0-1]?.fits .
```

# Deep Band Bulk Commands
To be run after the respective setup commands above.
__Be sure to change the flat directory!__
```
ls -1 SUPA*.fits > namechange.lis
namechange.csh namechange.lis
ls -1 H*.fits > ovserscansub.lis
overscansub.csh ovserscansub.lis
ln -s ../../flats/2000aug/$band'/skyflat'*.fits .
ls -1 skyflat*.fits > ffield_mf.lis
ls -1 To_RH*.fits > ffield_im.lis
ffield.csh ffield_mf.lis ffield_im.lis
ls -1 fTo_RH*.fits > distcorr.lis
distcorr.csh distcorr.lis
ls -1 gfTo_RH*.fits > mask_AGX.lis
mask_AGX.csh mask_AGX.lis
ln -s $gitdir/SuprimeCam/Astromatic/sext_astfile.param .
ln -s $gitdir/SuprimeCam/Astromatic/sext_astfile_final.param .
ln -s $gitdir/SuprimeCam/Astromatic/sext_scam_astrom.config .
ln -s $gitdir/SuprimeCam/Astromatic/scamp_scam.config .
python $gitdir/SuprimeCam/python/rename_before_swarp.py
python $gitdir/SuprimeCam/python/make_wht_for_swarp_1.py
ls object*.fits | grep -v wht | xargs -I {} -P 20 python $gitdir/SuprimeCam/python/run_sext_scam_1.py {}
ln -s $gitdir/SuprimeCam/Astromatic/scamp_scam_pass2.config .
scamp object*cat -c scamp_scam.config
ls object*fits | grep -v wht > good_frames_pass1.txt
```

# Shallow Band Bulk Commands
To be run after the respective setup commands above.
__Be sure to change the flat directory!__
```
ls -1 SUPA*.fits > namechange.lis
namechange.csh namechange.lis
ls -1 H*.fits > ovserscansub.lis
overscansub.csh ovserscansub.lis
ln -s ../../flats/2009mar/$band'/skyflat'*.fits .
ls -1 skyflat*.fits > ffield_mf.lis
ls -1 To_RH*.fits > ffield_im.lis
ffield.csh ffield_mf.lis ffield_im.lis
ls -1 fTo_RH*.fits > distcorr.lis
distcorr.csh distcorr.lis
ls -1 gfTo_RH*.fits > mask_AGX.lis
mask_AGX.csh mask_AGX.lis
python $gitdir/SuprimeCam/python/rename_before_swarp.py
mv object*.fits ../V/.
```

## After moving both 2009 and 2010 V-band data to the V folder
```
ln -s $gitdir/SuprimeCam/Astromatic/sext_astfile.param .
ln -s $gitdir/SuprimeCam/Astromatic/sext_astfile_final.param .
ln -s $gitdir/SuprimeCam/Astromatic/sext_scam_astrom.config .
ln -s $gitdir/SuprimeCam/Astromatic/scamp_scam.config .
python $gitdir/SuprimeCam/python/make_wht_for_swarp_1.py
ls object*.fits | grep -v wht | xargs -I {} -P 20 python $gitdir/SuprimeCam/python/run_sext_scam_1.py {}
ln -s $gitdir/SuprimeCam/Astromatic/scamp_scam_pass2.config .
scamp object*cat -c scamp_scam_pass2.config -ASTREFCAT_NAME ../$basename'_'$deepband'.cat'
ls object*fits | grep -v wht > good_frames_pass1.txt
```

## Check if any of the exposures failed during scamp and edit good_frame_pass1.txt

```
ln -s $gitdir/Suprimecam/Astromatic/swarp_scam_1.config .
swarp @good_frames_pass1.txt -c swarp_scam_1.config -CENTER_TYPE MANUAL -CENTER $fieldcenter
ds9 -tile swarp_median.fits -scale log -scale limits 0 100 -zoom to fit swarp_median_wht.fits -scale linear -scale minmax -zoom to fit
```

## look at swarp images

```
python $gitdir/SuprimeCam/python/make_wht_for_swarp_2.py swarp_median.fits 3.
python $gitdir/SuprimeCam/python/add_exptime.py $EXPTIME
ln -s $gitdir/Suprimecam/Astromatic/swarp_scam_2.config .
swarp *resamp.fits -c swarp_scam_2.config -CENTER_TYPE MANUAL -CENTER $fieldcenter
ds9 -tile swarp_wmean.fits -scale log -scale limits 0 100 -zoom to fit swarp_wmean_wht.fits -scale linear -scale mode minmax -zoom to fit
```

## look at swarp images

```
ln -s $gitdir/SuprimeCam/Astromatic/swarp_texp.config .
python $gitdir/SuprimeCam/python/make_texp_map.py $EXPTIME $fieldcenter
python $gitdir/SuprimeCam/python/fix_header_final.py swarp_wmean.fits $zp
ln -s $gitdir/SuprimeCam/Astromatic/sext_scam_final.config .
python $gitdir/SuprimeCam/python/run_sext_final.py swarp_wmean.fits --detectfile ../$basename'_'$deepband'.fits'
mv swarp_wmean.fits ../$basename'_'$band'.fits'
mv swarp_wmean_wht.fits ../$basename'_'$band'_wht.fits'
mv swarp_wmean.cat ../$basename'_'$band'.cat'
mv swarp_wmean.reg ../$basename'_'$band'.reg'
```
