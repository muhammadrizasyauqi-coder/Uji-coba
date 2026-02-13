<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suruh Akudus - Layanan Profesional</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root {
            --primary: #007bff;
            --primary-dark: #0056b3;
            --success: #28a745;
            --card-bg: #ffffff;
            --text-color: #2d3436;
            --body-bg: #f0f7ff;
            --input-bg: #ffffff;
            --input-border: #dfe6e9;
        }

        .dark-theme {
            --card-bg: #1e272e;
            --text-color: #f5f6fa;
            --body-bg: #0f1418;
            --input-bg: #2f3640;
            --input-border: #57606f;
        }

        body { 
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; 
            background-color: var(--body-bg); 
            color: var(--text-color); 
            padding: 20px 15px; 
            margin: 0; 
            transition: all 0.5s ease;
        }

        .card { 
            max-width: 480px; 
            margin: auto; 
            background: var(--card-bg); 
            padding: 30px; 
            border-radius: 24px; 
            box-shadow: 0 15px 35px rgba(0,123,255,0.1); 
        }

        /* Desain Logo */
        .logo-container { text-align: center; margin-bottom: 20px; }
        .logo-img { 
            width: 100px; 
            height: 100px; 
            border-radius: 50%; 
            border: 4px solid var(--primary); 
            padding: 5px;
            background: white;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        h2 { text-align: center; color: var(--primary); margin: 10px 0 5px 0; font-size: 24px; letter-spacing: 1px; }
        .subtitle { text-align: center; font-size: 14px; opacity: 0.7; margin-bottom: 25px; font-weight: 500; }

        .tabs { display: flex; gap: 10px; margin-bottom: 25px; background: rgba(0,123,255,0.05); padding: 6px; border-radius: 14px; }
        .tab-btn { flex: 1; padding: 12px; border: none; border-radius: 10px; cursor: pointer; font-weight: 600; background: none; color: var(--text-color); transition: 0.3s; }
        .tab-btn.active { background: var(--primary); color: white; box-shadow: 0 4px 12px rgba(0,123,255,0.3); }

        label { display: block; margin-top: 15px; font-weight: 600; font-size: 14px; color: var(--primary); }
        select, input, textarea { 
            width: 100%; padding: 14px; margin-top: 8px; border: 1.5px solid var(--input-border); 
            border-radius: 12px; box-sizing: border-box; font-size: 15px; 
            background: var(--input-bg); color: var(--text-color); transition: 0.3s;
        }
        input:focus { border-color: var(--primary); outline: none; box-shadow: 0 0 0 3px rgba(0,123,255,0.1); }

        .btn-lokasi { background: none; border: none; color: var(--primary); font-size: 12px; cursor: pointer; padding: 8px 0; font-weight: bold; display: block; }
        
        .box-tarif { 
            background: linear-gradient(135deg, rgba(0,123,255,0.05), rgba(0,123,255,0.1)); 
            padding: 20px; border-radius: 16px; margin-top: 25px; text-align: center; 
            border: 1px solid var(--primary); 
        }
        .total-harga { font-size: 36px; font-weight: 800; color: var(--primary); display: block; margin: 5px 0; }
        
        /* Modal Peringatan */
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); display: flex; align-items: center; justify-content: center; z-index: 10000; }
        .modal-box { background: var(--card-bg); padding: 30px; border-radius: 24px; max-width: 320px; text-align: center; }

        /* Ulasan Section */
        .review-section { margin-top: 35px; padding-top: 25px; border-top: 2px solid var(--input-border); }
        .review-form { background: rgba(0,123,255,0.03); padding: 20px; border-radius: 16px; margin-bottom: 20px; border: 1px solid var(--input-border); }
        .review-item { padding: 15px; border-bottom: 1px solid var(--input-border); }
        .rev-name { font-weight: bold; color: var(--primary); }

        #btn-pesan { 
            width: 100%; padding: 18px; background-color: var(--success); color: white; border: none; 
            border-radius: 14px; margin-top: 20px; font-size: 16px; cursor: pointer; font-weight: bold; 
            box-shadow: 0 6px 20px rgba(40, 167, 69, 0.3); transition: 0.3s;
        }
        #btn-pesan:active { transform: scale(0.98); }

        #loading-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 20000; flex-direction: column; align-items: center; justify-content: center; color: white; }
        .spinner { width: 50px; height: 50px; border: 5px solid #f3f3f3; border-top: 5px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        
        .search-results { background: var(--input-bg); border: 1px solid var(--input-border); position: absolute; z-index: 1000; width: 100%; max-height: 150px; overflow-y: auto; display: none; border-radius: 12px; margin-top: 5px; }
        .search-item { padding: 12px; cursor: pointer; border-bottom: 1px solid var(--input-border); font-size: 13px; }
        .hidden { display: none; }
    </style>
</head>
<body>

<div id="modalWelcome" class="modal-overlay">
    <div class="modal-box">
        <h3 style="color:var(--primary); margin-top:0;">Pemberitahuan</h3>
        <p style="font-size: 14px; line-height: 1.6;">Mohon mengetik nama <b>Desa</b> secara perlahan dan tidak terlalu cepat agar sistem pencarian dapat berfungsi secara optimal.</p>
        <button onclick="closeModal()" style="width:100%; padding:12px; background:var(--primary); color:white; border:none; border-radius:12px; cursor:pointer; font-weight:bold;">Saya Mengerti</button>
    </div>
</div>

<div id="loading-overlay">
    <div class="spinner"></div>
    <p style="margin-top: 15px; font-weight: 600;">Sedang memproses...</p>
</div>

<div class="card">
    <div class="logo-container">
        <img src="https://i.ibb.co/XfXkY8S/2-LN9-Kw-Y.jpg" alt="Logo Suruh Akudus" class="logo-img">
    </div>
    
    <h2>SURUH AKUDUS</h2>
    <p class="subtitle" id="time-greeting">Layanan Transportasi & Kurir Terpercaya</p>

    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('jasa')">Layanan</button>
        <button class="tab-btn" onclick="switchTab('ppob')">PPOB</button>
    </div>

    <div id="tab-jasa">
        <label>Pilih Jenis Layanan:</label>
        <select id="layanan" onchange="hitungTarif()">
            <option value="Ojek" data-harga="2500">Ojek (Rp2.500/km)</option>
            <option value="Kurir" data-harga="2000">Kurir Barang (Rp2.000/km)</option>
            <option value="Jastip" data-harga="2000">Jasa Titip (Rp2.000/km)</option>
        </select>

        <div style="position: relative;">
            <label>Titik Penjemputan:</label>
            <input type="text" id="jemput" placeholder="Masukkan nama desa..." oninput="cariAlamat('jemput')">
            <button type="button" class="btn-lokasi" onclick="ambilLokasiSaya()">📍 Gunakan lokasi saya saat ini</button>
            <div id="res-jemput" class="search-results"></div>
        </div>

        <div style="position: relative;">
            <label>Titik Tujuan:</label>
            <input type="text" id="tujuan" placeholder="Masukkan nama desa..." oninput="cariAlamat('tujuan')">
            <div id="res-tujuan" class="search-results"></div>
        </div>

        <div class="box-tarif">
            <span style="font-size: 13px; font-weight: bold; text-transform: uppercase; color: var(--primary);">Estimasi Biaya</span>
            <span class="total-harga" id="tampilan-tarif">Rp0</span>
            <small style="font-weight: 600; color: var(--text-color); opacity: 0.6;">Tarif minimal Rp8.000 (0-2 km)</small>
        </div>
        <button id="btn-pesan" onclick="prosesPesan()">PESAN MELALUI WHATSAPP</button>
    </div>

    <div id="tab-ppob" class="hidden">
        <div style="background: linear-gradient(135deg, var(--primary), #00c6ff); color: white; padding: 30px; border-radius: 20px; cursor: pointer; text-align: center;" onclick="window.open('https://link.speedcash.co.id/shopwarehouse', '_blank')">
            <h4 style="margin:0; font-size: 20px;">TOP UP & PPOB</h4>
            <p style="font-size:12px; margin-top:10px; opacity:0.9;">Pengisian Pulsa, Token PLN, dan Paket Data otomatis 24 jam.</p>
        </div>
    </div>

    <div class="review-section">
        <h4 style="text-align:center; color: var(--primary); margin-bottom: 20px;">Ulasan Pelanggan</h4>
        <div class="review-form">
            <input type="text" id="rev-nama" placeholder="Nama Lengkap (Wajib)">
            <textarea id="rev-pesan" rows="2" placeholder="Berikan ulasan Anda..."></textarea>
            <button onclick="simpanReview()" style="width:100%; margin-top:12px; padding:12px; background:var(--primary); color:white; border:none; border-radius:10px; cursor:pointer; font-weight:bold;">Kirim Ulasan</button>
        </div>
        <div id="review-list" class="review-list"></div>
    </div>
</div>

<script>
    const jam = new Date().getHours();
    if (jam >= 18 || jam < 6) document.body.classList.add('dark-theme');
    document.getElementById('time-greeting').innerText = (jam >= 18 || jam < 6) ? "Selamat Malam!" : "Selamat Siang!";

    function closeModal() { document.getElementById('modalWelcome').style.display = 'none'; }
    
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
        }, 600);
    }

    async function ambilLokasiSaya() {
        if (!navigator.geolocation) return alert("Lokasi tidak didukung.");
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
        let j = parseFloat(jarakFinal);
        let total = (j <= 2) ? 8000 : (j * harga);
        document.getElementById('tampilan-tarif').innerText = "Rp" + Math.round(total).toLocaleString('id-ID');
    }

    function prosesPesan() {
        if (jarakFinal <= 0) return alert("Silakan tentukan rute terlebih dahulu.");
        document.getElementById('loading-overlay').style.display = 'flex';
        setTimeout(() => {
            const teks = encodeURIComponent(`*PESANAN SURUH AKUDUS*\nLayanan: ${document.getElementById('layanan').value}\nJemput: ${document.getElementById('jemput').value}\nTujuan: ${document.getElementById('tujuan').value}\nJarak: ${jarakFinal} KM\nTotal: ${document.getElementById('tampilan-tarif').innerText}`);
            window.open(`https://wa.me/6285124569347?text=${teks}`);
            document.getElementById('loading-overlay').style.display = 'none';
        }, 2000);
    }

    function simpanReview() {
        const nama = document.getElementById('rev-nama').value;
        const pesan = document.getElementById('rev-pesan').value;
        if (!nama || !pesan) return alert("Mohon isi nama dan ulasan.");
        const rev = { nama, pesan, tgl: new Date().toLocaleDateString('id-ID') };
        let reviews = JSON.parse(localStorage.getItem('suruh_reviews')) || [];
        reviews.unshift(rev);
        localStorage.setItem('suruh_reviews', JSON.stringify(reviews));
        document.getElementById('rev-nama').value = '';
        document.getElementById('rev-pesan').value = '';
        tampilkanReview();
    }

    function tampilkanReview() {
        const list = document.getElementById('review-list');
        let reviews = JSON.parse(localStorage.getItem('suruh_reviews')) || [
            {nama: "Budi Santoso", pesan: "Layanan sangat memuaskan dan pengemudi ramah.", tgl: "13/02/2026"},
            {nama: "Sari Pertiwi", pesan: "Jastip yang sangat membantu, barang sampai dengan aman.", tgl: "12/02/2026"}
        ];
        list.innerHTML = reviews.map(r => `
            <div class="review-item">
                <div class="rev-name">${r.nama} <small style="font-weight:normal; color:#888;">• ${r.tgl}</small></div>
                <div style="font-size:13px; margin-top:4px;">"${r.pesan}"</div>
            </div>
        `).join('');
    }
    tampilkanReview();
</script>
</body>
</html>
