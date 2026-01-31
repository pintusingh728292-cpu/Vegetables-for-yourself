<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    
    <title>किसान बाज़ार - App</title>
    
    <style>
        :root { --farm-green: #4caf50; --dark-green: #2e7d32; --bg: #f4f7f6; }
        
        body { 
            font-family: 'Segoe UI', Roboto, sans-serif; 
            background: var(--bg); 
            margin: 0; 
            padding-bottom: 80px; /* Space for bottom button */
            -webkit-tap-highlight-color: transparent;
        }

        header { 
            background: var(--dark-green); 
            color: white; 
            text-align: center; 
            padding: 20px 10px; 
            border-radius: 0 0 20px 20px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }

        .container { padding: 15px; max-width: 600px; margin: auto; }
        
        /* Grid setup like an App */
        .product-list { 
            display: grid; 
            grid-template-columns: repeat(2, 1fr); 
            gap: 12px; 
            margin-bottom: 20px;
        }

        .card { 
            background: white; 
            border-radius: 15px; 
            padding: 10px; 
            text-align: center; 
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            transition: transform 0.2s;
        }
        
        .card:active { transform: scale(0.95); }

        .card img { width: 100%; height: 110px; object-fit: cover; border-radius: 10px; }
        
        .qty-controls { 
            display: flex; 
            align-items: center; 
            justify-content: space-between; 
            background: #f0f0f0;
            border-radius: 10px;
            margin-top: 8px;
            padding: 2px;
        }

        .qty-btn { 
            background: white; 
            border: none; 
            width: 30px; 
            height: 30px; 
            border-radius: 8px; 
            font-size: 18px; 
            font-weight: bold;
            color: var(--dark-green);
        }

        .sidebar { 
            background: white; 
            padding: 20px; 
            border-radius: 20px; 
            box-shadow: 0 -5px 20px rgba(0,0,0,0.05);
        }

        input, textarea { 
            width: 100%; 
            padding: 12px; 
            margin-bottom: 12px; 
            border: 1px solid #ddd; 
            border-radius: 10px; 
            font-size: 16px;
            outline: none;
        }

        .pay-info { 
            background: #fff9c4; 
            border: 2px dashed #fbc02d; 
            padding: 15px; 
            border-radius: 15px; 
            text-align: center; 
            margin: 15px 0;
        }

        /* Fixed Bottom Button like real apps */
        .footer-action {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: white;
            padding: 15px;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.1);
            display: flex;
            justify-content: center;
        }

        .whatsapp-btn { 
            background: #25D366; 
            color: white; 
            width: 100%; 
            max-width: 400px;
            padding: 15px; 
            border: none; 
            border-radius: 12px; 
            font-weight: bold; 
            font-size: 16px; 
            cursor: pointer;
        }
    </style>
</head>
<body>

<header>
    <h1>🌿 किसान बाज़ार</h1>
    <p>शुद्ध और ताज़ी सब्जियां</p>
</header>

<div class="container">
    <div class="product-list" id="productList"></div>

    <div class="sidebar">
        <h3>📋 ऑर्डर की जानकारी</h3>
        <input type="text" id="name" placeholder="आपका नाम">
        <input type="number" id="phone" placeholder="WhatsApp नंबर">
        <textarea id="address" placeholder="पूरा पता"></textarea>

        <div style="background:#f9f9f9; padding:10px; border-radius:10px;">
            <strong>🛒 आपका थैला:</strong>
            <div id="cartItems" style="margin: 10px 0; font-size: 14px;">अभी थैला खाली है...</div>
            <hr>
            <div style="display:flex; justify-content:space-between; font-weight:bold; font-size:18px;">
                <span>कुल बिल:</span>
                <span>₹<span id="total">0</span></span>
            </div>
        </div>

        <div class="pay-info">
            <strong>📲 ऑनलाइन पेमेंट (Scan करें)</strong><br>
            <img id="qrImg" src="https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=upi://pay?pa=6200730782@fam&pn=KisanBazar" style="width:150px; margin: 10px 0;">
            <br><small>UPI: 6200730782@fam</small>
        </div>
    </div>
</div>

<div class="footer-action">
    <button class="whatsapp-btn" onclick="sendWhatsApp()">WhatsApp पर ऑर्डर भेजें ➔</button>
</div>

<script>
    const items = [
        { id: 1, name: "टमाटर", price: 40, img: "https://images.unsplash.com/photo-1592924357228-91a4daadcfea?w=300" },
        { id: 2, name: "आलू", price: 25, img: "https://images.unsplash.com/photo-1518977676601-b53f02ac6d31?w=300" },
        { id: 3, name: "प्याज", price: 35, img: "https://images.unsplash.com/photo-1508747703725-719777637510?w=300" },
        { id: 4, name: "फूलगोभी", price: 45, img: "https://images.unsplash.com/photo-1568584711075-3d021a7c3ec3?w=300" },
        { id: 5, name: "लहसुन", price: 160, img: "https://images.unsplash.com/photo-1540148426945-6cf22a6b2383?w=300" },
        { id: 6, name: "भिंडी", price: 50, img: "https://cdn.pixabay.com/photo/2012/04/24/16/18/okra-40292_640.png" }
    ];

    let cart = {};

    function render() {
        document.getElementById('productList').innerHTML = items.map(i => `
            <div class="card">
                <img src="${i.img}">
                <h4>${i.name}</h4>
                <div style="color:#666; font-size:14px;">₹${i.price}/kg</div>
                <div class="qty-controls">
                    <button class="qty-btn" onclick="updateQty(${i.id}, -0.25)">-</button>
                    <span id="qty-${i.id}" style="font-weight:bold;">${cart[i.id] || 0}kg</span>
                    <button class="qty-btn" onclick="updateQty(${i.id}, 0.25)">+</button>
                </div>
            </div>
        `).join('');
    }

    function updateQty(id, val) {
        cart[id] = Math.max(0, (cart[id] || 0) + val);
        document.getElementById(`qty-${id}`).innerText = cart[id] + "kg";
        updateBill();
    }

    function updateBill() {
        let total = 0;
        let listText = "";
        items.forEach(i => {
            if(cart[i.id] > 0) {
                let cost = cart[i.id] * i.price;
                total += cost;
                listText += `• ${i.name} (${cart[i.id]}kg) - ₹${cost}<br>`;
            }
        });
        document.getElementById('total').innerText = total;
        document.getElementById('cartItems').innerHTML = listText || "अभी थैला खाली है...";
        
        const upi = `upi://pay?pa=6200730782@fam&pn=KisanBazar&am=${total}&cu=INR`;
        document.getElementById('qrImg').src = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(upi)}`;
    }

    function sendWhatsApp() {
        let name = document.getElementById('name').value;
        let addr = document.getElementById('address').value;
        let total = document.getElementById('total').innerText;
        if(!name || !addr || total == 0) { alert("कृपया नाम, पता और सामान चेक करें!"); return; }
        
        let msg = `*नया ऑर्डर: किसान बाज़ार*%0a*नाम:* ${name}%0a*पता:* ${addr}%0a*कुल बिल:* ₹${total}%0a*सामान:*%0a`;
        items.forEach(i => { if(cart[i.id] > 0) msg += `- ${i.name} (${cart[i.id]}kg)%0a`; });
        
        window.open(`https://wa.me/916200730782?text=${msg}`);
    }

    render();
</script>
</body>
</html>
