var roi = 
    /* color: #d63000 */
    /* shown: false */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[92.17715579583023, 22.518606437118255],
          [92.17715579583023, 22.45198970294549],
          [92.26435977532242, 22.45198970294549],
          [92.26435977532242, 22.518606437118255]]], null, false);

var s2 = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
        .filterBounds(roi)
      .filterDate("2022-01-01", "2022-12-01")
.filter(ee.Filter.lt("CLOUDY_PIXEL_PERCENTAGE", 6))


.first()
.select(["B4", "B5", "B3", "B2"])
Map.addLayer(s2.clip(roi),{},"rgb image")
print(s2)

var ndvi = s2.normalizedDifference(["B4", "B5"]).rename("NDVI")
print(ndvi)
var vizParam = {
  min: -1,
  max: 1,
  palette: ["blue", "yellow"]
}
Map.addLayer(ndvi.clip(roi),vizParam,"ndvi image")


var stat =ndvi.reduceRegion({
  reducer: ee.Reducer.minMax(),
  geometry: roi,
  scale: 10,
  bestEffort:true
  
})
print(stat)
