Legoland Billund in Parkitect
=============================

This project is intended to build a faithful 1:1 reconstruction of Legoland Billund in the game Parkitect, as the park looked in the spring of 2019. No mods were used, but the A Taste Of Adventure expansion pack is needed. The primary goal is to make the park look from the ground as it does in real life, but it should also be a functioning Parkitect park.

The map
-------

`legoland.svg` in this repository is an Inkscape vector file. I initialized it by downloading the region from OpenStreetMap as an SVG file. The file made Inkscape very slow, so I exported it as a high-resolution PNG (higher than OSM will give you by default) and imported that into the `reference` layer. Then I removed all unneeded geometry from the `orig` layer, leaving pretty much only buildings, ride tracks and water.

Then I configured a grid of 1 pixel, and another (dotted) grid of 1/2 pixel to make it easy to place elements in the middle of tiles. Then I rotated and scaled the map to mostly align with the grid and represent 1:1 size. Document boundaries were then set to 320x320 pixels, which will become the map size in Parkitect. The map is rotated to have the entrance in the bottom left corner because it felt natural to me, but note that in the game I used the proper rotation to have the sun in the south.

Of course Parkitect only lets you build on the grid, so I had to make some tradeoffs here. I chose to align the long chain of hotel buildings on the park's south-eastern side with the grid, which means that the Nordmarksvej will run at an almost perfectly 45 degree angle. This has consequences for the entrance plaza, so I moved the curve in the Nordmarksvey up a little to make things fit.

It also leaves a lot of buildings and rides at odd angles, so I had to rotate and tweak stuff quite a lot to make all the pieces fit together.

Resources
---------

- [OpenStreetMap](https://www.openstreetmap.org/query?lat=55.73489&lon=9.12797#map=17/55.73584/9.12592), which has far more detail than Google Maps, and lets you download things.

- [Google Maps](https://www.google.com/maps/@55.7358396,9.1253156,613m/data=!3m1!1e3) and of course Google Street View. Note that the official Street View imagery (the blue lines on the map) is from 2017, and the park has been thoroughly redecorated and modernized between then and 2019. Most photos taken by users (the blue dots on the map) are more recent, and were used for the detailing wherever possible.

- [SDFE Skråfoto](https://skraafoto.kortforsyningen.dk/oblivisionjsoff/index.aspx?project=Denmark&lon=9.1253156&lat=55.7358396), official aerial photos published by the Danish government, taken from straight overhead and from four oblique angles. This is a fantastic resource because it shows the park much from the same perspective as it looks in Parkitect. The top-down photos are from 2017, but the oblique ones are from 2019 after the remodelling.

- [DHM - Danmarks Højdemodel](https://download.kortforsyningen.dk/content/geodataprodukter?field_korttype_tid_1=653&field_aktualitet_tid=749&field_datastruktur_tid=All&field_scheme_tid=All), a LIDAR height map of Denmark with a resolution of 40 cm. After figuring out the Danish and creating a free account, I downloaded the relevant 10×10 km tile (617_50) of the DHM/Overflade (height including buildings, trees and such) and DHM/Terræn (height of the terrain surface) data sets in GeoTIFF format.

- [LEGO Colors](https://rebrickable.com/colors/) at Rebrickable for the hexadecimal RGB codes of the official LEGO colours. Great for copying and pasting straight into the Parkitect colour picker.

Height map notes
----------------

- Added base map to QGIS using WMS URL `https://services.kortforsyningen.dk/topo_skaermkort?token=XXX` with the token created [here](https://download.kortforsyningen.dk/content/min-side) after login.
- Downloaded `DSM_617_50_TIF_UTM32-ETRS89.zip` and `DTM_617_50_TIF_UTM32-ETRS89.zip`.
- The used CRS is EPSG:25832 (ETRS89 / UTM zone 32N).
- Each contains 100 tiles of 1×1 km each. The relevant ones are numbered `6176_507` and `6176_508`.
- These GeoTIFFs were extracted from the ZIP, added in QGIS and merged using the Processing Toolbox: GDAL > Raster miscellaneous > Merge. The outputs are `D[ST]M_merged.tif`.
- We consider the Nordmarksvej as flat, at 64.0 meters. This becomes a height value of 3.0 in-game (the base height of an empty map). The layers were shifted and scaled into game units using the Raster Calculator using an expression like `("DTM_merged@1" - 64) / 3 + 3`. The outputs are `D[ST]M_merged_scaled.tif`.
- To save disk space, the result was then clipped to a bounding box. The outputs are `D[ST]M_merged_scaled_clipped.tif`.
- A color scale was added to threshold the height levels into steps of 0.5.
- Exporting this to a PNG at 1:1 resolution is tricky! I did it like this: zoom the map to 1:25000, go to File > Export > Export Map to Image, choose the layer as the extents, and then set 1589 dpi (manually calculated). The result is still somehow not _quite_ right but it'll do.

Notes
-----

- A grid square in Parkitect is 3 meters. Height units are presumably also 3 meters.
- The base height of an empty map is 3.0.
- Legotop height is 8 game units.

Ride substitutions
------------------

Not all rides in Legoland are available in Parkitect, so (wanting to do this without mods) I had to get a bit creative.

- The traffic school, where kids drive vehicles around in a miniature town, became a Go Kart track.

- Atlantis Sea Life became a Haunted House.