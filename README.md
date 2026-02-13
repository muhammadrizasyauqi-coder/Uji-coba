<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Suruh Akudus - Jasa & PPOB</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root {
            --primary: #0056b3;
            --success: #25D366;
            --card-bg: #ffffff;
            --text-color: #333333;
            --body-bg: #f0f2f5;
            --input-bg: #ffffff;
            --input-border: #dddddd;
            --review-bg: #f9f9f9;
        }

        .dark-theme {
            --card-bg: #1e1e1e;
            --text-color: #f0f0f0;
            --body-bg: #121212;
            --input-bg: #2d2d2d;
            --input-border: #444444;
            --review-bg: #2d2d2d;
        }

        body { font-family: 'Segoe UI', Arial, sans-serif; background-color: var(--body-bg); color: var(--text-color); padding: 15px; margin: 0; transition: 0.5s; }
        .card { max-width: 450px; margin: auto; background: var(--card-bg); padding: 20px; border-radius: 20px; box-shadow: 0 4px 20px rgba(0,0,0,0.2); position: relative; }
        
        h2 { text-align: center; color: var(--primary); margin: 0; }
        .subtitle { text-align: center; font-size: 13px; opacity: 0.8; margin-bottom: 15px; }

        .tabs { display: flex; gap: 10px; margin-bottom: 20px; background: rgba(0,0,0,0.05); padding: 5px; border-radius: 12px; }
        .tab-btn { flex: 1; padding: 10px; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; background: none; color: var(--text-color); }
        .tab-btn.active { background: var(--primary); color: white; }

        label { display: block; margin-top: 15px; font-weight: bold; font-size: 14px; }
        select, input, textarea { width: 100%; padding: 12px; margin-top: 6px; border: 1px solid var(--input-border); border-radius: 10px; box-sizing: border-box; font-size: 15px; background: var(--input-bg); color: var(--text-color); }
        
        .box-tarif { background: rgba(37, 211, 102, 0.1); padding: 15px; border-radius: 12px; margin-top: 25px; text-align: center; border: 2px solid var(--success); }
        .total-harga { font-size: 30px; font-weight: bold; color: var(--success); display: block; }

        /* Review Section */
        .review-section { margin-top: 30px; padding-top: 20px; border-top: 1px solid var(--input-border); }
        .review-container { background: var(--review-bg); padding: 15px; border-radius: 12px; position: relative; min-height: 80px; overflow: hidden; }
        .review-slide { display: none; text-align: center; animation: fadeIn 0.5s; }
        .review-slide.active { display: block; }
        .stars { color: #ffc107; font-size: 14px; margin-bottom: 5px; }
        .review-text { font-style: italic; font-size: 13px; }
        .review-author { font-weight: bold; font-size: 12px; margin-top: 5px; display: block; color: var(--primary); }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Loading Animation */
        #loading-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 10000; flex-direction: column; align-items: center; justify-content: center; color: white; }
        .spinner { width: 50px; height: 50px; border: 5px solid #f3f3f3; border-top: 5px solid var(--success); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        #btn-pesan { width: 100%; padding: 16px; background-color: var(--success); color: white; border: none; border-radius: 12px; margin-top: 20px; font-size: 16px; cursor: pointer; font-weight: bold; }
        .hidden { display: none; }
    </style>
</head>
<body>

<div id="loading-overlay">
    <div class="spinner"></div>
    <p style="margin-top: 15px; font-weight: bold;" id="loading-text">Sekedap nggih...</p>
</div>

<div class="card">
    <h2>SURUH AKUDUS</h2>
    <p class="subtitle" id="time-greeting">Cah Kudus Asli!</p>

    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('jasa')">🛵 Jasa</button>
        <button class="tab-btn" onclick="switchTab('ppob')">💳 PPOB</button>
    </div>

    <div id="tab-jasa">
        <label>Layanan:</label>
        <select id="layanan" onchange="hitungTarif()">
            <option value="Ojek" data-harga="2500">🛵 Ojek (Rp2.500/km)</option>
            <option value="Kurir" data-harga="2000">📦 Kurir Barang (Rp2.000/km)</option>
            <option value="Jastip" data-harga="2000">🛒 Jastip Belanja (Rp2.000/km)</option>
        </select>

        <input type="text" id="jemput" placeholder="Desa Jemput..." oninput="cariAlamat('jemput')" style="margin-top:15px;">
        <input type="text" id="tujuan" placeholder="Desa Tujuan..." oninput="cariAlamat('tujuan')" style="margin-top:10px;">

        <div class="box-tarif">
            <span style="font-size: 13px;">Estimasi Total:</span>
            <span class="total-harga" id="tampilan-tarif">Rp0</span>
            <small style="opacity: 0.7;">Minimal Rp8.000</small>
        </div>
        <button id="btn-pesan" onclick="prosesPesan()">PESAN SEKARANG</button>
    </div>

    <div id="tab-ppob" class="hidden">
        <div style="background: linear-gradient(45deg, #6f42c1, #0056b3); color: white; padding: 25px; border-radius: 15px; cursor: pointer; text-align: center;" onclick="window.open('https://link.speedcash.co.id/shopwarehouse', '_blank')">
            <h4>⚡ PULSA & PPOB</h4>
            <p style="font-size:12px">Klik untuk isi Pulsa/Token otomatis</p>
        </div>
    </div>

    <div class="review-section">
        <p style="font-size: 12px; font-weight: bold; margin-bottom: 10px; text-align: center;">Apa Kata Mereka?</p>
        <div class="review-container">
            <div class="review-slide active">
                <div class="stars">★★★★★</div>
                <div class="review-text">"Paling cepet nek kon jastip sego jangkrik. Amanah tenan!"</div>
                <span class="review-author">- Mas Agus, Wergu</span>
            </div>
            <div class="review-slide">
                <div class="stars">★★★★★</div>
                <div class="review-text">"Drivere sopan, hafal dalan tikus Kudus. Gak telat."</div>
                <span class="review-author">- Mbak Siti, Jati</span>
            </div>
            <div class="review-slide">
                <div class="stars">★★★★★</div>
                <div class="review-text">"Isi pulsa neng kene langsung mlebu. Mantap!"</div>
                <span class="review-author">- Kang Dul, Mejobo</span>
            </div>
        </div>
    </div>
</div>

<script>
    // Theme & Greeting
    const jam = new Date().getHours();
    if (jam >= 18 || jam < 6) document.body.classList.add('dark-theme');
    document.getElementById('time-greeting').innerText = (jam >= 18 || jam < 6) ? "Selamat Malam, Lur!" : "Selamat Siang, Lur!";

    // Review Slider Logic
    let currentSlide = 0;
    const slides = document.querySelectorAll('.review-slide');
    setInterval(() => {
        slides[currentSlide].classList.remove('active');
        currentSlide = (currentSlide + 1) % slides.length;
        slides[currentSlide].classList.add('active');
    }, 4000);

    // Tab & Loading
    function switchTab(t) {
        document.getElementById('tab-jasa').classList.toggle('hidden', t !== 'jasa');
        document.getElementById('tab-ppob').classList.toggle('hidden', t !== 'ppob');
        document.querySelectorAll('.tab-btn').forEach((b, i) => b.classList.toggle('active', (i===0 && t==='jasa') || (i===1 && t==='ppob')));
    }

    function showLoading() {
        const t = ["Sabar nggih...", "Lagi proses lur...", "Niki nembe diproses...", "Ojo lali mampir Menara..."];
        document.getElementById('loading-text').innerText = t[Math.floor(Math.random() * t.length)];
        document.getElementById('loading-overlay').style.display = 'flex';
    }

    // Tarif & Pesan (Simple Version for Demo)
    let jarakFinal = 2.5; // Contoh jarak
    function hitungTarif() {
        const h = document.getElementById('layanan').selectedOptions[0].getAttribute('data-harga');
        let total = (jarakFinal <= 2) ? 8000 : (jarakFinal * h);
        document.getElementById('tampilan-tarif').innerText = "Rp" + Math.round(total).toLocaleString('id-ID');
    }
    hitungTarif();

    function prosesPesan() {
        showLoading();
        setTimeout(() => {
            window.open(`https://wa.me/6285124569347?text=Halo Suruh Akudus!`);
            document.getElementById('loading-overlay').style.display = 'none';
        }, 1500);
    }
</script>
</body>
</html>
