<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>किसान बाज़ार - AI स्मार्ट स्टोर</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --primary: #1b4332; --success: #2d6a4f; --danger: #e63946; --light: #f8faf9; --white: #ffffff; --dark: #1a1a1a; }
        body { font-family: 'Inter', sans-serif; background: var(--light); margin: 0; padding-bottom: 150px; scroll-behavior: smooth; }
        header { background: linear-gradient(135deg, var(--primary), var(--success)); color: white; padding: 30px 20px; text-align: center; border-radius: 0 0 35px 35px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); }
        .ai-tag { font-size: 11px; background: rgba(255,255,255,0.2); padding: 4px 12px; border-radius: 12px; margin-top: 8px; display: inline-block; font-weight: bold; }
        .container { padding: 15px; max-width: 600px; margin: auto; }
        .top-box { background: var(--white); border-radius: 25px; padding: 20px; margin-top: -40px; box-shadow: 0 10px 30px rgba(0,0,0,0.08); position: relative; z-index: 10; }
        input, textarea, select { width: 100%; border: 1.5px solid #edf2f7; padding: 12px; border-radius: 15px; margin-bottom: 10px; box-sizing: border-box; font-size: 15px; outline: none; }
        .product-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 25px; }
        .item-card { background: var(--white); border-radius: 25px; padding: 12px; position: relative; border: 1px solid #eee; text-align: center; }
        .item-card img { width: 100%; height: 110px; object-fit: cover; border-radius: 20px; background: #f0f0f0; }
        .qty-box { display: flex; align-items: center; justify-content: space-between; background: #f1f5f9; border-radius: 15px; padding: 4px; margin-top: 8px; }
        .btn-q { border: none; background: white; width: 32px; height: 32px; border-radius: 10px; cursor: pointer; font-weight: bold; color: var(--success); }
        .unit-sel { width: 60px; padding: 5px; font-size: 12px; margin: 5px 0 0 0; border-radius: 8px; background: #fff; border: 1px solid #ddd; }
        #floating-bill-btn { display:none; position: fixed; bottom: 85px; left: 50%; transform: translateX(-50%); background: var(--dark); color: white; padding: 12px 25px; border-radius: 50px; font-weight: bold; border: none; z-index: 999; box-shadow: 0 5px 15px rgba(0,0,0,0.3); }
        .bill-card { background: #fff; border-radius: 25px; padding: 20px; margin-top: 30px; border: 2px solid var(--success); }
        .bill-item { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; padding-bottom: 5px; border-bottom: 1px solid #f1f5f9; font-size: 14px; }
        #upload-form { display:none; position:fixed; top:50%; left:50%; transform:translate(-50%, -50%); background:white; padding:25px; border-radius:30px; box-shadow:0 0 50px rgba(0,0,0,0.4); z-index:2000; width:85%; max-width:400px; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: grid; grid-template-columns: 1fr 1fr; border-top: 1px solid #eee; z-index: 1000; padding: 5px 0; }
        .nav-btn { padding: 15px; text-align: center; font-weight: bold; border:none; background:none; color: #64748b; }
    </style>
</head>
<body>

<header>
    <h1 style="margin:0; font-weight:900;">🌿 किसान बाज़ार</h1>
    <div class="ai-tag">AI: Artificial Intelligence (कृत्रिम बुद्धिमत्ता)</div>
</header>

<div class="container">
    <div class="top-box">
        <input type="text" id="name" placeholder="👤 आपका नाम" oninput="saveUserData()">
        <textarea id="addr" placeholder="📍 पूरा पता..." rows="2" oninput="saveUserData()"></textarea>
        <div style="display: flex; gap: 8px;">
            <input type="text" id="search" placeholder="🔍 खोजें..." onkeyup="draw()" style="margin-bottom:0;">
            <button id="add-btn" style="display:none; background:var(--dark); color:white; border:none; padding:0 15px; border-radius:15px;" onclick="document.getElementById('upload-form').style.display='block'">➕</button>
        </div>
    </div>

    <button id="floating-bill-btn" onclick="document.getElementById('checkout').scrollIntoView({behavior: 'smooth'})">🛒 बिल देखें ➔</button>

    <div id="upload-form">
        <h3>नई सब्जी</h3>
        <input type="text" id="ni-name" placeholder="सब्जी का नाम">
        <input type="number" id="ni-price" placeholder="1kg की कीमत (₹)">
        <input type="file" id="ni-img" accept="image/*">
        <button onclick="addNewItem()" style="width:100%; background:var(--success); color:white; padding:12px; border-radius:15px; border:none; font-weight:bold;">सेव करें</button>
        <button onclick="document.getElementById('upload-form').style.display='none'" style="margin-top:10px; width:100%; border:none; background:none; color:red;">रद्द करें</button>
    </div>

    <div class="product-grid" id="menu"></div>

    <div class="bill-card" id="checkout">
        <h3>📋 बिल: ₹<span id="total">0</span></h3>
        <div id="list" style="margin-bottom:15px; color:#444;">थैला खाली है...</div>
        
        <div style="display:flex; gap:10px; margin-bottom:15px;">
            <div class="pay-option selected" id="opt-cod" onclick="selectPay('Cash')">Cash</div>
            <div class="pay-option" id="opt-upi" onclick="selectPay('UPI')">UPI / QR</div>
        </div>

        <div id="upi-box">
            <img id="qr-img" src="" style="width:140px; border:3px solid #eee; border-radius:10px;">
            <p id="upi-id-text" style="font-size:12px; font-weight:bold; color:var(--success);"></p>
        </div>

        <button onclick="orderNow()" style="width:100%; background:#25D366; color:white; border:none; padding:15px; border-radius:15px; font-size:18px; font-weight:900;">WhatsApp ऑर्डर ➔</button>
    </div>
</div>

<div class="bottom-nav">
    <a href="tel:+916200730782" class="nav-btn" style="text-decoration:none;">📞 सहायता</a>
    <button class="nav-btn" style="background:#eee; border-radius:10px; margin:5px;" onclick="staffLogin()">👤 स्टाफ</button>
</div>

<script>
    const WA_NUM = "916200730782";
    const UPI_ID = "6200730782@fam";
    let isStaff = false;
    let paymentMode = "Cash";
    let cart = {}; // Store as: { id: { qty: 2, unit: 'kg' } }

    let db = JSON.parse(localStorage.getItem('k_db')) || [
        { id: 1, name: "टमाटर", price: 40, img: "https://images.unsplash.com/photo-1582284540020-8acbe03f4924?w=250", stop: false },
        { id: 2, name: "मिर्च", price: 80, img: "https://images.unsplash.com/photo-1588252303782-cb80119abd6d?w=250", stop: false }
    ];

    function staffLogin() {
        if(prompt("पिन:") === "5566") { isStaff = !isStaff; document.getElementById('add-btn').style.display = isStaff ? 'block' : 'none'; draw(); }
    }

    function draw() {
        const q = document.getElementById('search').value.toLowerCase();
        document.getElementById('menu').innerHTML = db.filter(i => i.name.toLowerCase().includes(q)).map(i => {
            const isOut = i.stop === true;
            const currentUnit = cart[i.id]?.unit || 'kg';
            const currentVal = cart[i.id]?.qty || 0;

            return `
                <div class="item-card ${isOut ? 'out-of-stock' : ''}">
                    <img src="${i.img}" onclick="${isStaff ? `triggerImg(${i.id})` : ''}">
                    <input type="file" id="f-${i.id}" style="display:none" onchange="updateImg(event, ${i.id})">
                    
                    ${isStaff ? `<input type="text" value="${i.name}" onchange="updateVal(${i.id},'name',this.value)" style="padding:5px; text-align:center;">
                                 <input type="number" value="${i.price}" onchange="updateVal(${i.id},'price',this.value)" style="padding:5px; text-align:center;">` 
                               : `<h4>${i.name}</h4><div style="font-weight:bold; color:green;">₹${i.price}/kg</div>`}
                    
                    ${!isOut ? `
                        <select class="unit-sel" id="unit-${i.id}" onchange="changeUnit(${i.id}, this.value)">
                            <option value="kg" ${currentUnit==='kg'?'selected':''}>kg</option>
                            <option value="gm" ${currentUnit==='gm'?'selected':''}>gm</option>
                        </select>
                        <div class="qty-box">
                            <button class="btn-q" onclick="updQty(${i.id}, 1)">+</button>
                            <span>${currentVal}</span>
                            <button class="btn-q" onclick="updQty(${i.id}, -1)">-</button>
                        </div>
                    ` : '<p style="color:red; font-size:12px;">Stock Out</p>'}

                    ${isStaff ? `<button onclick="toggleStop(${i.id})" style="width:100%; padding:5px; margin-top:5px; background:orange; border:none; border-radius:5px;">${isOut?'Start':'Stop'}</button>
                                 <button onclick="removeItem(${i.id})" style="color:red; background:none; border:none; margin-top:5px;">हटाएं ✕</button>` : ''}
                </div>
            `;
        }).join('');
    }

    function changeUnit(id, unit) {
        if(!cart[id]) cart[id] = { qty: 0, unit: unit };
        cart[id].unit = unit;
        calcBill();
    }

    function updQty(id, v) {
        const unit = document.getElementById(`unit-${id}`).value;
        if(!cart[id]) cart[id] = { qty: 0, unit: unit };
        
        // ग्राम में 50-50 बढ़ेगा, किलो में 1-1
        let step = (cart[id].unit === 'gm') ? 50 : 1;
        cart[id].qty += (v * step);
        
        if(cart[id].qty <= 0) delete cart[id];
        draw(); calcBill();
    }

    function calcBill() {
        let t = 0; let h = "";
        db.forEach(i => {
            if(cart[i.id]) {
                let itemTotal = 0;
                if(cart[i.id].unit === 'kg') {
                    itemTotal = cart[i.id].qty * i.price;
                } else {
                    itemTotal = (i.price / 1000) * cart[i.id].qty;
                }
                t += itemTotal;
                h += `<div class="bill-item">
                        <span><b>${i.name}</b> (${cart[i.id].qty}${cart[i.id].unit})</span>
                        <span>₹${itemTotal.toFixed(0)} <b style="color:red; margin-left:10px; cursor:pointer;" onclick="delCart(${i.id})">✕</b></span>
                      </div>`;
            }
        });
        document.getElementById('total').innerText = Math.round(t);
        document.getElementById('list').innerHTML = h || "थैला खाली है...";
        document.getElementById('floating-bill-btn').style.display = t > 0 ? 'block' : 'none';
        
        if(t > 0 && paymentMode === "UPI") {
            document.getElementById('upi-box').style.display = 'block';
            document.getElementById('upi-id-text').innerText = UPI_ID;
            document.getElementById('qr-img').src = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=upi://pay?pa=${UPI_ID}%26pn=KisanBazar%26am=${Math.round(t)}%26cu=INR`;
        } else { document.getElementById('upi-box').style.display = 'none'; }
    }

    function delCart(id) { delete cart[id]; draw(); calcBill(); }
    function triggerImg(id) { document.getElementById(`f-${id}`).click(); }
    function updateImg(e, id) {
        const r = new FileReader();
        r.onload = (ev) => { db = db.map(i => i.id === id ? {...i, img: ev.target.result} : i); sync(); };
        r.readAsDataURL(e.target.files[0]);
    }
    function updateVal(id, k, v) { db = db.map(i => i.id === id ? {...i, [k]: k==='price'?Number(v):v} : i); sync(); }
    function toggleStop(id) { db = db.map(i => i.id === id ? {...i, stop: !i.stop} : i); sync(); }
    function selectPay(m) { paymentMode = m; document.getElementById('opt-cod').className = m==='Cash'?'pay-option selected':'pay-option'; document.getElementById('opt-upi').className = m==='UPI'?'pay-option selected':'pay-option'; calcBill(); }
    function sync() { localStorage.setItem('k_db', JSON.stringify(db)); draw(); }
    function saveUserData() { localStorage.setItem('k_n', document.getElementById('name').value); localStorage.setItem('k_a', document.getElementById('addr').value); }
    function removeItem(id) { if(confirm("डिलीट?")) { db = db.filter(i => i.id !== id); sync(); } }

    function addNewItem() {
        const n = document.getElementById('ni-name').value, p = Number(document.getElementById('ni-price').value), img = document.getElementById('ni-img').files[0];
        if(!n || !p || !img) return alert("सब भरें!");
        const r = new FileReader();
        r.onload = (e) => { db.push({ id: Date.now(), name: n, price: p, img: e.target.result, stop: false }); sync(); document.getElementById('upload-form').style.display='none'; };
        r.readAsDataURL(img);
    }

    function orderNow() {
        const n = document.getElementById('name').value, a = document.getElementById('addr').value, t = document.getElementById('total').innerText;
        if(!n || !a || t === "0") return alert("विवरण भरें!");
        let m = `*ऑर्डर - किसान बाज़ार*%0A👤 ग्राहक: ${n}%0A📍 पता: ${a}%0A💰 कुल: ₹${t}%0A💳 पेमेंट: ${paymentMode}%0A%0A*लिस्ट:*`;
        db.forEach(i => { if(cart[i.id]) m += `%0A- ${i.name} (${cart[i.id].qty}${cart[i.id].unit})`; });
        window.open(`https://wa.me/${WA_NUM}?text=${m}`);
    }

    window.onload = () => { 
        document.getElementById('name').value = localStorage.getItem('k_n') || ''; 
        document.getElementById('addr').value = localStorage.getItem('k_a') || ''; 
        draw(); 
    };
</script>
</body>
</html>
