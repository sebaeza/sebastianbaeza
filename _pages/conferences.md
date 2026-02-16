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

<div id="conference-map" style="height: 500px; width: 100%; position: relative; z-index: 0;"></div>

<script>
// Wait for Leaflet to be available (it loads via defer)
function initConferenceMap() {
  if (typeof L === "undefined") {
    setTimeout(initConferenceMap, 100);
    return;
  }

  var mapEl = document.getElementById("conference-map");
  if (!mapEl) return;

  // Fix: inject CSS override LAST so it wins over Bootstrap/MDB
  var s = document.createElement("style");
  s.textContent =
    ".leaflet-container img { max-width:none!important; max-height:none!important; width:auto!important; height:auto!important; padding:0!important; margin:0!important; border-radius:0!important; box-shadow:none!important; border:none!important; transition:none!important; }";
  document.head.appendChild(s);

  var map = L.map(mapEl, { scrollWheelZoom: false }).setView([20, 0], 2);

  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
  }).addTo(map);

  setTimeout(function () { map.invalidateSize(); }, 400);

  var conferences = [
    { n:"GeoInno 2026", c:"Budapest, Hungary", la:47.4979, ln:19.0402, y:2026, t:"Unveiling the network core and periphery structures of technological development in salmon aquaculture" },
    { n:"Global Conference on Economic Geography 2025", c:"Worcester, USA", la:42.2626, ln:-71.8023, y:2025, t:"Does the periphery matter across technological trajectories?" },
    { n:"GCEG 2025 Panel", c:"Worcester, USA", la:42.27, ln:-71.795, y:2025, t:"Engaged pluralism with diversity for the future of economic geography (Panel: EG at 100)" },
    { n:"Japan Assoc. of Economic Geographers 2025", c:"Tokyo, Japan", la:35.6762, ln:139.6503, y:2025, t:"Technological trajectories in core and periphery settings: comparing knowledge bases" },
    { n:"AAG Annual Meeting 2024", c:"Honolulu, USA", la:21.3069, ln:-157.8583, y:2024, t:"Unequal geographies of technological change in Chilean salmon farming" },
    { n:"SOCHER 2022", c:"Antofagasta, Chile", la:-23.6509, ln:-70.3975, y:2022, t:"Putting the State back in: Neostructural innovation in the Chilean salmon industry" },
    { n:"DiGRA 2019", c:"Kyoto, Japan", la:35.0116, ln:135.7681, y:2019, t:"Video Games Production in the periphery: the Chilean case" },
    { n:"AAG Annual Meeting 2019", c:"Washington DC, USA", la:38.9072, ln:-77.0369, y:2019, t:"Affective Labour and the business of making games" },
    { n:"Global Conference on Economic Geography 2018", c:"Cologne, Germany", la:50.9375, ln:6.9603, y:2018, t:"Playing with the south: dependency in video games development in Chile" },
    { n:"RGS-IBG Annual Conference 2017", c:"London, UK", la:51.5074, ln:-0.1278, y:2017, t:"Gaming with the south? Cultural economy and GPNs of videogames in Chile" },
    { n:"XXXVI National Congress of Geography 2015", c:"Santiago, Chile", la:-33.4489, ln:-70.6693, y:2015, t:"The forestry complex in Araucania region and the urban economy" },
    { n:"Chile-Japan Academic Forum 2014", c:"Tokyo, Japan", la:35.7128, ln:139.762, y:2014, t:"The impact of woodchips: corporate territories in Chile-Japan forestry trade" },
    { n:"Chile-Japan Academic Forum 2014 (2nd)", c:"Tokyo, Japan", la:35.72, ln:139.755, y:2014, t:"Urban and regional challenges of mining in the Atacama Desert" },
    { n:"Japanese Society of Latin America 2013", c:"Tokyo, Japan", la:35.6895, ln:139.6917, y:2013, t:"The Chilean wood, paper, and pulp industry linkages to Japan" },
    { n:"XXXIV National Congress of Geography 2013", c:"Chillan, Chile", la:-36.6066, ln:-72.1034, y:2013, t:"Foreign trade with China and domestic employment in Chilean regions" },
    { n:"Colloquium Governance of Risks 2013", c:"Santiago, Chile", la:-33.44, ln:-70.64, y:2013, t:"The impact of woodchips: unequal trade with Japan in Southern Chile" },
    { n:"AAG Annual Meeting 2013", c:"Los Angeles, USA", la:34.0522, ln:-118.2437, y:2013, t:"Resource-based economies and booming Asia: regional impacts in Chile" },
    { n:"XXXIII National Congress of Geography 2012", c:"Arica, Chile", la:-18.4783, ln:-70.3126, y:2012, t:"The two sides of the copper coin" },
    { n:"XI Geography Students Meeting 2011", c:"Santiago, Chile", la:-33.4372, ln:-70.6506, y:2011, t:"Health vulnerability in Manila and pollution in the Pasig river" },
    { n:"II Asian and African Studies Meeting 2011", c:"Santiago, Chile", la:-33.445, ln:-70.66, y:2011, t:"Water pollution and health vulnerability in Manila, Philippines" }
  ];

  function color(y) {
    if (y >= 2025) return "#e63946";
    if (y >= 2022) return "#457b9d";
    if (y >= 2018) return "#2a9d8f";
    if (y >= 2014) return "#e9c46a";
    return "#8d99ae";
  }

  conferences.forEach(function (d) {
    L.circleMarker([d.la, d.ln], {
      radius: 8, fillColor: color(d.y), color: "#fff",
      weight: 2, opacity: 1, fillOpacity: 0.85
    }).addTo(map).bindPopup(
      "<strong>" + d.n + "</strong><br><em>" + d.c + " (" + d.y + ")</em><br><br>" + d.t
    );
  });

  var legend = L.control({ position: "bottomright" });
  legend.onAdd = function () {
    var div = L.DomUtil.create("div");
    div.style.cssText = "background:white;padding:10px 14px;border-radius:6px;box-shadow:0 1px 4px rgba(0,0,0,0.3);line-height:1.8;font-size:13px;";
    div.innerHTML = "<strong>Period</strong><br>" +
      '<i style="background:#e63946;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px"></i> 2025-2026<br>' +
      '<i style="background:#457b9d;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px"></i> 2022-2024<br>' +
      '<i style="background:#2a9d8f;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px"></i> 2018-2021<br>' +
      '<i style="background:#e9c46a;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px"></i> 2014-2017<br>' +
      '<i style="background:#8d99ae;width:12px;height:12px;display:inline-block;border-radius:50%;margin-right:6px"></i> 2011-2013';
    return div;
  };
  legend.addTo(map);
}

initConferenceMap();
</script>

### Conference presentations by region

**Americas:** Santiago, Chillán, Arica, Antofagasta (Chile) · Washington DC, Los Angeles, Honolulu, Worcester (USA)

**Europe:** London (UK) · Cologne (Germany) · Budapest (Hungary)

**Asia:** Tokyo, Kyoto (Japan)