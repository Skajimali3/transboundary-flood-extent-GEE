Transboundary Flood Extent Mapping using Google Earth Engine
This repository contains Google Earth Engine (GEE) scripts for SAR-based flood extent mapping in transboundary river basins using Sentinel-1A Ground Range Detected (GRD) imagery. The workflow was developed for upstream–downstream flood assessment in the Kosi and Teesta river systems.

-----------GEE code for Flood Extent analysis-----------------------

//extract the Upstream Downstream Shapefile from table
------First rename the variable 'table' to 'AOI' by selecting and typing-----------
//Then write the code:
Map.addLayer(AOI);

//import the satellite image collection
var collection = ee.ImageCollection("COPERNICUS/S1_GRD")
.filterBounds(AOI)
.filter(ee.Filter.listContains("transmitterReceiverPolarisation","VV"))
.select('VV')

//filter the before and after flood imageries

var before = collection.filterDate("2023-09-20","2023-09-30").mosaic()
var after = collection.filterDate("2023-10-03", "2023-10-10").mosaic()

var before_clip = before.clip(AOI)
var after_clip = after.clip(AOI)

//apply smoothening filter

var before_s = before_clip.focal_median(30, "circle", "meters")
var after_s = after_clip.focal_median(30, "circle", "meters")

//difference

var difference = after_s.substract(before_s)

var flood extent = difference.lt(-3)
var flood = flood_extent.updateMask(flood_extent)

//display maps
Map.addLayer(before_clip,{min: -30, max:0}, "Before_flood_T_2023")
Map.addLayer(after_clip,{min: -30, max:0}, "After_flood_T_2023")
Map.addLayer(difference,{}, "Difference_T_2023")
Map.addLayer(flood, {},"Flood_Extent_T_2023")



// Import the Sentinel-1 SAR Ground Range Detected (GRD) image collection.
var s1_collection = ee.ImageCollection("COPERNICUS/S1_GRD")
    .filterBounds(AOI)
    // Filter for images with 'VV' polarization to ensure consistency.
    .filter(ee.Filter.listContains("transmitterReceiverPolarisation", "VV"))
    // Select only the 'VV' band for analysis.
    .select('VV');

// Define the 'before' and 'after' flood date ranges.
// These dates are specific to the 2023 Kosi/Teesta flood events.
var date_before_start = '2023-09-20';
var date_before_end = '2023-09-30';
var date_after_start = '2023-10-04';
var date_after_end = '2023-10-15';

// Filter the collection for 'before' flood imagery and create a mosaic.
var before_flood_image = s1_collection.filterDate(date_before_start, date_before_end).mosaic();

// Filter the collection for 'after' flood imagery and create a mosaic.
var after_flood_image = s1_collection.filterDate(date_after_start, date_after_end).mosaic();

// Clip the before and after images to the AOI.
var before_flood_clipped = before_flood_image.clip(AOI);
var after_flood_clipped = after_flood_image.clip(AOI);

// Apply a median filter for speckle reduction (smoothening).
// Focal window size: 30 meters, circular shape.
var before_flood_smoothed = before_flood_clipped.focal_median(30, "circle", "meters");
var after_flood_smoothed = after_flood_clipped.focal_median(30, "circle", "meters");

// Calculate the difference between the 'after' and 'before' smoothed images.
var difference_image = after_flood_smoothed.subtract(before_flood_smoothed);

// Identify potential flood extent based on a threshold.
// A negative difference indicates a decrease in backscatter, often associated with water.
var flood_extent_raw = difference_image.lt(-3); // Threshold of -3 dB for flood detection.

// Create a masked flood extent layer, showing only the areas identified as flooded.
var flood_extent = flood_extent_raw.updateMask(flood_extent_raw);

// Define visualization parameters for SAR imagery.
var sar_vis_params = {min: -30, max: 0, palette: ['black', 'gray', 'white']}; // dB values

// Define visualization parameters for the difference image.
var diff_vis_params = {min: -10, max: 5, palette: ['blue', 'white', 'red']};

// Define visualization parameters for the flood extent.
var flood_vis_params = {min: 0, max: 1, palette: ['black', 'blue']};

// Display the processed images on the map.
Map.addLayer(before_flood_clipped, sar_vis_params, "Before Flood Teesta (2023)");
Map.addLayer(after_flood_clipped, sar_vis_params, "After Flood Teesta (2023)");
Map.addLayer(difference_image, diff_vis_params, "Difference Teesta (After - Before)");
Map.addLayer(flood_extent, flood_vis_params, "Flood Extent Teesta 2023 (Threshold < -3)");

// Add an information layer to indicate processing complete.
print('Flood mapping process complete for ' + AOI.first().get('gaul1_name') + ' during 2023 Kosi/Teesta flood period.');
print('Dates: Before (' + date_before_start + ' to ' + date_before_end + '), After (' + date_after_start + ' to ' + date_after_end + ')');


// Export the flood extent map layer to Google Drive.
Export.image.toDrive({
  image: flood_extent, // Corrected: flood_extent is already an Image object.
  description: 'Flood Extent Teesta 2023 Export',
  folder: 'EarthEngineExportMaps',
  fileNamePrefix: 'Flood_Extent_Teesta_2023',
  region: AOI, // Corrected: AOI should be passed as the region parameter.
  scale: 10, // Output resolution in meters per pixel.
  maxPixels: 1e13
});


// Export the before flood map layer to Google Drive.
Export.image.toDrive({
  image: before_flood_clipped, // Corrected: before_flood_clipped is already an Image object.
  description: 'Before Flood Teesta 2023 Export',
  folder: 'EarthEngineExportMaps',
  fileNamePrefix: 'Before_Flood_Teesta_2023',
  region: AOI, // Corrected: AOI should be passed as the region parameter.
  scale: 10, // Output resolution in meters per pixel.
  maxPixels: 1e13
});


// Export the after flood map layer to Google Drive.
Export.image.toDrive({
  image: after_flood_clipped, // Corrected: after_flood_clipped is already an Image object.
  description: 'After Flood Teesta 2023 Export',
  folder: 'EarthEngineExportMaps',
  fileNamePrefix: 'After_Flood_Teesta_2023',
  region: AOI, // Corrected: AOI should be passed as the region parameter.
  scale: 10, // Output resolution in meters per pixel.
  maxPixels: 1e13
});


// Export the flood difference map layer to Google Drive.
Export.image.toDrive({
  image: difference_image, // Corrected: difference_image is already an Image object.
  description: 'Flood Difference Teesta 2023 Export',
  folder: 'EarthEngineExportMaps',
  fileNamePrefix: 'Flood_Difference_Teesta_2023',
  region: AOI, // Corrected: AOI should be passed as the region parameter.
  scale: 10, // Output resolution in meters per pixel.
  maxPixels: 1e13
});
