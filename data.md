## Data used during IAS 2016 Short Course

all data can be downloaded from http://ode.rsl.wustl.edu/mars/index.aspx 

### CTX

EDR from PDS Geosciences Node

* 99M D21_035563_1987_XN_18N282W.IMG
* 49M F04_037396_1985_XN_18N282W.IMG
* 79M P19_008650_1987_XI_18N282W.IMG

### HiRISE

DTM and orthoimages from University of Arizona
http://hirise.lpl.arizona.edu/dtm/dtm.php?ID=PSP_002387_1985

* 305M DTEEC_002387_1985_003798_1985_A01.IMG
* 369M PSP_002387_1985_RED_A_01_ORTHO.JP2
* 5.3K PSP_002387_1985_RED_A_01_ORTHO.LBL


### CRISM

TRDR/DDR from PDS Geosciences Node

* 8.2M hrl000040ff_07_de183l_ddr1.img
* 4.5K hrl000040ff_07_de183l_ddr1.lbl
* 8.2M hrl000040ff_07_de183s_ddr1.img
* 4.5K hrl000040ff_07_de183s_ddr1.lbl
* 257M hrl000040ff_07_if183l_trr3.img
* 9.3K hrl000040ff_07_if183l_trr3.lbl
*  63M hrl000040ff_07_if183s_trr3.img
* 8.5K hrl000040ff_07_if183s_trr3.lbl
* 572K hrl000040ff_07_ra183l_hkp3.tab
* 257M hrl000040ff_07_ra183l_trr3.img
* 9.3K hrl000040ff_07_ra183l_trr3.lbl
* 572K hrl000040ff_07_ra183s_hkp3.tab
*  63M hrl000040ff_07_ra183s_trr3.img
* 8.5K hrl000040ff_07_ra183s_trr3.lbl

Link to PlanetServer - http://access.planetserver.eu/index.html?&lat=18.51&lon=77.34&range=2e5

### HRSC

Level4 from PDS Geosciences Node (or PSA)

*  43M h0988_0000_da4.img
* 774M h0988_0000_nd4.img 

### MOLA (crop)

MEGDR from PDS Geosciences Node or [USGS](http://astrogeology.usgs.gov/search/details/Mars/GlobalSurveyor/MOLA/Mars_MGS_MOLA_DEM_mosaic_global_463m/cub)


## Map_template (sample)

see https://isis.astrogeology.usgs.gov/UserDocs/index.html

```
  Group = Mapping
    ProjectionName     = EQUIRECTANGULAR
    CenterLongitude    = 0.0
    CenterLatitude     = 0.0
    TargetName         = Mars
    EquatorialRadius   = 3396190.0 <meters>
    PolarRadius        = 3396190.0 <meters>
    LatitudeType       = Planetocentric
    LongitudeDirection = PositiveEast
    LongitudeDomain    = 180
  End_Group
```

## Processing steps 

some skipped for ease during limited time, reproducible though with info below.

### HiRISE

#### DTM
source DTM in PDS format, orthoimages in JP2, from http://hirise.lpl.arizona.edu/dtm/dtm.php?ID=PSP_002387_1985

```
pds2isis from=DTEEC_002387_1985_003798_1985_A01.IMG to=DTEEC_002387_1985_003798_1985_A01.cub

map2map from=DTEEC_002387_1985_003798_1985_A01.cub map=../map_template-CTX.map \
  to=DTEEC_002387_1985_003798_1985_A01.eqc.cub pixres=from
```

#### Orthoimage(s)

```
JP2_to_PDS -Input PSP_002387_1985_RED_A_01_ORTHO.JP2 \
  -LAbel PSP_002387_1985_RED_A_01_ORTHO.LBL \
  -Output PSP_002387_1985_RED_A_01_ORTHO.IMG
  
pds2isis from=PSP_002387_1985_RED_A_01_ORTHO.IMG to=PSP_002387_1985_RED_A_01_ORTHO.cub

map2map from=PSP_002387_1985_RED_A_01_ORTHO.cub map=../map_template-CTX.map \
  to=PSP_002387_1985_RED_A_01_ORTHO.eqc.cub pixres=from
```

### CTX

source EDR, going thorugh PDS --> level0 cub --> level1 cub --> level2 cub

for ease https://gist.github.com/aprossi/dac0f38f4991f8da9fcfe3722728ce16

steps:
* mroctx2isis
* spiceinit
* ctxcal
* ctxevenodd
* cam2map

Plus, quick & dirty mosaic:

```
ls *lev2.cub > moslist

automos fromlist=moslist mosaic=ctx_mosaci_jezero.cub
```
<img src="https://farm2.staticflickr.com/1718/26617044272_c3bc74547f_b.jpg" width=200px> 

A bit less dirty:

```
ls F04_037396_1985_XN_18N282W.lev2.cub > holdlist

equalizer fromlist=moslist holdlist=holdlist

ls *equ.cub > equlist

automos fromlist=equlist mosaic=ctx_mosaci_jezero.equ.cub
```
<img src="https://farm2.staticflickr.com/1649/26709944605_e975e41cbb_b.jpg" width=200px>

(extraction of label to get the right kernels for VM)

```
catlab from=D21_035563_1987_XN_18N282W.lev1.cub to=D21_035563_1987_XN_18N282W.lev1-label.txt
catlab from=F04_037396_1985_XN_18N282W.lev1.cub to=F04_037396_1985_XN_18N282W.lev1-label.txt
catlab from=P19_008650_1987_XI_18N282W.lev1.cub to=P19_008650_1987_XI_18N282W.lev1-label.txt
```
* [lev1 label of D21_035563_1987_XN_18N282W](https://gist.github.com/aprossi/b7fa2456827588da76caa433ba325b94)
* [lev1 label of F04_037396_1985_XN_18N282W](https://gist.github.com/aprossi/d53722aa4131264d89853505a246d9b7)
* [lev1 label of P19_008650_1987_XI_18N282W](https://gist.github.com/aprossi/38e5691c058c3e98e6aa0d699b9e3eb0)

minimal set of MRO kernels and related data are on this [list](https://gist.github.com/aprossi/06f56760b1d4adc7730b18fa68bba714) (about 1 Gb, in addition to isis3 base data.

### HRSC

source Level4 Nadir
```
pds2isis from=h0988_0000_nd4.img to=h0988_0000_nd4.cub

map2map from=h0988_0000_nd4.cub map=../map_template-CTX.map \
  to=h0988_0000_nd4.eqc.cub pixres=from
  
maptrim from=h0988_0000_nd4.eqc.cub to=h0988_0000_nd4.eqc.trim.cub \
  minlat=17 maxlat=20.5 minlon=76.3 maxlon=77.9 mode=crop
```
<img src="https://farm2.staticflickr.com/1548/26710150635_b20b073485_b.jpg" width=200px>

source Level4 DA (MOLA-like)

```
pds2isis from=h0988_0000_da4.img to=h0988_0000_da4.cub

map2map from=h0988_0000_da4.cub to=h0988_0000_da4.eqc.trim.cub \
  map=h0988_0000_nd4.eqc.trim.cub pixres=from defaultrange=map
```

<img src="https://farm2.staticflickr.com/1550/26644016391_9801a5ddf6_b.jpg" width=200px>

### MOLA MEGDR 

```
maptrim from=Mars_MGS_MOLA_DEM_mosaic_global_463m.cub to=Mars_MGS_MOLA_DEM_mosaic_463m.trim.cub minlat=15 maxlat=25 minlon=65 maxlon=80 mode=crop
```

### Needed software
(only ISIS3 provided during short course)
ISIS3 - https://isis.astrogeology.usgs.gov/documents/InstallGuide/index.html
PDS_JP2 - http://pirlwww.lpl.arizona.edu/software/PDS_JP2/download.php

