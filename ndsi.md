var roi = 
    /* color: #d63000 */
    /* shown: false */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[91.78371185447297, 22.520527826402972],
          [91.78371185447297, 22.484527404195028],
          [91.84980191482579, 22.484527404195028],
          [91.84980191482579, 22.520527826402972]]], null, false);

var s2 = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
        .filterBounds(roi)
      .filterDate("2021-01-01", "2021-12-01")
.filter(ee.Filter.lt("CLOUDY_PIXEL_PERCENTAGE", 6))
.first()
.select(["B4", "B6"])
Map.addLayer(s2.clip(roi),{},"rgb image")
print(s2)

var ndsi = s2.normalizedDifference(["B4", "B6"]).rename("NDSI")
print(ndsi)
var vizParam = {
  min: -1,
  max: 1,
  palette: ["cyan", "yellow"]
}
Map.addLayer(ndsi.clip(roi),vizParam,"ndsi image")


var stat =ndsi.reduceRegion({
  reducer: ee.Reducer.minMax(),
  geometry: roi,
  scale: 10,
  bestEffort:true
  
})
print(stat)
