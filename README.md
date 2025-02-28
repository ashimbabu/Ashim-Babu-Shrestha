# Ashim-Babu-Shrestha
avar classProperty = 'class';
var bands = ['SR_B2', 'SR_B3', 'SR_B4', 'SR_B5', 'SR_B6', 'SR_B7'];

// Define the study area (Nuwakot district)
var geometry = ee.Geometry.Polygon(
        [[[84.990674, 28.091033], [85.499411, 28.091033], 
          [85.499411, 27.760428], [84.990674, 27.760428]]]);

// Center the map on Nuwakot district
Map.centerObject(geometry, 10);

// Load the shapefile of Nuwakot district
var nuwakot = ee.FeatureCollection('users/ashimbabu/Nuwakot_District');

// Center the map on Nuwakot district
Map.centerObject(nuwakot);

// Add the shapefile layer to the map
Map.addLayer(nuwakot, {color: 'blue'}, 'Nuwakot District');

// Optional: Print some information about the shapefile
print('Nuwakot District Shapefile:', nuwakot);

// Load Landsat 9 Surface Reflectance Tier 1 collection
var landsat9 = ee.ImageCollection('LANDSAT/LC09/C02/T1_L2')
                  .filterBounds(geometry)
                  .filterDate('2024-05-01', '2024-05-31') // Adjust date range
                  .map(function(image) {
                    return image
                      .select(['SR_B2', 'SR_B3', 'SR_B4', 'SR_B5', 'SR_B6', 'SR_B7'])
                      .multiply(0.0000275).add(-0.2); // Convert DN to reflectance
                  });

// Get the number of images in the collection
var imageCount = landsat9.size();

// Print the number of images to the console
print('Number of images:', imageCount);

// Create a cloud-free composite using median value
var composite = landsat9.median().clip(geometry);

// Visualization parameters for Landsat
var visParams = {
  bands: ['SR_B4', 'SR_B3', 'SR_B2'], // RGB
  min: 0,
  max: 0.3,
  gamma: 1.4
};

// Display the composite
Map.addLayer(composite, visParams, 'Landsat 9 Composite');

// LULC classes: Water, Settlement, Vegetation, Agriculture, Other Land
var classNames = ['Water', 'Settlement', 'Vegetation', 'Agriculture', 'Other Land'];
var classValues = [0, 1, 2, 3, 4];

// Manually created training data (replace these with your own sample points)
var trainingData = ee.FeatureCollection([
  // Water
  ee.Feature(ee.Geometry.Point([84.884, 27.992]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.809, 28.035]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.855, 28.135]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.015, 27.807]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.919, 27.999]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.784, 27.814]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.742, 27.804]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.733, 27.904]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.774, 27.972]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([84.959, 27.027]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.076, 27.854]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.066, 27.856]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.044, 27.855]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.029, 27.850]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.182, 27.991]), {'class': 0}),
  
  // Settlement
  ee.Feature(ee.Geometry.Point([84.807, 27.990]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.810, 27.997]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.824, 28.002]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.819, 28.018]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.826, 28.015]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.819, 28.032]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.776, 27.935]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.805, 27.926]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.855, 27.947]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.100, 27.972]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.088, 27.975]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.118, 27.965]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.959, 27.965]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.939, 27.973]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([84.985, 27.934]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.148, 27.880]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.137, 27.881]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.144, 27.898]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.140, 27.921]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.140, 27.920]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.142, 27.921]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.143, 27.921]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.146, 27.925]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.143, 27.925]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.145, 27.927]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.141, 27.926]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.141, 27.925]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.144, 27.893]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.143, 27.894]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.140, 27.910]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.142, 27.911]), {'class': 1}),
  
  // Vegetation
  ee.Feature(ee.Geometry.Point([84.997, 28.003]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.096, 27.997]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.961, 27.972]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.875, 27.872]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.895, 27.901]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.925, 27.877]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.979, 27.874]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.795, 27.874]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.905, 28.131]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.889, 28.131]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.070, 28.176]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.092, 28.178]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.022, 28.177]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.836, 28.026]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.898, 28.016]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.887, 27.722]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.905, 27.754]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([84.954, 27.735]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.216, 27.863]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.160, 27.862]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.463, 27.969]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.001, 28.076]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.186, 27.967]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.177, 27.964]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.091, 27.911]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.093, 27.911]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.088, 27.911]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.085, 27.912]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.245, 27.897]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.359, 27.819]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.432, 27.888]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.351, 27.960]), {'class': 2}),
  
    // Agriculture
  ee.Feature(ee.Geometry.Point([85.009, 27.808]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.000, 27.804]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.925, 27.800]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.774, 27.811]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.764, 27.815]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.858, 27.804]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.751, 27.873]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.734, 27.926]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.823, 28.049]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.822, 28.056]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.923, 28.008]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([84.920, 28.011]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.078, 27.857]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.120, 27.875]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.155, 27.947]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.117, 28.029]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.126, 28.030]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.106, 28.032]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.107, 28.021]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.125, 28.019]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.041, 27.946]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.038, 27.946]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.044, 27.944]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.041, 27.944]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.081, 27.909]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.081, 27.908]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.084, 27.908]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.188, 27.852]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.196, 27.892]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.205, 27.885]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.094, 27.971]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.286, 27.949]), {'class': 3}),
  
   // Other Land
  ee.Feature(ee.Geometry.Point([84.742, 27.865]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.759, 27.947]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.160, 28.059]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.088, 28.070]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.181, 28.119]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.959, 28.122]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.796, 28.150]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.816, 27.719]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.811, 27.715]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.138, 27.893]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.020, 27.844]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.775, 27.993]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([84.781, 27.802]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.054, 28.159]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.033, 28.177]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.472, 28.047]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.476, 28.047]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.473, 28.044]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.471, 28.041]), {'class': 4})
  ]);

// Sample the composite at the training points
var training = composite.sampleRegions({
  collection: trainingData,
  properties: ['class'],
  scale: 30
});

// Train a Random Forest classifier
var classifier = ee.Classifier.smileRandomForest(100).train({
  features: training,
  classProperty: 'class',
  inputProperties: ['SR_B2', 'SR_B3', 'SR_B4', 'SR_B5', 'SR_B6', 'SR_B7']
});

// Classify the image
var classified2024 = composite.classify(classifier);

// Display the classified image
var classVis = {min: 0, max: 4, palette: ['blue', 'red', 'green', 'yellow', 'brown']
};

Map.addLayer(classified2024, classVis, 'LULC Classification 2024');

// Center the map on the Nuwakot district
Map.centerObject(nuwakot);

// Load the reference data (known ground truth points with LULC classes)
var referenceData = ee.FeatureCollection('SamplePoints');

// Accuracy assessment: Add a random column to the training dataset
var withRandom = training.randomColumn('random');

// Split the dataset into 80% training and 20% testing
var split = 0.8;
var trainingPartition = withRandom.filter(ee.Filter.lt('random', split));
var testingPartition = withRandom.filter(ee.Filter.gte('random', split));

// Train the classifier with the training data partition
var trainedClassifier = ee.Classifier.smileRandomForest(500).train({
  features: trainingPartition,
  classProperty: classProperty,
  inputProperties: bands
});

// Classify the test dataset
var test_1 = testingPartition.classify(trainedClassifier);

// Print the Confusion Matrix
var confusionMatrix_2024 = test_1.errorMatrix(classProperty, 'classification');
print('Confusion Matrix 2024:', confusionMatrix_2024);

// Overall Accuracy
var overallAccuracy = confusionMatrix_2024.accuracy();
print('Overall Accuracy:', overallAccuracy);

// Kappa Coefficient
var kappa = confusionMatrix_2024.kappa();
print('Kappa Coefficient:', kappa);

// Producer's Accuracy (for each class)
var producersAccuracy = confusionMatrix_2024.producersAccuracy();
print('Producer\'s Accuracy:', producersAccuracy);

// User's Accuracy (for each class)
var usersAccuracy = confusionMatrix_2024.consumersAccuracy();
print('User\'s Accuracy:', usersAccuracy);

// Sample the classified image at the reference points
var SampledPoints = classified2024.sampleRegions({
  collection: referenceData,
  properties: ['landcover'],  // This should be the ground truth property in the reference data
  scale: 30,  // Set this to match the resolution of your classified image
  geometries: true
});

// Manually created sample points (for accuracy asssessment)
var SampledPoints = ee.FeatureCollection([

  // Accuracy Assessment (Settlements)
  ee.Feature(ee.Geometry.Point([85.142, 27.888]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.145, 27.900]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.155, 27.967]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.108, 27.859]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.284, 27.889]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.364, 27.939]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.114, 27.831]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.064, 27.895]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.149, 27.793]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.113, 27.026]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.183, 27.970]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.153, 27.938]), {'class': 1}),
  ee.Feature(ee.Geometry.Point([85.139, 27.907]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.131, 27.885]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.104, 27.858]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.021, 27.843]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.179, 27.900]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.230, 27.904]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.304, 27.916]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.379, 27.922]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.265, 27.880]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.242, 27.892]), {'class': 0}),
  ee.Feature(ee.Geometry.Point([85.322, 27.814]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.182, 27.895]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.412, 27.960]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.202, 27.903]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.106, 27.898]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.203, 27.846]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.256, 27.834]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.359, 27.871]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.445, 27.898]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.068, 27.997]), {'class': 2}),
  ee.Feature(ee.Geometry.Point([85.164, 27.895]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.095, 27.971]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.293, 27.967]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.246, 27.789]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.423, 27.858]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.233, 27.952]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.023, 27.908]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.315, 27.861]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.373, 27.843]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.042, 27.965]), {'class': 3}),
  ee.Feature(ee.Geometry.Point([85.400, 27.024]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.458, 27.958]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.437, 27.878]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.465, 27.877]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.083, 27.059]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.077, 27.003]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.413, 27.812]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.020, 27.844]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.029, 27.995]), {'class': 4}),
  ee.Feature(ee.Geometry.Point([85.354, 27.811]), {'class': 4}),
  ]);

// Print the sampled points to inspect
print('SampledPoints:', SampledPoints);

// Add sampled points to the map (optional for visualization)
Map.addLayer(SampledPoints, {}, 'SampledPoints');
Map.centerObject(nuwakot);

// Export the classified image
Export.image.toDrive({
  image: classified2024,
  description: 'LULC_Nuwakot_Classified_2024',
  scale: 30,
  region: geometry,
  fileFormat: 'GeoTIFF'
});

// Define a function to add a legend to the map
function addLegend() {
  var legend = ui.Panel({
    style: {
      position: 'bottom-left',
      padding: '8px 15px'
    }
  });

  // Create legend title
  var legendTitle = ui.Label({
    value: 'LULC Classification',
    style: {fontWeight: 'bold', fontSize: '16px', margin: '0 0 4px 0'}
  });
  legend.add(legendTitle);

  // Create and style one row of the legend
  function makeRow(color, name) {
    var colorBox = ui.Label({
      style: {
        backgroundColor: color,
        padding: '8px',
        margin: '0 0 4px 0'
      }
    });

    var description = ui.Label({
      value: name,
      style: {margin: '0 0 4px 6px'}
    });

    return ui.Panel({
      widgets: [colorBox, description],
      layout: ui.Panel.Layout.Flow('horizontal')
    });
  }

  // Palette corresponds to classification classes
  var palette = ['yellow', 'brown', 'red', 'green', 'blue'];
  var names = ['Agriculture', 'Other Land', 'Settlement', 'Vegetation', 'Water'];

  for (var i = 0; i < palette.length; i++) {
    legend.add(makeRow(palette[i], names[i]));
  }

  Map.add(legend);
}

// Call the function to add the legend
addLegend();
