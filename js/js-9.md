# JS 9

* mapbox datasets
* mapbox styles

We use Mapbox studio to design map styles trougth **tilesets** and **GEOjson** data.

```
//GEOjson to rappresent map data: 

{
  "features": [
    {
      "type": "Feature",
      "properties": {
        "title": "Lincoln Park",
        "description": "A northside park that is home to the Lincoln Park Zoo"
      },
      "geometry": {
        "coordinates": [-87.637596, 41.940403],
        "type": "Point"
      }
    }]
}

features.geometry(coordinates) and features.properties(other data)
```

_Datasets_ are editable collections of GEOjson:

<figure><img src="../.gitbook/assets/Datasets.PNG" alt=""><figcaption><p>here we can draw new points and add features.properties to it, (title and description) or upload a GEOjson</p></figcaption></figure>

We export the dataset to create a new **Tileset**, a map is divided into tiles for performance.

_Tilesets_ are **used as source** in map style **layers** (where we can add symbol custom icons).

<figure><img src="../.gitbook/assets/TilesetStyle.PNG" alt=""><figcaption><p>After we style the layer</p></figcaption></figure>

After **making our style public** on mapbox studio we can also use its tileset source:

```
//we pass the point PROPERTY of the click EVENT to the tileset style layer 

mapp.on('click', (event) => {

    const features = mapp.queryRenderedFeatures(event.point, {
      layers: ['chicago-parks']
    });
    if (!features.length) {
      return;
    }

    const feature = features[0];
//if it matches it will generate a Popup, setting its LngLat and HTML
  
    const popup = new mapboxgl.Popup({ offset: [0, -15] })
    .setLngLat(feature.geometry.coordinates)
    .setHTML(
      `<h4>${feature.properties.title}</h4><p>${feature.properties.description}</p>`
    )
    .addTo(mapp);

});
```

<figure><img src="../.gitbook/assets/PopUplayer.PNG" alt=""><figcaption><p>this PopUp could be edited trought its css .mapboxgl-popup-content </p></figcaption></figure>

We can add a **flyTo animation** effect _between the points_ :

```
//instead of click we start it on LOADed map

mapp2.on('load', (e)=>{

  const features = mapp2.queryRenderedFeatures(e.point, {
    layers: ['chicago-parks']
  });
  if (!features.length) {
    return;
  }
  
  let index= 0
  
  for(const x of features) {
    index+= 1
    
    setTimeout(() => {
      mapp2.flyTo({ 
        center: x.geometry.coordinates,
        zoom: 13
      });

    //Markers don't need to be added, already in the style

    }, 2000 * index);
  //each point gets 2000 of timeout before passing to the other
}

```



