# AR Detection Algorithm - Kennett (2021)

[![DOI](https://zenodo.org/badge/392864728.svg)](https://zenodo.org/badge/latestdoi/392864728)

An Atmospheric River (AR) detection algorithm for global or regional studies. The algorithm is a modified version of the Guan & Waliser (2015) method, implemented in python. Note this code will be subject to amendment and improvement over time.

If you use or adapt this python script please acknowledge the source, and reference the GitHub repository at: https://github.com/daemonkennett/ar_detection

I am always interested in learning more about AR research. Feel free to contact me at daemonkennett@gmail.com

## Detection Algorithm Description:

This AR detection algorithm is a modified version of the Guan & Waliser (2015) method. Identification of ARs is based on the intensity and geometry of integrated water vapour transport (IVT). 

The detection algorithm was originally run using data sourced from the ERA5 reanalysis dataset of the European Centre for Medium-Range Weather Forecasts, using 6-hourly IVT data with a grid resolution of 0.25 degrees. The script is easily modifiable to run with other IVT data input. The algorithm identifies regions of enhanced IVT whose shape and IVT direction are consistent with the AR definition. To identify regions of enhanced IVT, the IVT magnitude is checked against a threshold value; defined for each month as the 85th percentile IVT for the 5-month period centred on that month. A fixed lower limit of 150 kg/m/s is also imposed. Both the percentile and minimum threshold values can be modified. Contiguous regions of grid cells with IVT values above the percentile threshold and fixed lower limit are isolated and labelled. If a region of enhanced IVT intersects the land mask, the grid cell with the maximum IVT over the land mask is identified. This grid cell is defined as the landfall location. The object axis is then calculated following Brands et al. (2017). Axis calculation can be described as follows:

- **(1)** The landfall location is labelled as the target grid cell, e.
- **(2)** The IVT direction at e is calculated and discretized into one of 8 cardinal directions (N, NE, E, SE, S, SW, W, NW). Of the 8 grid cells adjacent to e the upstream grid cell, s, and the two grid cells neighboring s are identified. Of these 3 candidate grid cells, the one with maximum IVT is tested to determine if the IVT exceeds the percentile threshold.
- **(3)** If the IVT threshold is exceeded this grid cell is labelled as the new target grid cell e, and we repeat step (2). This process is continued until the upstream grid cell fails to exceed the threshold or a grid cell is detected twice.

The grid cells identified by this process comprise the object axis. The length of the object is computed as the sum of the distances between neighbouring axis cells. To be identified as an AR, the following geometry criteria must be satisfied:

- **Length Check**: The length of the object must exceed 2000 km.
- **Narrowness Check**: The width of an object is defined as its surface area divided by its length. An object is discarded if its length/width ratio is less than 2.
- **Mean Meridional IVT Criterion**: An object is discarded if the mean IVT does not have a poleward component greater than 50 kg/m/s. This filters objects that do not transport moisture toward higher latitudes.
- **Coherence in IVT Direction Criterion**: An object is discarded if more than half of the grid cells have IVT deviating more than 45 degrees from the object’s mean IVT. This filters objects that do not feature a coherent IVT direction.
- **Consistency between IVT Direction and Object Orientation**: If object orientation deviates from the mean IVT direction by more than 45 degrees, the object is filtered. Object orientation is calculated as the angle between the first and last grid cells of the AR axis. Further, the distance between the first and last grid cells of the AR axis must be greater than 1000 km.

**References:**

Brands, S., Gutiérrez, J.M., San-Martín, D., 2017. Twentieth-century atmospheric river activity along the west coasts of Europe and North America: algorithm formulation, reanalysis uncertainty and links to atmospheric circulation patterns. Clim Dyn 48, 2771–2795. https://doi.org/10.1007/s00382-016-3095-6

Guan, B., Waliser, D.E., 2015. Detection of atmospheric rivers: Evaluation and application of an algorithm for global studies: Detection of Atmospheric Rivers. J. Geophys. Res. Atmos. 120, 12514–12535. https://doi.org/10.1002/2015JD024257

## Running the Detection Algorithm

To run the detection algorithm, simply run the ar\_detection.ipynb script after adjusting the appropriate parameters within the jupyter notebook. Input data should be included in a subdirectory named 'input\_data' in the same directory containing the ar\_detection.ipynb script. The 'input_data' directory 
should include a separate IVT .nc file for each month, for both  eastward and northward IVT, using the format 'e-ivt-YYYY-#m' and 'n-ivt-YYYY-#m' respectively, e.g. 'e-ivt-2021-1.nc'. A land mask .nc file should also be included in the same directory containing the ar\_detection.ipynb script. This must be a .nc file of the same lat/lon shape as the IVT files, with a single time dimension, and named 'land_mask.nc'. The netcdf files should be cropped to the desired lat/lon search region before running the detection algorithm. This can be done for example by using ncks from the NCO package.

Note that this detection algorithm was implemented on a Linux machine, and has not been tested for Windows/Mac users.

## License

MIT
