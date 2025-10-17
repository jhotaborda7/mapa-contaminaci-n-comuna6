# mapa-contaminaci-n-comuna6
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mapa de Contaminación - Comuna 6 Doce de Octubre</title>
    
    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px;
            text-align: center;
            box-shadow: 0 2px 10px rgba(0,0,0,0.2);
        }

        header h1 {
            font-size: 24px;
            margin-bottom: 5px;
        }

        header p {
            font-size: 14px;
            opacity: 0.9;
        }

        .container {
            display: flex;
            flex: 1;
            overflow: hidden;
        }

        .sidebar {
            width: 300px;
            background: #f8f9fa;
            padding: 20px;
            overflow-y: auto;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
        }

        .sidebar h3 {
            color: #333;
            margin-bottom: 15px;
            font-size: 18px;
        }

        .filter-section {
            margin-bottom: 25px;
        }

        .filter-option {
            display: flex;
            align-items: center;
            margin: 10px 0;
            padding: 8px;
            border-radius: 5px;
            transition: background 0.3s;
        }

        .filter-option:hover {
            background: #e9ecef;
        }

        .filter-option input {
            margin-right: 10px;
        }

        .color-indicator {
            width: 15px;
            height: 15px;
            border-radius: 50%;
            margin-right: 8px;
        }

        .stats-box {
            background: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            margin-bottom: 15px;
        }

        .stat-item {
            display: flex;
            justify-content: space-between;
            margin: 8px 0;
            padding: 5px 0;
            border-bottom: 1px solid #eee;
        }

        .stat-label {
            font-weight: 600;
            color: #555;
        }

        .stat-value {
            color: #667eea;
            font-weight: bold;
        }

        .btn {
            width: 100%;
            padding: 12px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 14px;
            font-weight: 600;
            transition: all 0.3s;
            margin-top: 10px;
        }

        .btn-primary {
            background: #667eea;
            color: white;
        }

        .btn-primary:hover {
            background: #5568d3;
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(102, 126, 234, 0.3);
        }

        .btn-secondary {
            background: #28a745;
            color: white;
        }

        .btn-secondary:hover {
            background: #218838;
        }

        #map {
            flex: 1;
            height: 100%;
        }

        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
        }

        .modal-content {
            background: white;
            margin: 5% auto;
            padding: 30px;
            border-radius: 10px;
            width: 90%;
            max-width: 500px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.3);
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .modal-header h2 {
            color: #333;
        }

        .close {
            font-size: 28px;
            font-weight: bold;
            color: #aaa;
            cursor: pointer;
            transition: color 0.3s;
        }

        .close:hover {
            color: #000;
        }

        .form-group {
            margin-bottom: 15px;
        }

        .form-group label {
            display: block;
            margin-bottom: 5px;
            color: #555;
            font-weight: 600;
        }

        .form-group input,
        .form-group select,
        .form-group textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 14px;
        }

        .form-group textarea {
            resize: vertical;
            min-height: 80px;
        }

        .legend {
            background: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        .legend h4 {
            margin-bottom: 10px;
            color: #333;
        }

        .legend-item {
            display: flex;
            align-items: center;
            margin: 8px 0;
        }

        @media (max-width: 768px) {
            .container {
                flex-direction: column;
            }
            
            .sidebar {
                width: 100%;
                max-height: 300px;
            }
        }
    </style>
</head>
<body>
    <header>
        <h1>🗺️ Mapa de Contaminación por Residuos Sólidos</h1>
        <p>Comuna 6 - Doce de Octubre, Medellín</p>
    </header>

    <div class="container">
        <aside class="sidebar">
            <div class="filter-section">
                <h3>🎯 Filtros</h3>
                <div class="filter-option">
                    <input type="checkbox" id="filter-critico" checked>
                    <span class="color-indicator" style="background: #dc3545;"></span>
                    <label for="filter-critico">Crítico</label>
                </div>
                <div class="filter-option">
                    <input type="checkbox" id="filter-alto" checked>
                    <span class="color-indicator" style="background: #fd7e14;"></span>
                    <label for="filter-alto">Alto</label>
                </div>
                <div class="filter-option">
                    <input type="checkbox" id="filter-medio" checked>
                    <span class="color-indicator" style="background: #ffc107;"></span>
                    <label for="filter-medio">Medio</label>
                </div>
                <div class="filter-option">
                    <input type="checkbox" id="filter-bajo" checked>
                    <span class="color-indicator" style="background: #28a745;"></span>
                    <label for="filter-bajo">Bajo</label>
                </div>
            </div>

            <div class="stats-box">
                <h3>📊 Estadísticas</h3>
                <div class="stat-item">
                    <span class="stat-label">Total Puntos:</span>
                    <span class="stat-value" id="total-points">0</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Críticos:</span>
                    <span class="stat-value" id="critico-count">0</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Altos:</span>
                    <span class="stat-value" id="alto-count">0</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Medios:</span>
                    <span class="stat-value" id="medio-count">0</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Bajos:</span>
                    <span class="stat-value" id="bajo-count">0</span>
                </div>
            </div>

            <button class="btn btn-secondary" onclick="exportData()">📥 Exportar Datos</button>
            <button class="btn btn-primary" onclick="clearAllMarkers()">🗑️ Limpiar Mapa</button>
        </aside>

        <div id="map"></div>
    </div>

    <!-- Modal para agregar punto -->
    <div id="reportModal" class="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>📍 Reportar Punto de Contaminación</h2>
                <span class="close" onclick="closeModal()">&times;</span>
            </div>
            <form id="reportForm">
                <div class="form-group">
                    <label>Dirección o Referencia:</label>
                    <input type="text" id="direccion" required>
                </div>
                <div class="form-group">
                    <label>Nivel de Contaminación:</label>
                    <select id="nivel" required>
                        <option value="critico">🔴 Crítico</option>
                        <option value="alto">🟠 Alto</option>
                        <option value="medio">🟡 Medio</option>
                        <option value="bajo">🟢 Bajo</option>
                    </select>
                </div>
                <div class="form-group">
                    <label>Tipo de Residuos:</label>
                    <select id="tipo-residuo">
                        <option value="mixtos">Mixtos</option>
                        <option value="organicos">Orgánicos</option>
                        <option value="plasticos">Plásticos</option>
                        <option value="escombros">Escombros</option>
                        <option value="electronicos">Electrónicos</option>
                    </select>
                </div>
                <div class="form-group">
                    <label>Descripción:</label>
                    <textarea id="descripcion"></textarea>
                </div>
                <div class="form-group">
                    <label>Nombre (Opcional):</label>
                    <input type="text" id="reportante">
                </div>
                <button type="submit" class="btn btn-primary">Guardar Punto</button>
            </form>
        </div>
    </div>

    <!-- Leaflet JS -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    
    <script>
        // Inicializar mapa centrado en Comuna 6 Doce de Octubre
        const map = L.map('map').setView([6.2476, -75.6058], 14);

        // Capa base de OpenStreetMap
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // Almacenar marcadores
        let markers = [];
        let currentLatLng = null;

        // Colores por nivel
        const colors = {
            'critico': '#dc3545',
            'alto': '#fd7e14',
            'medio': '#ffc107',
            'bajo': '#28a745'
        };

        // Agregar leyenda al mapa
        const legend = L.control({position: 'bottomright'});
        legend.onAdd = function(map) {
            const div = L.DomUtil.create('div', 'legend');
            div.innerHTML = `
                <h4>Nivel de Contaminación</h4>
                <div class="legend-item">
                    <span class="color-indicator" style="background: #dc3545;"></span>
                    Crítico
                </div>
                <div class="legend-item">
                    <span class="color-indicator" style="background: #fd7e14;"></span>
                    Alto
                </div>
                <div class="legend-item">
                    <span class="color-indicator" style="background: #ffc107;"></span>
                    Medio
                </div>
                <div class="legend-item">
                    <span class="color-indicator" style="background: #28a745;"></span>
                    Bajo
                </div>
            `;
            return div;
        };
        legend.addTo(map);

        // Evento click en el mapa
        map.on('click', function(e) {
            currentLatLng = e.latlng;
            document.getElementById('reportModal').style.display = 'block';
        });

        // Cerrar modal
        function closeModal() {
            document.getElementById('reportModal').style.display = 'none';
            document.getElementById('reportForm').reset();
        }

        // Enviar formulario
        document.getElementById('reportForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const data = {
                lat: currentLatLng.lat,
                lng: currentLatLng.lng,
                direccion: document.getElementById('direccion').value,
                nivel: document.getElementById('nivel').value,
                tipoResiduo: document.getElementById('tipo-residuo').value,
                descripcion: document.getElementById('descripcion').value,
                reportante: document.getElementById('reportante').value,
                fecha: new Date().toLocaleDateString('es-CO')
            };

            addMarker(data);
            closeModal();
            updateStats();
        });

        // Agregar marcador
        function addMarker(data) {
            const customIcon = L.divIcon({
                className: 'custom-marker',
                html: `<div style="background-color: ${colors[data.nivel]}; width: 25px; height: 25px; border-radius: 50%; border: 3px solid white; box-shadow: 0 2px 5px rgba(0,0,0,0.3);"></div>`,
                iconSize: [25, 25]
            });

            const marker = L.marker([data.lat, data.lng], {icon: customIcon})
                .addTo(map)
                .bindPopup(`
                    <div style="min-width: 200px;">
                        <h3 style="margin-bottom: 10px; color: ${colors[data.nivel]};">
                            ${data.nivel.toUpperCase()}
                        </h3>
                        <p><strong>📍 Dirección:</strong> ${data.direccion}</p>
                        <p><strong>🗑️ Tipo:</strong> ${data.tipoResiduo}</p>
                        <p><strong>📅 Fecha:</strong> ${data.fecha}</p>
                        ${data.descripcion ? `<p><strong>📝 Descripción:</strong> ${data.descripcion}</p>` : ''}
                        ${data.reportante ? `<p><strong>👤 Reportado por:</strong> ${data.reportante}</p>` : ''}
                        <button onclick="removeMarker(${markers.length})" style="margin-top: 10px; padding: 5px 10px; background: #dc3545; color: white; border: none; border-radius: 3px; cursor: pointer;">Eliminar</button>
                    </div>
                `);

            markers.push({marker: marker, data: data});
            
            // Guardar en localStorage
            saveToLocalStorage();
        }

        // Eliminar marcador
        function removeMarker(index) {
            if (markers[index]) {
                map.removeLayer(markers[index].marker);
                markers.splice(index, 1);
                updateStats();
                saveToLocalStorage();
            }
        }

        // Limpiar todos los marcadores
        function clearAllMarkers() {
            if (confirm('¿Estás seguro de eliminar todos los puntos?')) {
                markers.forEach(item => map.removeLayer(item.marker));
                markers = [];
                updateStats();
                localStorage.removeItem('contaminacionData');
            }
        }

        // Actualizar estadísticas
        function updateStats() {
            const stats = {
                total: markers.length,
                critico: markers.filter(m => m.data.nivel === 'critico').length,
                alto: markers.filter(m => m.data.nivel === 'alto').length,
                medio: markers.filter(m => m.data.nivel === 'medio').length,
                bajo: markers.filter(m => m.data.nivel === 'bajo').length
            };

            document.getElementById('total-points').textContent = stats.total;
            document.getElementById('critico-count').textContent = stats.critico;
            document.getElementById('alto-count').textContent = stats.alto;
            document.getElementById('medio-count').textContent = stats.medio;
            document.getElementById('bajo-count').textContent = stats.bajo;
        }

        // Filtros
        ['critico', 'alto', 'medio', 'bajo'].forEach(nivel => {
            document.getElementById(`filter-${nivel}`).addEventListener('change', function() {
                markers.forEach(item => {
                    if (item.data.nivel === nivel) {
                        if (this.checked) {
                            map.addLayer(item.marker);
                        } else {
                            map.removeLayer(item.marker);
                        }
                    }
                });
            });
        });

        // Guardar en localStorage
        function saveToLocalStorage() {
            const data = markers.map(item => item.data);
            localStorage.setItem('contaminacionData', JSON.stringify(data));
        }

        // Cargar desde localStorage
        function loadFromLocalStorage() {
            const data = localStorage.getItem('contaminacionData');
            if (data) {
                JSON.parse(data).forEach(item => addMarker(item));
                updateStats();
            }
        }

        // Exportar datos
        function exportData() {
            if (markers.length === 0) {
                alert('No hay datos para exportar');
                return;
            }

            let csv = 'Latitud,Longitud,Dirección,Nivel,Tipo de Residuo,Descripción,Reportante,Fecha\n';
            
            markers.forEach(item => {
                const d = item.data;
                csv += `${d.lat},${d.lng},"${d.direccion}","${d.nivel}","${d.tipoResiduo}","${d.descripcion || ''}","${d.reportante || ''}","${d.fecha}"\n`;
            });

            const blob = new Blob([csv], { type: 'text/csv' });
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `contaminacion_comuna6_${new Date().toISOString().split('T'),[object Object],}.csv`;
            a.click();
        }

        // Cargar datos al iniciar
        loadFromLocalStorage();

        // Cerrar modal al hacer clic fuera
        window.onclick = function(event) {
            const modal = document.getElementById('reportModal');
            if (event.target == modal) {
                closeModal();
            }
        }    
    </script>
</body>
</html>
