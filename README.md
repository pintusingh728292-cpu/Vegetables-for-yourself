<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>किसान बाज़ार - ताज़ा स्टोर</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --primary: #1b4332; --success: #2d6a4f; --warning: #f59e0b; --danger: #ef4444; --light: #f8faf9; --white: #ffffff; }
        body { font-family: 'Inter', sans-serif; background: var(--light); margin: 0; padding-bottom: 180px; scroll-behavior: smooth; }
        header { background: linear-gradient(135deg, var(--primary), var(--success)); color: white; padding: 40px 20px; text-align: left; border-radius: 0 0 40px 40px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); }
        header h1 { margin: 0; font-size: 32px; font-weight: 900; }
        .container { padding: 15px; max-width: 600px; margin: auto; }

        .glass-card { background: var(--white); border-radius: 25px; padding: 20px; margin-top: -50px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); }
        input, textarea { width: 100%; border: 1.5px solid #edf2f7; padding: 14px; border-radius: 15px; margin-bottom: 12px; box-sizing: border-box; outline: none; }

        .badge { position: absolute; top: 12px; left: 12px; font-size: 9px; font-weight: 800; padding: 4px 10px; border-radius: 10px; text-transform: uppercase; z-index: 5; }
        .st-ok { background: #dcfce7; color: #166534; }
        .st-low { background: #fef3c7; color: #92400e; animation: blink 1.5s infinite; }
        .st-out { background: #fee2e2; color: #991b1b; }
        @keyframes blink { 50% { opacity: 0.5; } }

        .product-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 25px; }
        .item-card { background: var(--white); border-radius: 25px; padding: 12px; position: relative; box-shadow: 0 4px 15px rgba(0,0,0,0.02); border: 1px solid #f0f0f0; }
        .item-card img { width: 100%; height: 110px; object-fit: cover; border-radius: 20px; background: #eee; }
        
        /* ग्राम बटन स्टाइल */
        .g-btn { flex: 1; font-size: 10px; padding: 6px 2px; border: 1px solid #e2e8f0; border-radius: 8px; background: #fff; cursor: pointer; font-weight: bold; }
        .g-btn:active { background: var(--success); color: white; }

        .qty-box { display: flex; align-items: center; justify-content: space-between; background: #f1f5f9; border-radius: 15px; padding: 4px; margin-top: 8px; }
        .btn-q { border: none; background: var(--white); width: 32px; height: 32px; border-radius: 10px; cursor: pointer; font-size: 18px; font-weight: bold; color: var(--success); }

        .bill-card { background: #fff; border-radius: 25px; padding: 25px; margin-top: 30px; border: 2px solid var(--success); }
        .float-btn { position: fixed; bottom: 100px; right: 20px; background: var(--success); color: white; padding: 18px 28px; border-radius: 35px; border: none; font-weight: bold; display: none; z-index: 1000; box-shadow: 0 10px 20px rgba(0,0,0,0.2); }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: grid; grid-template-columns: 1fr 1fr; border-top: 1px solid #f1f5f9; z-index: 1001; }
        .nav-btn { padding: 20px; text-align: center; font-weight: bold; text-decoration: none; color: #64748b; }
    </style>
</head>
<body>

<header>
    <h1>🌿 किसान बाज़ार</h1>
    <p style="margin:5px 0 0 0; opacity: 0.8; font-size: 14px;">20+ ताज़ा सब्जियां एवं फल</p>
</header>

<div class="container">
    <div class="glass-card">
        <input type="text" id="name" placeholder="आपका नाम" oninput="save()">
        <textarea id="addr" placeholder="पूरा पता..." rows="2" oninput="save()"></textarea>
        <input type="text" id="search" style="background:#f1f5f9; border:none;" placeholder="🔍 सब्जी का नाम खोजें..." onkeyup="draw()">
    </div>

    <div class="product-grid" id="menu"></div>

    <div class="bill-card" id="checkout">
        <h3 style="margin-top:0;">📋 आपका बिल</h3>
        <div id="list" style="margin-bottom:15px; color:#4a5568; font-size: 14px;">थैला खाली है...</div>
        <div style="display:flex; justify-content:space-between; font-size:22px; font-weight:900; color:var(--primary); padding-top:10px; border-top: 1px solid #eee;">
            <span>कुल:</span> <span>₹<span id="total">0</span></span>
        </div>
        <button onclick="order()" style="width:100%; background:#25D366; color:white; border:none; padding:18px; border-radius:20px; font-size:18px; font-weight:900; margin-top:20px; cursor:pointer;">WhatsApp पर भेजें ➔</button>
    </div>
</div>

<button class="float-btn" id="jump" onclick="document.getElementById('checkout').scrollIntoView({behavior:'smooth'})">🛒 बिल देखें (<span id="count">0</span>)</button>

<div class="bottom-nav">
    <a href="tel:+91XXXXXXXXXX" class="nav-btn">📞 सहायता</a>
    <button class="nav-btn" style="background:#1a1a1a; color:white; border:none;" onclick="alert('पिन: 5566')">👤 स्टाफ</button>
</div>

<script>
    const WA_NUM = "91XXXXXXXXXX"; 

    let db = [
        { id: 1, name: "टमाटर", price: 30, stock: 40, unit: "kg", img: "https://cdn.pixabay.com/photo/2011/03/16/16/01/tomatoes-5356_1280.jpg" },
        { id: 2, name: "आलू", price: 25, stock: 100, unit: "kg", img: "https://images.unsplash.com/photo-1518977676601-b53f82aba655?w=200" },
        { id: 3, name: "प्याज", price: 50, stock: 40, unit: "kg", img: "https://images.unsplash.com/photo-1508747703725-719777637510?w=200" },
        { id: 4, name: "खीरा", price: 40, stock: 20, unit: "kg", img: "https://images.unsplash.com/photo-1568584711271-6c929fb49b60?w=300" },
        { id: 5, name: "भिंडी", price: 40, stock: 15, unit: "kg", img: "https://images.unsplash.com/photo-1446071103245-96c88219a3a7?w=200" },
        { id: 6, name: "बैंगन", price: 40, stock: 20, unit: "kg", img: "https://images.unsplash.com/photo-1615484477778-ca3b77940c25?w=200" },
        { id: 7, name: "लौकी", price: 30, stock: 10, unit: "kg", img: "https://images.unsplash.com/photo-1603048588665-791ca8aea617?w=200" },
        { id: 8, name: "मटर", price: 60, stock: 30, unit: "kg", img: "https://images.unsplash.com/photo-1592394533824-9440e5d68530?w=300" },
        { id: 9, name: "शिमला मिर्च", price: 80, stock: 6, unit: "kg", img: "https://images.unsplash.com/photo-1592484224522-680c85f769d6?w=200" },
        { id: 10, name: "पत्ता गोभी", price: 40, stock: 15, unit: "kg", img: "https://images.unsplash.com/photo-1594282486552-05b4d80fbb9f?w=200" },
        { id: 11, name: "गाजर", price: 40, stock: 25, unit: "kg", img: "https://images.unsplash.com/photo-1598170845058-32b9d6a5da37?w=200" },
        { id: 12, name: "मूली", price: 20, stock: 20, unit: "kg", img: "https://images.unsplash.com/photo-1592336637380-49669528d97e?w=200" },
        { id: 13, name: "हरी मिर्च", price: 80, stock: 10, unit: "kg", img: "https://images.unsplash.com/photo-1588253518679-1293149fca65?w=200" },
        { id: 14, name: "धनिया", price: 20, stock: 15, unit: "kg", img: "https://images.unsplash.com/photo-1588879460618-9249e7d947d1?w=200" },
        { id: 15, name: "लहसुन", price: 200, stock: 5, unit: "kg", img: "https://images.unsplash.com/photo-1540148426945-6cf22a6b2383?w=200" },
        { id: 16, name: "अदरक", price: 160, stock: 5, unit: "kg", img: "https://images.unsplash.com/photo-1599940824399-b87987ceb72a?w=200" },
        { id: 17, name: "पनीर (200g)", price: 85, stock: 12, unit: "pkt", img: "https://images.unsplash.com/photo-1631452180519-c014fe946bc7?w=200" },
        { id: 18, name: "सेब", price: 150, stock: 20, unit: "kg", img: "https://images.unsplash.com/photo-1560806887-1e4cd0b6bccb?w=200" },
        { id: 19, name: "केला", price: 60, stock: 24, unit: "डजन", img: "https://images.unsplash.com/photo-1571771894821-ad99621139c6?w=200" },
        { id: 20, name: "नींबू", price: 10, stock: 50, unit: "pc", img: "https://images.unsplash.com/photo-1590505660564-41132890520a?w=200" }
    ];

    let cart = {};

    function formatWeight(val, unit) {
        if (unit === "kg") {
            if (val === 0) return "0 ग्राम";
            if (val < 1) return (val * 1000).toFixed(0) + " ग्राम";
            return val.toFixed(2) + " किलो";
        }
        return val + " " + unit;
    }

    function draw() {
        const query = document.getElementById('search').value.toLowerCase();
        document.getElementById('menu').innerHTML = db.filter(i => i.name.toLowerCase().includes(query)).map(i => {
            let sClass = "st-ok", sText = "स्टॉक: " + i.stock;
            if (i.stock <= 0) { sClass = "st-out"; sText = "खत्म"; }
            else if (i.stock <= 5) { sClass = "st-low"; sText = "सिर्फ " + i.stock + " बचा"; }
            
            return `
                <div class="item-card" style="${i.stock <= 0 ? 'opacity:0.6' : ''}">
                    <span class="badge ${sClass}">${sText}</span>
                    <img src="${i.img}">
                    <h4 style="margin:8px 0 2px 0; font-size:15px;">${i.name}</h4>
                    <div style="font-weight:900; color:var(--success); font-size:13px; margin-bottom:8px;">₹${i.price}/${i.unit}</div>
                    
                    ${i.unit === 'kg' ? `
                    <div style="display:flex; gap:4px; margin-bottom:8px;">
                        <button class="g-btn" onclick="setQty(${i.id}, 0.25)">250g</button>
                        <button class="g-btn" onclick="setQty(${i.id}, 0.5)">500g</button>
                        <button class="g-btn" onclick="setQty(${i.id}, 1)">1kg</button>
                    </div>` : ''}

                    <div class="qty-box">
                        <button class="btn-q" onclick="upd(${i.id}, -0.25)">-</button>
                        <span style="font-weight:bold; font-size:11px;">${formatWeight(cart[i.id] || 0, i.unit)}</span>
                        <button class="btn-q" onclick="upd(${i.id}, 0.25)">+</button>
                    </div>
                </div>`;
        }).join('');
    }

    function setQty(id, val) {
        const item = db.find(i => i.id === id);
        if (val > item.stock) return alert("स्टॉक कम है!");
        cart[id] = val; draw(); bill();
    }

    function upd(id, val) {
        const item = db.find(i => i.id === id);
        // किलो वाली चीज़ों के लिए 250 ग्राम का स्टेप, बाकियों के लिए 1 का स्टेप
        const step = (item.unit === 'kg') ? 0.25 : 1;
        const cur = cart[id] || 0;
        let newVal = (val > 0) ? cur + step : cur - step;
        
        if (val > 0 && newVal > item.stock) return alert("स्टॉक खत्म!");
        cart[id] = Math.max(0, parseFloat(newVal.toFixed(2)));
        draw(); bill();
    }

    function bill() {
        let totalCost = 0, count = 0, html = "";
        db.forEach(i => {
            if(cart[i.id] > 0) {
                let cost = cart[i.id] * i.price; totalCost += cost; count++;
                html += `<div style="display:flex; justify-content:space-between; margin-bottom:8px;">
                    <span><b>${i.name}</b> (${formatWeight(cart[i.id], i.unit)})</span>
                    <span>₹${cost.toFixed(0)}</span>
                </div>`;
            }
        });
        document.getElementById('total').innerText = totalCost.toFixed(0);
        document.getElementById('list').innerHTML = html || "थैला खाली है...";
        document.getElementById('jump').style.display = count > 0 ? 'block' : 'none';
        document.getElementById('count').innerText = count;
    }

    function order() {
        const n = document.getElementById('name').value, a = document.getElementById('addr').value, t = document.getElementById('total').innerText;
        if(!n || !a || t === "0") return alert("विवरण भरें!");
        let msg = `*नया ऑर्डर - किसान बाज़ार*%0A👤 ग्राहक: ${n}%0A📍 पता: ${a}%0A💰 बिल: ₹${t}%0A%0A*आइटम:*`;
        db.forEach(i => { if(cart[i.id] > 0) msg += `%0A- ${i.name} (${formatWeight(cart[i.id], i.unit)})`; });
        window.open(`https://wa.me/${WA_NUM}?text=${msg}`);
    }

    function save() {
        localStorage.setItem('k_n', document.getElementById('name').value);
        localStorage.setItem('k_a', document.getElementById('addr').value);
    }

    window.onload = () => {
        document.getElementById('name').value = localStorage.getItem('k_n') || '';
        document.getElementById('addr').value = localStorage.getItem('k_a') || '';
        draw();
    };
</script>
</body>
</html>
