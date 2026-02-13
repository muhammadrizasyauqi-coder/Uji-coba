<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suruh Akudus - Layanan & PPOB Kudus</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root { --primary: #0056b3; --success: #25D366; --bg: #f0f2f5; }
        body { font-family: 'Segoe UI', Arial, sans-serif; background-color: var(--bg); padding: 15px; margin: 0; }
        .card { max-width: 450px; margin: auto; background: white; padding: 20px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        
        /* Tab Menu */
        .tabs { display: flex; gap: 10px; margin-bottom: 20px; background: #eee; padding: 5px; border-radius: 10px; }
        .tab-btn { flex: 1; padding: 10px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; background: none; transition: 0.3s; }
        .tab-btn.active { background: white; color: var(--primary); box-shadow: 0 2px 5px rgba(0,0,0,0.1); }

        h2 { text-align: center; color: var(--primary); margin: 0; }
        .subtitle { text-align: center; font-size: 13px; color: #666; margin-bottom: 15px; }
        
        label { display: block; margin-top: 15px; font-weight: bold; font-size: 14px; }
        select, input, textarea { width: 100%; padding: 12px; margin-top: 6px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 15px; }
        
        .btn-lokasi { background: none; border: none; color: var(--primary); font-size: 12px; cursor: pointer; padding: 5px 0; font-weight: bold; }
        
        .search-results { background: white; border: 1px solid #ddd; position: absolute; z-index: 1000; width: 100%; max-height: 200px; overflow-y: auto; display: none; box-shadow: 0 4px 10px rgba(0,0,0,0.1); border-radius: 8px; }
        .search-item { padding: 10px; cursor: pointer; border-bottom: 1px solid #eee; font-size: 13px; }
        .search-item:hover { background: #f0f0f0; }

        .box-tarif { background: #eef9f1; padding: 15px; border-radius: 10px; margin-top: 25px; text-align: center; border: 2px solid var(--success); }
        .total-harga { font-size: 30px; font-weight: bold; color: var(--success); display: block; }
        
        /* PPOB Style */
        .ppob-container { text-align: center; padding: 10px 0; }
        .ppob-card { background: linear-gradient(135deg, #6f42c1, #0056b3); color: white; padding: 20px; border-radius: 12px; cursor: pointer; transition: 0.3s; margin-top: 10px; }
        .ppob-card:hover { transform: translateY(-3px); }
        .ppob-card h4 { margin: 0; font-size: 18px; }
        .ppob-card p { font-size: 12px; opacity: 0.9; margin: 5px 0 0; }

        #btn-pesan { width: 100%; padding: 16px; background-color: var(--success); color: white; border: none; border-radius: 10px; margin-top: 20px; font-size: 16px; cursor: pointer; font-weight: bold; }

        /* Modal */
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); display: flex; align-items: center; justify-content: center; z-index: 9999; }
        .modal-box { background: white; padding: 25px; border-radius: 20px; max-width: 300px; text-align: center; }
        .btn-close-modal { margin-top: 15px; padding: 10px 20px; background: var(--primary); color: white; border: none; border-radius: 8px; cursor: pointer; }

        .hidden { display: none; }
    </style>
</head>
<body>

<div id="modalWelcome" class="modal-overlay">
    <div class="modal-box">
        <h3 style="color:red">⚠️ Perhatian!</h3>
        <p style="font-size: 14px;">Ketik nama <b>Desa</b> pelan-pelan dan pilih dari saran yang muncul agar tarif akurat.</p>
        <button class="btn-close-modal" onclick="closeModal()">Oke, Lur!</button>
    </div>
</div>

<div class="card">
    <h2>SURUH AKUDUS</h2>
    <p class="subtitle">Cepat, Amanah, Cah Kudus Asli!</p>

    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('jasa')">🛵 Jasa & Kurir</button>
        <button class="tab-btn" onclick="switchTab('ppob')">💳 Pulsa & PPOB</button>
    </div>

    <div id="tab-jasa">
        <label>Pilih Layanan:</label>
        <select id="layanan" onchange="hitungTarif()">
            <option value="Ojek" data-harga="2500">🛵 Ojek (Rp2.500/km)</option>
            <option value="Kurir" data-harga="2000">📦 Kurir Barang (Rp2.000/km)</option>
            <option value="Jastip" data-harga="2000">🛒 Jastip Belanja (Rp2.000/km)</option>
        </select>

        <div style="position: relative;">
            <label>Desa Penjemputan:</label>
            <input type="text" id="jemput" placeholder="Ketik desa jemput..." oninput="cariAlamat('jemput')">
            <button type="button" class="btn-lokasi" onclick="ambilLokasiSaya()">📍 Gunakan Lokasi Saya</button>
            <div id="res-jemput" class="search-results"></div>
        </div>

        <div style="position: relative;">
            <label>Desa Tujuan:</label>
            <input type="text" id="tujuan" placeholder="Ketik desa tujuan..." oninput="cariAlamat('tujuan')">
            <div id="res-tujuan" class="search-results"></div>
        </div>

        <label>Jarak:</label>
        <input type="text" id="jarak-display" readonly placeholder="Otomatis..." style="background:#f9f9f9">

        <div class="box-tarif">
            <span style="font-size: 13px; font-weight: bold;">Estimasi Tarif:</span>
            <span class="total-harga" id="tampilan-tarif">Rp0</span>
            <small style="font-size: 10px; color: #666;">(Min. Rp5.000 untuk 0-2 KM)</small>
        </div>
        <button id="btn-pesan" onclick="prosesPesan()">PESAN VIA WHATSAPP</button>
    </div>

    <div id="tab-ppob" class="hidden">
        <div class="ppob-container">
            <p style="font-size: 14px; color: #444;">Beli Pulsa, Paket Data, Token PLN, & Voucher Game lebih murah di sini:</p>
            
            <div class="ppob-card" onclick="window.open('https://link.speedcash.co.id/shopwarehouse', '_blank')">
                <h4>🚀 TOP UP & PPOB</h4>
                <p>Klik untuk buka toko pulsa otomatis</p>
            </div>
            
            <p style="margin-top: 20px; font-size: 11px; color: #999;">Layanan ini bekerja sama dengan SpeedCash untuk transaksi otomatis 24 jam.</p>
        </div>
    </div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
    let locJemput = null, locTujuan = null, jarakFinal = 0, searchTimeout = null;

    function closeModal() { document.getElementById('modalWelcome').style.display = 'none'; }

    // Switch Tab Logic
    function switchTab(tab) {
        document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
        if(tab === 'jasa') {
            document.getElementById('tab-jasa').classList.remove('hidden');
            document.getElementById('tab-ppob').classList.add('hidden');
            document.querySelector('.tab-btn:nth-child(1)').classList.add('active');
        } else {
            document.getElementById('tab-jasa').classList.add('hidden');
            document.getElementById('tab-ppob').classList.remove('hidden');
            document.querySelector('.tab-btn:nth-child(2)').classList.add('active');
        }
    }

    function cariAlamat(type) {
        clearTimeout(searchTimeout);
        const query = document.getElementById(type).value;
        const resDiv = document.getElementById('res-' + type);
        if (query.length < 3) { resDiv.style.display = 'none'; return; }

        searchTimeout = setTimeout(async () => {
            try {
                const response = await fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${query}+Kudus+Indonesia&limit=5`);
                const data = await response.json();
                resDiv.innerHTML = '';
                resDiv.style.display = 'block';
                data.forEach(item => {
                    const div = document.createElement('div');
                    div.className = 'search-item';
                    div.innerText = item.display_name;
                    div.onclick = () => {
                        document.getElementById(type).value = item.display_name;
                        resDiv.style.display = 'none';
                        if (type === 'jemput') locJemput = {lat: item.lat, lon: item.lon};
                        else locTujuan = {lat: item.lat, lon: item.lon};
                        if (locJemput && locTujuan) hitungRute();
                    };
                    resDiv.appendChild(div);
                });
            } catch (e) { console.error(e); }
        }, 500);
    }

    async function ambilLokasiSaya() {
        if (!navigator.geolocation) return alert("GPS tidak didukung!");
        document.getElementById('jemput').value = "Mencari lokasi...";
        navigator.geolocation.getCurrentPosition(async (pos) => {
            locJemput = {lat: pos.coords.latitude, lon: pos.coords.longitude};
            const response = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${locJemput.lat}&lon=${locJemput.lon}`);
            const data = await response.json();
            document.getElementById('jemput').value = data.display_name;
            if (locTujuan) hitungRute();
        });
    }

    async function hitungRute() {
        const url = `https://router.project-osrm.org/route/v1/driving/${locJemput.lon},${locJemput.lat};${locTujuan.lon},${locTujuan.lat}?overview=false`;
        const response = await fetch(url);
        const data = await response.json();
        if (data.routes && data.routes[0]) {
            jarakFinal = (data.routes[0].distance / 1000).toFixed(1);
            document.getElementById('jarak-display').value = jarakFinal + " KM";
            hitungTarif();
        }
    }

    function hitungTarif() {
        const select = document.getElementById('layanan');
        const hargaPerKm = parseFloat(select.options[select.selectedIndex].getAttribute('data-harga'));
        let jarakNum = parseFloat(jarakFinal);
        let total = (jarakNum <= 2) ? 5000 : (jarakNum * hargaPerKm);
        document.getElementById('tampilan-tarif').innerText = "Rp" + Math.round(total).toLocaleString('id-ID');
    }

    function prosesPesan() {
        if (jarakFinal <= 0) return alert("Pilih desa dulu!");
        const teks = window.encodeURIComponent(`*ORDER SURUH AKUDUS*\nLayanan: ${document.getElementById('layanan').value}\nJemput: ${document.getElementById('jemput').value}\nTujuan: ${document.getElementById('tujuan').value}\nJarak: ${jarakFinal} KM\nTotal: ${document.getElementById('tampilan-tarif').innerText}`);
        window.open(`https://wa.me/6285124569347?text=${teks}`);
    }
</script>
</body>
</html>
