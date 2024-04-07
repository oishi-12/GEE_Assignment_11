var roi = 
    /* color: #d63000 */
    /* shown: false */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[91.78233856345734, 22.51957639780854],
          [91.78233856345734, 22.48468616095691],
          [91.84688324119172, 22.48468616095691],
          [91.84688324119172, 22.51957639780854]]], null, false);

var s2 = ee.ImageCollection("COPERNICUS/S2_SR_HARMONIZED")
        .filterBounds(roi)
      .filterDate("2023-01-01", "2023-12-01")
.filter(ee.Filter.lt("CLOUDY_PIXEL_PERCENTAGE", 6))
.first()
.select(["B8", "B11"])
Map.addLayer(s2.clip(roi),{},"rgb image")
print(s2)

var ndwi = s2.normalizedDifference(["B8", "B11"]).rename("NDWI")
print(ndwi)
var vizParam = {
  min: -1,
  max: 1,
  palette: ["green", "yellow"]
}
Map.addLayer(ndwi.clip(roi),vizParam,"ndwi image")


var stat =ndwi.reduceRegion({
  reducer: ee.Reducer.minMax(),
  geometry: roi,
  scale: 10,
  bestEffort:true
  
})
print(stat)
