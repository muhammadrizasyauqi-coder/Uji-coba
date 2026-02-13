<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suruh Akudus - Jasa & PPOB</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root {
            --primary: #007bff; /* Biru Logo Kamu */
            --primary-dark: #0056b3;
            --success: #25D366;
            --card-bg: #ffffff;
            --text-color: #333333;
            --body-bg: #e7f1ff; /* Background biru muda tipis */
            --input-bg: #ffffff;
            --input-border: #ccd5e0;
        }

        /* Dark Mode Colors (Tetap mempertahankan aksen biru) */
        .dark-theme {
            --card-bg: #1a222d;
            --text-color: #f0f5ff;
            --body-bg: #0d1117;
            --input-bg: #242c38;
            --input-border: #38444d;
        }

        body { font-family: 'Segoe UI', Arial, sans-serif; background-color: var(--body-bg); color: var(--text-color); padding: 15px; margin: 0; transition: 0.5s; }
        .card { max-width: 450px; margin: auto; background: var(--card-bg); padding: 20px; border-radius: 20px; box-shadow: 0 8px 30px rgba(0,123,255,0.15); transition: 0.5s; }
        
        h2 { text-align: center; color: var(--primary); margin: 0; text-transform: uppercase; letter-spacing: 1px; }
        .subtitle { text-align: center; font-size: 13px; opacity: 0.8; margin-bottom: 15px; }

        .tabs { display: flex; gap: 10px; margin-bottom: 20px; background: rgba(0,123,255,0.1); padding: 5px; border-radius: 12px; }
        .tab-btn { flex: 1; padding: 10px; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; background: none; color: var(--text-color); }
        .tab-btn.active { background: var(--primary); color: white; }

        label { display: block; margin-top: 15px; font-weight: bold; font-size: 14px; }
        select, input, textarea { width: 100%; padding: 12px; margin-top: 6px; border: 1px solid var(--input-border); border-radius: 10px; box-sizing: border-box; font-size: 15px; background: var(--input-bg); color: var(--text-color); }
        
        .btn-lokasi { background: none; border: none; color: var(--primary); font-size: 12px; cursor: pointer; padding: 5px 0; font-weight: bold; }
        .search-results { background: var(--input-bg); border: 1px solid var(--input-border); position: absolute; z-index: 1000; width: 100%; max-height: 150px; overflow-y: auto; display: none; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
        .search-item { padding: 10px; cursor: pointer; border-bottom: 1px solid var(--input-border); font-size: 13px; color: var(--text-color); }

        .box-tarif { background: rgba(0, 123, 255, 0.05); padding: 15px; border-radius: 12px; margin-top: 25px; text-align: center; border: 2px solid var(--primary); }
        .total-harga { font-size: 32px; font-weight: bold; color: var(--primary); display: block; }

        /* Review Section */
        .review-section { margin-top: 30px; padding-top: 20px; border-top: 2px dashed var(--input-border); }
        .review-form { background: rgba(0,123,255,0.03); padding: 15px; border-radius: 12px; margin-bottom: 15px; border: 1px solid var(--input-border); }
        .review-list { max-height: 200px; overflow-y: auto; }
        .review-item { padding: 12px; border-bottom: 1px solid var(--input-border); background: rgba(255,255,255,0.02); }
        .rev-name { font-weight: bold; color: var(--primary); font-size: 14px; }
        .rev-text { font-size: 13px; font-style: italic; margin-top: 3px; }
        
        #loading-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); z-index: 10000; flex-direction: column; align-items: center; justify-content: center; color: white; }
        .spinner { width: 45px; height: 45px; border: 4px solid rgba(255,255,255,0.3); border-top: 4px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        #btn-pesan { width: 100%; padding: 18px; background-color: var(--success); color: white; border: none; border-radius: 12px; margin-top: 20px; font-size: 16px; cursor: pointer; font-weight: bold; box-shadow: 0 4px 15px rgba(37, 211, 102, 0.3); }
        .hidden { display: none; }
    </style>
</head>
<body>

<div id="loading-overlay">
    <div class="spinner"></div>
    <p style="margin-top: 15px; font-weight: 500;" id="loading-text">Sedang memproses rute...</p>
</div>

<div class="card">
    <h2>SURUH AKUDUS</h2>
    <p class="subtitle" id="time-greeting">Layanan Jasa & Kurir Kudus</p>

    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('jasa')">🛵 Jasa</button>
        <button class="tab-btn" onclick="switchTab('ppob')">💳 PPOB</button>
    </div>

    <div id="tab-jasa">
        <label>Pilih Layanan:</label>
        <select id="layanan" onchange="hitungTarif()">
            <option value="Ojek" data-harga="2500">🛵 Ojek (Rp2.500/km)</option>
            <option value="Kurir" data-harga="2000">📦 Kurir Barang (Rp2.000/km)</option>
            <option value="Jastip" data-harga="2000">🛒 Jastip Belanja (Rp2.000/km)</option>
        </select>

        <div style="position: relative;">
            <label>Lokasi Penjemputan:</label>
            <input type="text" id="jemput" placeholder="Ketik nama desa jemput..." oninput="cariAlamat('jemput')">
            <button type="button" class="btn-lokasi" onclick="ambilLokasiSaya()">📍 Gunakan Lokasi Saya Sekarang</button>
            <div id="res-jemput" class="search-results"></div>
        </div>

        <div style="position: relative;">
            <label>Lokasi Tujuan:</label>
            <input type="text" id="tujuan" placeholder="Ketik nama desa tujuan..." oninput="cariAlamat('tujuan')">
            <div id="res-tujuan" class="search-results"></div>
        </div>

        <div class="box-tarif">
            <span style="font-size: 13px; font-weight: bold;">Estimasi Total Tarif:</span>
            <span class="total-harga" id="tampilan-tarif">Rp0</span>
            <small style="opacity: 0.8; font-weight: bold;">Minimal Tarif Rp8.000</small>
        </div>
        <button id="btn-pesan" onclick="prosesPesan()">PESAN SEKARANG VIA WA</button>
    </div>

    <div id="tab-ppob" class="hidden">
        <div style="background: linear-gradient(135deg, var(--primary), #00c6ff); color: white; padding: 30px; border-radius: 15px; cursor: pointer; text-align: center; box-shadow: 0 4px 15px rgba(0,123,255,0.3);" onclick="window.open('https://link.speedcash.co.id/shopwarehouse', '_blank')">
            <h4 style="margin:0; font-size:20px;">⚡ PULSA & PPOB</h4>
            <p style="font-size:12px; margin-top:10px; opacity:0.9;">Klik di sini untuk isi Pulsa, Paket Data, & Token PLN otomatis 24 Jam.</p>
        </div>
    </div>

    <div class="review-section">
        <h4 style="text-align:center; margin-bottom:12px; color:var(--primary)">Review Pelanggan</h4>
        <div class="review-form">
            <input type="text" id="rev-nama" placeholder="Nama Anda (Wajib)" required>
            <textarea id="rev-pesan" rows="2" placeholder="Tulis pengalaman Anda..."></textarea>
            <button onclick="simpanReview()" style="width:100%; margin-top:10px; padding:12px; background:var(--primary); color:white; border:none; border-radius:8px; cursor:pointer; font-weight:bold;">Kirim Review</button>
        </div>

        <div id="review-list" class="review-list">
            </div>
    </div>
</div>

<script>
    // 1. TEMA & GREETING (SMART COLOR)
    const jam = new Date().getHours();
    if (jam >= 18 || jam < 6) document.body.classList.add('dark-theme');
    document.getElementById('time-greeting').innerText = (jam >= 18 || jam < 6) ? "Selamat Malam, Lur!" : "Selamat Siang, Lur!";

    // 2. LOGIKA MAPS & TARIF (OSM & OSRM)
    let locJemput = null, locTujuan = null, jarakFinal = 0, searchTimeout = null;

    function switchTab(t) {
        document.getElementById('tab-jasa').classList.toggle('hidden', t !== 'jasa');
        document.getElementById('tab-ppob').classList.toggle('hidden', t !== 'ppob');
        document.querySelectorAll('.tab-btn').forEach((b, i) => b.classList.toggle('active', (i===0 && t==='jasa') || (i===1 && t==='ppob')));
    }

    async function cariAlamat(type) {
        clearTimeout(searchTimeout);
        const query = document.getElementById(type).value;
        const resDiv = document.getElementById('res-' + type);
        if (query.length < 3) return;
        
        searchTimeout = setTimeout(async () => {
            const response = await fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${query}+Kudus&limit=5`);
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
        const res = await fetch(url);
        const data = await res.json();
        if (data.routes && data.routes[0]) {
            jarakFinal = (data.routes[0].distance / 1000).toFixed(1);
            hitungTarif();
        }
    }

    function hitungTarif() {
        const harga = document.getElementById('layanan').selectedOptions[0].getAttribute('data-harga');
        let jarakNum = parseFloat(jarakFinal);
        // LOGIKA BARU: Wajib 8rb jika dibawah 2km
        let total = (jarakNum <= 2) ? 8000 : (jarakNum * harga);
        document.getElementById('tampilan-tarif').innerText = "Rp" + Math.round(total).toLocaleString('id-ID');
    }

    function prosesPesan() {
        if (jarakFinal <= 0) return alert("Tentukan lokasi jemput dan tujuan dulu, Lur!");
        
        // Animasi Loading
        const t = ["Mencari driver terdekat...", "Menghitung rute tercepat...", "Sabar nggih, sekedap...", "Menghubungkan ke WhatsApp..."];
        document.getElementById('loading-text').innerText = t[Math.floor(Math.random() * t.length)];
        document.getElementById('loading-overlay').style.display = 'flex';

        setTimeout(() => {
            const teks = encodeURIComponent(`*ORDER SURUH AKUDUS*\nLayanan: ${document.getElementById('layanan').value}\nJemput: ${document.getElementById('jemput').value}\nTujuan: ${document.getElementById('tujuan').value}\nJarak: ${jarakFinal} KM\nTotal: ${document.getElementById('tampilan-tarif').innerText}`);
            window.open(`https://wa.me/6285124569347?text=${teks}`);
            document.getElementById('loading-overlay').style.display = 'none';
        }, 2000);
    }

    // 3. LOGIKA REVIEW (LOCALSTORAGE)
    function simpanReview() {
        const nama = document.getElementById('rev-nama').value;
        const pesan = document.getElementById('rev-pesan').value;
        if (!nama || !pesan) return alert("Nama dan Pesan wajib diisi!");

        const reviewBaru = { nama, pesan, tgl: new Date().toLocaleDateString('id-ID') };
        let reviews = JSON.parse(localStorage.getItem('suruh_reviews')) || [];
        reviews.unshift(reviewBaru);
        localStorage.setItem('suruh_reviews', JSON.stringify(reviews));

        document.getElementById('rev-nama').value = '';
        document.getElementById('rev-pesan').value = '';
        tampilkanReview();
    }

    function tampilkanReview() {
        const list = document.getElementById('review-list');
        let reviews = JSON.parse(localStorage.getItem('suruh_reviews')) || [
            {nama: "Mas Agus", pesan: "Pelayanan mantap, ojeke sat set tekan tujuan!", tgl: "13/02/2024"},
            {nama: "Mbak Siti", pesan: "Jastip paling murah se-Kudus, 8rb wes tekan omah.", tgl: "12/02/2024"}
        ];

        list.innerHTML = reviews.map(r => `
            <div class="review-item">
                <div class="rev-name">${r.nama} <small style="font-weight:normal; color:#888; font-size:10px;">• ${r.tgl}</small></div>
                <div class="rev-text">"${r.pesan}"</div>
            </div>
        `).join('');
    }
    tampilkanReview();
</script>
</body>
</html>
