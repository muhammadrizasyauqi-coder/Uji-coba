<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suruh Akudus - OpenStreetMap Version</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        body { font-family: 'Segoe UI', Arial, sans-serif; background-color: #f0f2f5; padding: 15px; margin: 0; }
        .card { max-width: 450px; margin: auto; background: white; padding: 25px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h2 { text-align: center; color: #0056b3; margin-top: 0; }
        label { display: block; margin-top: 15px; font-weight: bold; font-size: 14px; }
        input, select, textarea { width: 100%; padding: 12px; margin-top: 6px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        /* Style untuk dropdown hasil pencarian */
        .search-results { background: white; border: 1px solid #ddd; position: absolute; z-index: 1000; width: 380px; max-height: 200px; overflow-y: auto; display: none; }
        .search-item { padding: 10px; cursor: pointer; border-bottom: 1px solid #eee; font-size: 13px; }
        .search-item:hover { background: #f0f0f0; }

        .box-tarif { background: #f8f9fa; padding: 15px; border-radius: 10px; margin-top: 20px; text-align: center; border: 1px dashed #0056b3; }
        .total-harga { font-size: 26px; font-weight: bold; color: #28a745; display: block; }
        
        #btn-pesan { width: 100%; padding: 16px; background-color: #25D366; color: white; border: none; border-radius: 10px; margin-top: 20px; font-size: 16px; cursor: pointer; font-weight: bold; }
        .hidden { display: none; }
    </style>
</head>
<body>

<div class="card">
    <h2>SURUH AKUDUS</h2>
    <p style="text-align:center; font-size:12px; color:#666;">Cepat, Amanah, Gratis Ongkir (Eh Gak Deng!)</p>
    
    <label>Pilih Layanan:</label>
    <select id="layanan" onchange="hitungTarif()">
        <option value="Ojek" data-harga="3500">🛵 Ojek (Rp3.500/km)</option>
        <option value="Kurir" data-harga="3000">📦 Kurir Barang (Rp3.000/km)</option>
        <option value="Jastip" data-harga="2500">🛒 Jastip Belanja (Rp2.500/km)</option>
    </select>

    <div style="position: relative;">
        <label>Titik Penjemputan:</label>
        <input type="text" id="jemput" placeholder="Cari lokasi jemput..." oninput="cariAlamat('jemput')">
        <div id="res-jemput" class="search-results"></div>
    </div>

    <div style="position: relative;">
        <label>Titik Tujuan:</label>
        <input type="text" id="tujuan" placeholder="Cari lokasi tujuan..." oninput="cariAlamat('tujuan')">
        <div id="res-tujuan" class="search-results"></div>
    </div>

    <label>Estimasi Jarak:</label>
    <input type="text" id="jarak-display" readonly placeholder="Otomatis terhitung..." style="background:#eee">

    <div class="box-tarif">
        <span style="font-size: 13px;">Estimasi Total Tarif:</span>
        <span class="total-harga" id="tampilan-tarif">Rp0</span>
    </div>

    <button id="btn-pesan" onclick="prosesPesan()">PESAN VIA WHATSAPP</button>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<script>
    let locJemput = null;
    let locTujuan = null;
    let jarakFinal = 0;

    // Fungsi cari alamat pake Nominatim (OSM)
    async function cariAlamat(type) {
        const query = document.getElementById(type).value;
        const resDiv = document.getElementById('res-' + type);
        
        if (query.length < 3) {
            resDiv.style.display = 'none';
            return;
        }

        const response = await fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${query}+Kudus&limit=5`);
        const data = await response.json();

        resDiv.innerHTML = '';
        resDiv.style.display = 'block';

        data.forEach(item => {
            const div = document.createElement('div');
            div.className = 'search-item';
            div.innerText = item.display_name;
            div.onclick = () => pilihAlamat(type, item.lat, item.lon, item.display_name);
            resDiv.appendChild(div);
        });
    }

    function pilihAlamat(type, lat, lon, name) {
        document.getElementById(type).value = name;
        document.getElementById('res-' + type).style.display = 'none';
        
        if (type === 'jemput') locJemput = {lat, lon};
        else locTujuan = {lat, lon};

        if (locJemput && locTujuan) hitungRute();
    }

    // Hitung jarak asli jalan raya pake OSRM
    async function hitungRute() {
        const url = `https://router.project-osrm.org/route/v1/driving/${locJemput.lon},${locJemput.lat};${locTujuan.lon},${locTujuan.lat}?overview=false`;
        
        try {
            const response = await fetch(url);
            const data = await response.json();
            
            if (data.routes && data.routes[0]) {
                jarakFinal = (data.routes[0].distance / 1000).toFixed(1);
                document.getElementById('jarak-display').value = jarakFinal + " KM";
                hitungTarif();
            }
        } catch (e) {
            alert("Gagal menghitung jarak, coba lagi.");
        }
    }

    function hitungTarif() {
        const select = document.getElementById('layanan');
        const hargaPerKm = select.options[select.selectedIndex].getAttribute('data-harga');
        let total = jarakFinal * hargaPerKm;
        
        if (total > 0 && total < 8000) total = 8000;
        
        document.getElementById('tampilan-tarif').innerText = "Rp" + Math.round(total).toLocaleString('id-ID');
    }

    function prosesPesan() {
        const tarif = document.getElementById('tampilan-tarif').innerText;
        if (jarakFinal == 0) return alert("Pilih alamat dulu, Lur!");
        
        const teks = window.encodeURIComponent(`*ORDER SURUH AKUDUS*\nLayanan: ${document.getElementById('layanan').value}\nJemput: ${document.getElementById('jemput').value}\nTujuan: ${document.getElementById('tujuan').value}\nJarak: ${jarakFinal} KM\nTotal: ${tarif}`);
        window.open(`https://wa.me/6285124569347?text=${teks}`);
    }
</script>

</body>
</html>
