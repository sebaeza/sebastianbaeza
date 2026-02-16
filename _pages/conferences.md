---
layout: page
title: conferences
permalink: /conferences/
description: An interactive map of my conference presentations worldwide
nav: true
nav_order: 6
map: true
---

Over the years, I have presented my research at conferences across the globe — from Santiago to Tokyo, London to Honolulu. This map shows the locations of my academic presentations.

<style>
  #conference-map {
    height: 500px;
    width: 100%;
    border-radius: 8px;
    margin: 20px 0;
    z-index: 0;
    position: relative;
  }
  /* Fix Leaflet tile rendering broken by Bootstrap/al-folio styles */
  #conference-map .leaflet-tile-pane img {
    max-width: none !important;
    max-height: none !important;
    border-radius: 0 !important;
    box-shadow: none !important;
    padding: 0 !important;
    margin: 0 !important;
  }
  #conference-map .leaflet-container img {
    max-width: none !important;
    max-height: none !important;
  }
</style>

<div id="conference-map"></div>

<script>
document.addEventListener("DOMContentLoaded", function () {
  var map = L.map('conference-map', {
    scrollWheelZoom: false
  }).setView([20, 0], 2);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors',
    maxZoom: 18
  }).addTo(map);

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
      title: "Does the periphery matter across technological trajectories? Examining different technologies in the Japanese case"
    },
    {
      name: "Global Conference on Economic Geography 2025 — Panel",
      city: "Worcester, USA",
      lat: 42.2700,
      lng: -71.7950,
      year: 2025,
      title: "Engaged pluralism with diversity for the future of economic geography (Panel: EG at 100)"
    },
    {
      name: "Japan Assoc. of Economic Geographers 2025",
      city: "Tokyo, Japan",
      lat: 35.6762,
      lng: 139.6503,
      year: 2025,
      title: "Technological trajectories in core and periphery settings: comparing knowledge bases"
    },
    {
      name: "AAG Annual Meeting 2024",
      city: "Honolulu, USA",
      lat: 21.3069,
      lng: -157.8583,
      year: 2024,
      title: "Unequal geographies of technological change: the structural and relational dimensions of specialization in Chilean salmon farming"
    },
    {
      name: "SOCHER 2022",
      city: "Antofagasta, Chile",
      lat: -23.6509,
      lng: -70.3975,
      year: 2022,
      title: "Putting the State back in: Neostructural innovation in the Chilean salmon industry"
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
      title: "Affective Labour and the business of making games: video games development in the periphery"
    },
    {
      name: "Global Conference on Economic Geography 2018",
      city: "Cologne, Germany",
      lat: 50.9375,
      lng: 6.9603,
      year: 2018,
      title: "Playing with the south: dependency in the video games software development in Chile"
    },
    {
      name: "RGS-IBG Annual Conference 2017",
      city: "London, UK",
      lat: 51.5074,
      lng: -0.1278,
      year: 2017,
      title: "Gaming with the south? Cultural economy and the global production networks of videogames in Chile"
    },
    {
      name: "XXXVI National & XXI International Congress of Geography 2015",
      city: "Santiago, Chile",
      lat: -33.4489,
      lng: -70.6693,
      year: 2015,
      title: "The forestry complex in Araucania region and its relation with the urban economy"
    },
    {
      name: "Chile-Japan Academic Forum 2014",
      city: "Tokyo, Japan",
      lat: 35.7128,
      lng: 139.7620,
      year: 2014,
      title: "The impact of woodchips: corporate territories and the benefits of trade between Chile and Japan in the forestry sector"
    },
    {
      name: "Chile-Japan Academic Forum 2014 (2nd presentation)",
      city: "Tokyo, Japan",
      lat: 35.7200,
      lng: 139.7550,
      year: 2014,
      title: "The urban and regional challenges of mining in the world's driest desert: the Antofagasta case"
    },
    {
      name: "Japanese Society of Latin America Social Science 2013",
      city: "Tokyo, Japan",
      lat: 35.6895,
      lng: 139.6917,
      year: 2013,
      title: "The Chilean wood, paper, and pulp industry – structures and linkages to Japan and global markets"
    },
    {
      name: "XXXIV National & XIX International Congress of Geography 2013",
      city: "Chillán, Chile",
      lat: -36.6066,
      lng: -72.1034,
      year: 2013,
      title: "The effect of foreign trade with China in the domestic employment of Chilean regions"
    },
    {
      name: "Colloquium in Governance of Risks and Resources 2013",
      city: "Santiago, Chile",
      lat: -33.4400,
      lng: -70.6400,
      year: 2013,
      title: "The impact of woodchips: unequal trade relationship with Japan in Southern Chile"
    },
    {
      name: "AAG Annual Meeting 2013",
      city: "Los Angeles, USA",
      lat: 34.0522,
      lng: -118.2437,
      year: 2013,
      title: "Resource-based economies and booming Asia: regional impacts in export-driven Chile"
    },
    {
      name: "XXXIII National & XVIII International Congress of Geography 2012",
      city: "Arica, Chile",
      lat: -18.4783,
      lng: -70.3126,
      year: 2012,
      title: "The two sides of the copper coin: Dynamics and specialization exports of Chilean regions"
    },
    {
      name: "XI Geography Students Meeting 2011",
      city: "Santiago, Chile",
      lat: -33.4372,
      lng: -70.6506,
      year: 2011,
      title: "Analysis of health vulnerability in Manila, Philippines and its relation to pollution levels in the river Pasig"
    },
    {
      name: "II Asian and African Studies Meeting 2011",
      city: "Santiago, Chile",
      lat: -33.4450,
      lng: -70.6600,
      year: 2011,
      title: "Water pollution and health vulnerability in Manila, Philippines"
    }
  ];

  function getColor(year) {
    if (year >= 2025) return "#e63946";
    if (year >= 2022) return "#457b9d";
    if (year >= 2018) return "#2a9d8f";
    if (year >= 2014) return "#e9c46a";
    return "#8d99ae";
  }

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

  // Force Leaflet to recalculate tile positions after layout settles
  setTimeout(function () {
    map.invalidateSize();
  }, 200);
});
</script>

### Conference presentations by region

**Americas:** Santiago, Chillán, Arica, Antofagasta (Chile) · Washington DC, Los Angeles, Honolulu, Worcester (USA)

**Europe:** London (UK) · Cologne (Germany) · Budapest (Hungary)

**Asia:** Tokyo, Kyoto (Japan)