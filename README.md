#Transboundary Flood Extent Mapping using Google Earth Engine:

This repository contains Google Earth Engine (GEE) scripts for SAR-based flood extent mapping in transboundary river basins using Sentinel-1A Ground Range Detected (GRD) imagery. The workflow was developed for upstream–downstream flood assessment in the Kosi and Teesta river systems.

Note: the following codes illustrate the flood extent analysis only for one year as reference (i.e., 2023). The same codes are used throughout for all years in this analysis only by changing the dates and years.

#Features:
Sentinel-1A SAR preprocessing;
Pre-flood and post-flood image mosaicking;
Speckle reduction using focal median filtering;
SAR backscatter change detection;
Threshold-based flood inundation extraction;
Flood extent visualization and export;
Compatible with transboundary flood analysis;


#Methodology:
The workflow applies change detection between pre-flood and post-flood SAR backscatter:

[
\Delta \sigma^0 = \sigma^0_{after} - \sigma^0_{before}
]

Flooded pixels are identified where:

[
\Delta \sigma^0 < -3 \text{ dB}
]


#The script generates:
Before flood map;
After flood map;
Flood difference map;
Binary flood extent layer;


#Source:
Sentinel-1A SAR GRD imagery;
Google Earth Engine Data Catalog;
Software Requirements;
Google Earth Engine;
JavaScript API;


#Applications:
Flood hazard mapping;
Upstream–downstream flood comparison;
Transboundary flood assessment;
Climate disaster monitoring;
Flood vulnerability studies;


