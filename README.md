# Pemetaan-Cabang-ALKES
Aplikasi web pemetaan cabang alkes berbasis Leaflet yang menampilkan data lokasi dan persentase distribusi, dengan pembaruan data melalui file Excel.
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Pemetaan Persentase Kabupaten/Kota</title>

  <!-- Leaflet -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

  <!-- SheetJS -->
  <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>

  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
    }
    #map {
      height: 90vh;
      width: 100%;
    }
    .controls {
      padding: 10px;
      background: #f5f5f5;
    }
  </style>
</head>
<body>

<div class="controls">
  <input type="file" id="upload" accept=".xlsx,.xls">
  <span>Upload file Excel</span>
</div>

<div id="map"></div>

<script>
  // Inisialisasi peta
  const map = L.map('map').setView([-6.9, 107.6], 8);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap'
  }).addTo(map);

  let markersLayer = L.layerGroup().addTo(map);

  document.getElementById('upload').addEventListener('change', function(e) {
    const file = e.target.files[0];
    if (!file) return;

    const reader = new FileReader();

    reader.onload = function(evt) {
      const data = new Uint8Array(evt.target.result);
      const workbook = XLSX.read(data, { type: 'array' });

      const sheet = workbook.Sheets[workbook.SheetNames[0]];
      const rows = XLSX.utils.sheet_to_json(sheet);

      markersLayer.clearLayers();

      rows.forEach(row => {
        const lat = parseFloat(row.Latitude);
        const lng = parseFloat(row.Longitude);
        const persen = row.Persentase;
        const nama = row["Kabupaten/Kota"];

        if (!isNaN(lat) && !isNaN(lng)) {
          const marker = L.circleMarker([lat, lng], {
            radius: 10,
            color: 'red',
            fillColor: 'red',
            fillOpacity: 0.6
          }).addTo(markersLayer);

          marker.bindPopup(
            `<b>${nama}</b><br>Persentase: ${persen}%`
          );
        }
      });
    };

    reader.readAsArrayBuffer(file);
  });
</script>

</body>
</html>
