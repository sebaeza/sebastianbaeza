---
layout: page
title: conferences
permalink: /conferences/
description: A map of conferences I have participated in.
nav: true
nav_order: 5
---

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
     integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
     crossorigin=""/>

<div id="conference-map" style="height: 600px; width: 100%; border-radius: 8px; z-index: 1; margin-bottom: 20px;"></div>

<div class="conferences-list">
  <h3>Conference List</h3>
  <ul>
    {% for conf in site.data.conferences %}
    <li>
      <strong>{{ conf.year }}</strong>: {{ conf.title }} - <em>{{ conf.location }}</em><br>
      <span style="font-size: 0.9em; color: #666;">{{ conf.paper }}</span>
    </li>
    {% endfor %}
  </ul>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
     integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
     crossorigin=""></script>

<script>
    // Initialize the map centered roughly on the Atlantic to show Americas and Europe/Asia
    var map = L.map('conference-map').setView([20, 0], 2);

    // Add OpenStreetMap tiles
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom: 18,
        attribution: '© OpenStreetMap'
    }).addTo(map);

    // Loop through _data/conferences.yml
    {% for conf in site.data.conferences %}
        L.marker([{{ conf.latitude }}, {{ conf.longitude }}]).addTo(map)
            .bindPopup("<b>{{ conf.title }}</b> ({{ conf.year }})<br>{{ conf.location }}<br><em>{{ conf.paper }}</em>");
    {% endfor %}
</script>