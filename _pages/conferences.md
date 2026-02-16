---
layout: page
title: Conferences
permalink: /conferences/
description: An interactive map of my conference presentations worldwide
nav: true
nav_order: 6
map: true
---

Over the years, I have presented my research at conferences across
the globe — from Santiago to Tokyo, London to Honolulu. This map
shows the locations of my academic presentations.

<div id="conference-map" style="height: 500px; width: 100%; border-radius: 8px; margin: 20px 0; z-index: 0;"></div>

<script>
document.addEventListener("DOMContentLoaded", function () {
  // Initialize the map centered to show most conferences
  var map = L.map('conference-map', {
    scrollWheelZoom: false
  }).setView([20, 0], 2);

  // Add tile layer (OpenStreetMap)
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors',
    maxZoom: 18
  }).addTo(map);

  // ---- Conference data ----
  var conferences = [
    {
      name: "GeoInno 2026",
      city: "Budapest, Hungary",
      lat: 47.4979,
      lng: 19.0402,
      year: 2026,
      title: "Unveiling the network core and periphery structures of technological development in salmon aquaculture"
    },
    {
      name: "Global Conference on Economic Geography 2025",
      city: "Worcester, USA",
      lat: 42.2626,
      lng: -71.8023,
      year: 2025,
      title: "Does the periphery matter across technological trajectories?"
    },
    {
      name: "Japan Assoc. of Economic Geographers 2025",
      city: "Tokyo, Japan",
      lat: 35.6762,
      lng: 139.6503,
      year: 2025,
      title: "Technological trajectories in core and periphery settings"
    },
    {
      name: "AAG Annual Meeting 2024",
      city: "Honolulu, USA",
      lat: 21.3069,
      lng: -157.8583,
      year: 2024,
      title: "Unequal geographies of technological change in Chilean salmon farming"
    },
    {
      name: "SOCHER 2022",
      city: "Antofagasta, Chile",
      lat: -23.6509,
      lng: -70.3975,
      year: 2022,
      title: "Putting the State back in: Neostructural innovation in Chilean salmon industry"
    },
    {
      name: "DiGRA 2019",
      city: "Kyoto, Japan",
      lat: 35.0116,
      lng: 135.7681,
      year: 2019,
      title: "Video Games Production in the periphery: the Chilean case"
    },
    {
      name: "AAG Annual Meeting 2019",
      city: "Washington DC, USA",
      lat: 38.9072,
      lng: -77.0369,
      year: 2019,
      title: "Affective Labour and the business of making games"
    },
    {
      name: "Global Conference on Economic Geography 2018",
      city: "Cologne, Germany",
      lat: 50.9375,
      lng: 6.9603,
      year: 2018,
      title: "Playing with the south: dependency in video games software development in Chile"
    },
    {
      name: "RGS-IBG Annual Conference 2017",
      city: "London, UK",
      lat: 51.5074,
      lng: -0.1278,
      year: 2017,
      title: "Gaming with the south? Cultural economy and GPNs of videogames in Chile"
    },
    {
      name: "XXXVI National & XXI International Congress of Geography 2015",
      city: "Santiago, Chile",
      lat: -33.4489,
      lng: -70.6693,
      year: 2015,
      title: "The forestry complex in Araucania region and the urban economy"
    },
    {
      name: "Chile-Japan Academic Forum 2014",
      city: "Tokyo, Japan",
      lat: 35.7128,
      lng: 139.7620,
      year: 2014,
      title: "The impact of woodchips: corporate territories and Chile-Japan trade"
    },
    {
      name: "Japanese Society of Latin America Social Science 2013",
      city: "Tokyo, Japan",
      lat: 35.6895,
      lng: 139.6917,
      year: 2013,
      title: "The Chilean wood, paper, and pulp industry – structures and linkages to Japan"
    },
    {
      name: "XXXIV National & XIX International Congress of Geography 2013",
      city: "Chillán, Chile",
      lat: -36.6066,
      lng: -72.1034,
      year: 2013,
      title: "Foreign trade with China and domestic employment of Chilean regions"
    },
    {
      name: "AAG Annual Meeting 2013",
      city: "Los Angeles, USA",
      lat: 34.0522,
      lng: -118.2437,
      year: 2013,
      title: "Resource-based economies and booming Asia: regional impacts in Chile"
    },
    {
      name: "XXXIII National & XVIII International Congress of Geography 2012",
      city: "Arica, Chile",
      lat: -18.4783,
      lng: -70.3126,
      year: 2012,
      title: "The two sides of the copper coin"
    },
    {
      name: "XI Geography Students Meeting 2011",
      city: "Santiago, Chile",
      lat: -33.4372,
      lng: -70.6506,
      year: 2011,
      title: "Health vulnerability in Manila and pollution in the river Pasig"
    }
  ];

  // Color scale by year
  function getColor(year) {
    if (year >= 2025) return "#e63946";  // red - upcoming/recent
    if (year >= 2022) return "#457b9d";  // blue - recent
    if (year >= 2018) return "#2a9d8f";  // teal - mid career
    if (year >= 2014) return "#e9c46a";  // gold - early career
    return "#8d99ae";                     // grey - earliest
  }

  // Add markers
  conferences.forEach(function (conf) {
    var color = getColor(conf.year);
    var marker = L.circleMarker([conf.lat, conf.lng], {
      radius: 8,
      fillColor: color,
      color: "#fff",
      weight: 2,
      opacity: 1,
      fillOpacity: 0.85
    }).addTo(map);

    marker.bindPopup(
      "<strong>" + conf.name + "</strong><br>" +
      "<em>" + conf.city + " (" + conf.year + ")</em><br><br>" +
      conf.title
    );
  });

  // Add a legend
  var legend = L.control({ position: "bottomright" });
  legend.onAdd = function () {
    var div = L.DomUtil.create("div", "legend");
    div.style.background = "white";
    div.style.padding = "10px 14px";
    div.style.borderRadius = "6px";
    div.style.boxShadow = "0 1px 4px rgba(0,0,0,0.3)";
    div.style.lineHeight = "1.8";
    div.style.fontSize = "13px";
    div.innerHTML =
      "<strong>Period</strong><br>" +
      '<i style="background:#e63946;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px;"></i> 2025–2026<br>' +
      '<i style="background:#457b9d;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px;"></i> 2022–2024<br>' +
      '<i style="background:#2a9d8f;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px;"></i> 2018–2021<br>' +
      '<i style="background:#e9c46a;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px;"></i> 2014–2017<br>' +
      '<i style="background:#8d99ae;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px;"></i> 2011–2013';
    return div;
  };
  legend.addTo(map);
});
</script>

### Conference presentations by region

**Americas:** Santiago, Chillán, Arica, Antofagasta (Chile) ·
Washington DC, Los Angeles, Honolulu, Worcester (USA)

**Europe:** London (UK) · Cologne (Germany) · Budapest (Hungary)

**Asia:** Tokyo, Kyoto (Japan)