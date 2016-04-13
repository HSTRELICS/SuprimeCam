RXCJ2211 it falls within the SDSS footprint so I will estimate the zeropoints
based off a rough comparison with SDSS. Note that this will be a very rough
zeropoint estimate, but this should suffice since the immediate need is just
Hectospec target selection.

## Using the reduced Subaru archival V and i images
Run through the reduction notes RXCJ2211_Reduction_Notes.md with specifying a
zero point of 0 during the "Fix header in final combined image" step near the
end of the reduction.

## Query the SDSS database
SELECT
p.objID,
p.ra,
p.dec,
p.raErr,
p.decErr,
p.type,
p.u,
p.g,
p.r,
p.i,
p.z
FROM
PhotoObj AS p
WHERE
p.ra BETWEEN 332.692 AND 333.190
AND p.dec BETWEEN -4.06 AND -3.1
AND p.type = 6 #just stars

Save the catalog as rxcj2211_SDSSstars.csv

## Match the SDSS and Subaru catalogs

Using the SExtractor catalogs generated from the final coadd of the reduction I
matched them to the SDSS star catalog rxcj2211_SDSSstars.csv which includes all
SDSS stars withing a 20' radius of the field center and their magnitdes (u, gu,
r, i, z). The matching was done using TopCat spatial matching based on RA, Dec
of each catalog.

## Estimate Zero Point based on median color

Then in the ZeroPointEstimate_RXCJ2211.ipynb I determined the median difference
between the SDSS and Subaru bands over a reasonable magnitude range to estimate
the following zero points. (which agree to within ~0.01 mags with Chris'
estimates from a few nights later.)

Results:
---------

band    ZP
----  ------
V     27.059
i     27.325
