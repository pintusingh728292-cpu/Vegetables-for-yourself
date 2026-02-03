<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>किसान बाज़ार - App Style</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
<style>
:root {
  --primary:#1b4332; --success:#2d6a4f; --danger:#e63946; --light:#f8faf9; --white:#fff;
}
body {
  font-family:'Inter',sans-serif;
  margin:0;
  background:var(--light);
  padding-bottom:120px;
}
header {
  background: linear-gradient(135deg,var(--primary),var(--success));
  color:white;
  padding:20px 15px;
  text-align:center;
  border-radius:0 0 25px 25px;
  box-shadow:0 5px 15px rgba(0,0,0,0.1);
}
.container {
  max-width:600px;
  margin:auto;
  padding:10px 15px;
}
.top-box {
  background:var(--white);
  border-radius:20px;
  padding:15px;
  margin-top:-25px;
  box-shadow:0 10px 20px rgba(0,0,0,0.08);
  position:relative;
  z-index:5;
}
input,textarea,select {
  width:100%;
  padding:12px;
  border:1.5px solid #eee;
  border-radius:12px;
  margin-bottom:10px;
  font-size:14px;
  outline:none;
  box-sizing:border-box;
}
.product-grid {
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:12px;
  margin-top:15px;
}
.item-card {
  background:var(--white);
  border-radius:20px;
  padding:10px;
  border:1px solid #eee;
  text-align:center;
  position:relative;
  transition:0.2s;
}
.item-card:hover {
  transform:scale(1.02);
  box-shadow:0 8px 18px rgba(0,0,0,0.1);
}
.item-card img {
  width:100%;
  height:120px;
  object-fit:cover;
  border-radius:15px;
  background:#f9f9f9;
  cursor:pointer;
}
.qty-box {
  display:flex;
  align-items:center;
  justify-content:space-between;
  background:#f1f5f9;
  border-radius:12px;
  padding:3px;
  margin-top:5px;
}
.btn-q {
  border:none;
  background:white;
  width:35px;
  height:35px;
  border-radius:10px;
  cursor:pointer;
  font-weight:900;
  color:var(--success);
  font-size:20px;
  transition:0.2s;
}
.btn-q:hover { background:var(--success); color:white; }
.bill-card {
  background:#fff;
  border-radius:25px;
  padding:20px;
  margin-top:20px;
  border:2px solid var(--success);
  box-shadow:0 10px 25px rgba(0,0,0,0.08);
  transition:0.3s;
}
.bill-row {
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:10px 0;
  border-bottom:1px solid #f1f5f9;
  font-size:14px;
}
.del-x {
  color:var(--danger);
  font-weight:900;
  cursor:pointer;
  background:#fff0f0;
  width:25px;
  height:25px;
  border-radius:50%;
  display:flex;
  align-items:center;
  justify-content:center;
  font-size:12px;
  transition:0.2s;
}
.del-x:hover { background:var(--danger); color:white; }
#qr-box {
  text-align:center;
  margin-top:15px;
  padding:15px;
  background:#f9f9f9;
  border-radius:20px;
  border:1px dashed #ccc;
}
.staff-ui { display:none; margin-bottom:10px; }
.master-btn {
  padding:15px;
  border-radius:15px;
  border:none;
  font-weight:bold;
  cursor:pointer;
  width:100%;
  margin-bottom:10px;
  color:white;
  transition:0.2s;
}
.master-btn:hover { opacity:0.9; }
#pop {
  display:none;
  position:fixed;
  top:50%;
  left:50%;
  transform:translate(-50%,-50%);
  background:white;
  padding:25px;
  border-radius:25px;
  box-shadow:0 0 100px rgba(0,0,0,0.5);
  z-index:1000;
  width:85%;
  max-width:400px;
}
.bottom-bar {
  position:fixed;
  bottom:0;
  width:100%;
  background:white;
  display:flex;
  border-top:1px solid #eee;
  padding:10px 0;
  z-index:100;
}
.bottom-bar button,a {
  flex:1;
  text-align:center;
  border:none;
  font-weight:bold;
  font-size:14px;
}
</style>
</head>
<body>

<header>
<h1 style="margin:0;font-size:26px;">🌿 किसान बाज़ार</h1>
</header>

<div class="container">
<div class="top-box">
<div id="staff-header" class="staff-ui">
<button class="master-btn" style="background:#007bff;" onclick="downloadCode()">💾 नया अपडेटेड कोड डाउनलोड करें</button>
<button class="master-btn" style="background:#1a1a1a;" onclick="document.getElementById('pop').style.display='block'">➕ नई सब्जी जोड़ें</button>
</div>
<input type="text" id="c-name" placeholder="👤 ग्राहक का नाम" oninput="saveUser()">
<textarea id="c-addr" placeholder="📍 डिलीवरी का पूरा पता..." rows="2" oninput="saveUser()"></textarea>
<input type="text" id="search" placeholder="🔍 सब्जी का नाम खोजें..." onkeyup="draw()">
</div>

<div class="product-grid" id="menu"></div>

<div class="bill-card">
<h3 style="margin:0 0 15px 0;">📋 कुल बिल: ₹<span id="total">0</span></h3>
<div id="list" style="margin-bottom:15px;color:#666;">थैला खाली है...</div>

<select id="payMode" onchange="calc()" style="font-weight:bold;border-color:var(--success);">
<option value="Cash">💵 नकद (Cash on Delivery)</option>
<option value="UPI">📱 UPI / QR कोड</option>
</select>

<div id="qr-box" style="display:none;">
<img id="qr-img" src="" style="width:150px;background:white;padding:5px;border-radius:10px;">
<p style="font-size:11px;font-weight:bold;margin-top:8px;">UPI ID: 6200730782@fam</p>
</div>

<button onclick="sendOrder()" style="width:100%;background:#25D366;color:white;border:none;padding:18px;border-radius:18px;font-size:18px;font-weight:900;margin-top:10px;cursor:pointer;">WhatsApp ऑर्डर भेजें ➔</button>
</div>
</div>

<div id="pop">
<h3>नई सब्जी डालें</h3>
<input type="text" id="ni-n" placeholder="सब्जी का नाम">
<input type="number" id="ni-p" placeholder="रेट (₹ प्रति किलो/पीस)">
<input type="file" id="ni-f" accept="image/*">
<button onclick="addNew()" style="width:100%;background:var(--success);color:white;padding:15px;border-radius:15px;border:none;font-weight:bold;">सेव करें</button>
<button onclick="document.getElementById('pop').style.display='none'" style="margin-top:10px;width:100%;background:none;border:none;color:red;font-weight:bold;">बंद करें</button>
</div>

<div class="bottom-bar">
<button onclick="login()">👤 स्टाफ लॉगिन</button>
<a href="tel:+916200730782">📞 सहायता</a>
</div>

<script>
const WA_NUM="916200730782",UPI_ID="6200730782@fam",S_P="NTU2Ng==";
let isStaff=false,cart={};
let db=[
{id:1,name:"टमाटर",price:40,img:"data:image/"https://example.com/images/tamatar.png" },
{id:2,name:"आलू",price:30,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."},
{id:3,name:"प्याज़",price:50,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."},
{id:4,name:"भिंडी",price:60,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."},
{id:5,name:"गोभी",price:35,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."},
{id:6,name:"अदरक",price:80,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."},
{id:7,name:"लहसुन",price:90,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."},
{id:8,name:"हरी मिर्च",price:70,img:"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWCAYAAADdU..."}
];
if(localStorage.getItem('k_db')){db=JSON.parse(localStorage.getItem('k_db'));}
function login(){if(btoa(prompt("पिन दर्ज करें (Pin):"))===S_P){isStaff=!isStaff;document.querySelectorAll('.staff-ui').forEach(el=>el.style.display=isStaff?'block':'none');draw();}else{alert("गलत पिन!");}}
function draw(){
const q=document.getElementById('search').value.toLowerCase();
document.getElementById('menu').innerHTML=db.filter(i=>i.name.toLowerCase().includes(q)).map(i=>{
const u=cart[i.id]?.unit||'kg';
const v=cart[i.id]?.qty||0;
return`<div class="item-card">${v>0?`<div class="del-x" style="position:absolute;top:8px;right:8px;" onclick="remCart(${i.id})">✕</div>`:''}<img src="${i.img}" onclick="${isStaff?`trigImg(${i.id})`:''}"><input type="file" id="f-${i.id}" style="display:none" onchange="upImg(event,${i.id})"><h4 style="margin:8px 0;">${i.name}</h4><div style="font-weight:bold;color:var(--success);">₹${i.price}/${u}</div><select onchange="chUnit(${i.id},this.value)" style="width:100%;font-size:12px;margin:5px 0;"><option value="kg" ${u==='kg'?'selected':''}>किलो (Kg)</option><option value="gm" ${u==='gm'?'selected':''}>ग्राम (Gm)</option><option value="pc" ${u==='pc'?'selected':''}>पीस (Pc)</option></select><div class="qty-box"><button class="btn-q" onclick="upQty(${i.id},1)">+</button><span>${v}</span><button class="btn-q" onclick="upQty(${i.id},-1)">-</button></div>${isStaff?`<button onclick="remDb(${i.id})" style="color:red;background:none;border:none;font-size:10px;margin-top:12px;cursor:pointer;font-weight:bold;">हटाएं ✕</button>`:''}</div>`;
}).join('');
}
function chUnit(id,u){if(!cart[id])cart[id]={qty:0,unit:u};cart[id].unit=u;cart[id].qty=0;calc();draw();}
function upQty(id,v){if(!cart[id])cart[id]={qty:0,unit:'kg'};let step=(cart[id].unit==='gm')?50:1;cart[id].qty+=(v*step);if(cart[id].qty<=0)delete cart[id];calc();draw();}
function remCart(id){delete cart[id];calc();draw();}
function calc(){let t=0,h="";db.forEach(i=>{if(cart[i.id]){let p=(cart[i.id].unit==='gm')?(i.price/1000)*cart[i.id].qty:cart[i.id].qty*i.price;t+=p;h+=`<div class="bill-row"><span><b>${i.name}</b> (${cart[i.id].qty}${cart[i.id].unit})</span><span>₹${Math.round(p)} <span class="del-x" onclick="remCart(${i.id})">✕</span></span></div>`;}});document.getElementById('total').innerText=Math.round(t);document.getElementById('list').innerHTML=h||"थैला खाली है...";const qb=document.getElementById('qr-box');if(document.getElementById('payMode').value==='UPI'&&t>0){qb.style.display='block';document.getElementById('qr-img').src=`https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=upi://pay?pa=${UPI_ID}%26am=${Math.round(t)}%26cu=INR`; } else{qb.style.display='none';}}
function saveUser(){localStorage.setItem('k_n',document.getElementById('c-name').value);localStorage.setItem('k_a',document.getElementById('c-addr').value);}
function trigImg(id){document.getElementById(`f-${id}`).click();}
function upImg(e,id){const r=new FileReader();r.onload=(ev)=>{db=db.map(i=>i.id===id?{...i,img:ev.target.result,name:i.name,price:i.price,id:i.id}:i);sync();};r.readAsDataURL(e.target.files[0]);}
function addNew(){const n=document.getElementById('ni-n').value,p=Number(document.getElementById('ni-p').value),f=document.getElementById('ni-f').files[0];if(!n||!p)return alert("नाम और रेट भरें!");const r=new FileReader();r.onload=(e)=>{db.push({id:Date.now(),name:n,price:p,img:e.target.result});sync();document.getElementById('pop').style.display='none';};if(f)r.readAsDataURL(f);else{db.push({id:Date.now(),name:n,price:p,img:""});sync();document.getElementById('pop').style.display='none';}}
function sync(){localStorage.setItem('k_db',JSON.stringify(db));draw();}
function remDb(id){if(confirm("डिलीट करें?")){db=db.filter(i=>i.id!==id);sync();}}
function downloadCode(){let html=document.documentElement.outerHTML;let dbString=`let db=${JSON.stringify(db)};`;let updatedHtml=html.replace(/let db = \[[\s\S]*?\];/,dbString);let blob=new Blob([updatedHtml],{type:"text/html"});let a=document.createElement("a");a.href=URL.createObjectURL(blob);a.download="KisanBazar_Final.html";a.click();}
function sendOrder(){const n=document.getElementById('c-name').value,a=document.getElementById('c-addr').value,t=document.getElementById('total').innerText;if(!n||!a||t==="0")return alert("विवरण भरें!");let m=`*नया ऑर्डर - किसान बाज़ार*%0A👤 ग्राहक: ${n}%0A📍 पता: ${a}%0A💰 बिल: ₹${t}%0A💳 पेमेंट: ${document.getElementById('payMode').value}%0A%0A*लिस्ट:*`;db.forEach(i=>{if(cart[i.id])m+=`%0A- ${i.name} (${cart[i.id].qty}${cart[i.id].unit})`});window.open(`https://wa.me/${WA_NUM}?text=${m}`);}
window.onload=()=>{document.getElementById('c-name').value=localStorage.getItem('k_n')||'';document.getElementById('c-addr').value=localStorage.getItem('k_a')||'';draw();};
</script>
</body>
</html>
