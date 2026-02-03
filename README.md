<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>किसान बाज़ार</title>

<style>
body{margin:0;font-family:sans-serif;background:#f4f6f5}
header{background:#2d6a4f;color:#fff;padding:20px;text-align:center}
.container{max-width:900px;margin:auto;padding:15px}
.card{background:#fff;border-radius:15px;padding:15px;margin-bottom:15px}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.item{border:1px solid #eee;border-radius:12px;padding:10px;text-align:center}
.item img{width:100%;height:120px;object-fit:cover;border-radius:10px}
.qty{display:flex;justify-content:space-between;margin-top:5px}
button{border:none;border-radius:10px;padding:8px 12px;cursor:pointer}
.plus{background:#2d6a4f;color:#fff}
.minus{background:#ccc}
.bill-item{display:flex;justify-content:space-between;align-items:center;border-bottom:1px solid #eee;padding:6px 0}
.remove{color:red;font-size:18px;cursor:pointer;margin-left:10px}
.pay{display:flex;gap:10px;margin-top:10px}
.pay div{flex:1;padding:10px;border:1px solid #ccc;border-radius:10px;text-align:center;cursor:pointer}
.pay .active{background:#2d6a4f;color:#fff}
.whatsapp{background:#25D366;color:#fff;width:100%;font-size:16px}
.admin-btn{background:#000;color:#fff;width:100%}
.hidden{display:none}
input,select{width:100%;padding:8px;margin-top:5px}
.upi-box{border:1px dashed #2d6a4f;padding:10px;border-radius:10px;margin-top:10px;text-align:center}
.qr img{width:180px;margin-top:10px}
</style>
</head>

<body>

<header>
<h2>🌿 किसान बाज़ार</h2>
</header>

<div class="container">

<!-- CUSTOMER INFO -->
<div class="card">
<input id="cname" placeholder="आपका नाम">
<input id="cphone" placeholder="मोबाइल नंबर">
<input id="addr" placeholder="पूरा पता">
</div>

<!-- STAFF PANEL -->
<div class="card hidden" id="staffPanel">
<h3>👤 स्टाफ पैनल</h3>

<h4>📞 Contact / Payment</h4>
<input id="shopPhone" placeholder="WhatsApp नंबर">
<input id="upiId" placeholder="UPI ID">
<button onclick="saveSettings()">💾 सेव करें</button>

<hr>

<h4>➕ सब्ज़ी जोड़ें</h4>
<input id="sname" placeholder="सब्ज़ी का नाम">
<input id="skg" type="number" placeholder="₹ / kg">
<input id="sgm" type="number" placeholder="₹ / gram">
<input id="spc" type="number" placeholder="₹ / piece">
<input id="simg" type="file" accept="image/*">
<button onclick="addItem()">➕ Add</button>
</div>

<!-- MENU -->
<div class="grid" id="menu"></div>

<!-- BILL -->
<div class="card">
<h3>🧾 बिल</h3>
<div id="bill">कोई आइटम नहीं</div>
<h3>कुल ₹<span id="total">0</span></h3>

<div class="pay">
<div id="cash" class="active" onclick="payMode('Cash')">Cash</div>
<div id="upi" onclick="payMode('UPI')">UPI</div>
</div>

<div class="upi-box hidden" id="upiBox">
<b>UPI ID:</b> <span id="showUpi"></span>

<div class="qr">
<img id="qrImg">
</div>

<button onclick="payUPI()">UPI से भुगतान करें</button>
</div>

<button class="whatsapp" onclick="sendOrder()">📲 WhatsApp Order</button>
</div>

<button class="admin-btn" onclick="staffLogin()">🔐 Staff Login</button>

</div>

<script>
let payment="Cash";
let isStaff=false;

let settings = JSON.parse(localStorage.getItem("kb_settings")) || {
 phone:"916200730782",
 upi:"6200730782@fam"
};

let db = JSON.parse(localStorage.getItem("kb_db")) || [
 {id:1,name:"टमाटर",price:{kg:40,gm:0.04,pc:10},
 img:"https://images.unsplash.com/photo-1582284540020-8acbe03f4924?w=400"}
];

let cart={};

function saveAll(){
 localStorage.setItem("kb_db",JSON.stringify(db));
 localStorage.setItem("kb_settings",JSON.stringify(settings));
}

function draw(){
 showUpi.innerText=settings.upi;
 qrImg.src=`https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=upi://pay?pa=${settings.upi}`;
 let html="";
 db.forEach(i=>{
  let q=cart[i.id]?.qty||0;
  let u=cart[i.id]?.unit||"kg";
  html+=`
  <div class="item">
   <img src="${i.img}">
   <b>${i.name}</b><br>
   ₹${i.price[u]} / ${u}
   <select onchange="setUnit(${i.id},this.value)">
    <option value="kg">kg</option>
    <option value="gm">gram</option>
    <option value="pc">piece</option>
   </select>
   <div class="qty">
    <button class="minus" onclick="qty(${i.id},-1)">−</button>
    <b>${q}</b>
    <button class="plus" onclick="qty(${i.id},1)">+</button>
   </div>
  </div>`;
 });
 menu.innerHTML=html;
 bill();
}

function setUnit(id,u){
 if(!cart[id]) cart[id]={qty:0,unit:u};
 cart[id].unit=u; draw();
}

function qty(id,v){
 if(!cart[id]) cart[id]={qty:0,unit:"kg"};
 cart[id].qty+=v;
 if(cart[id].qty<=0) delete cart[id];
 draw();
}

function removeItem(id){
 delete cart[id];
 draw();
}

function bill(){
 let t=0,h="";
 db.forEach(i=>{
  if(cart[i.id]){
   let p=i.price[cart[i.id].unit]*cart[i.id].qty;
   t+=p;
   h+=`
   <div class="bill-item">
    <span>${i.name}</span>
    <span>
     ₹${p.toFixed(2)}
     <span class="remove" onclick="removeItem(${i.id})">❌</span>
    </span>
   </div>`;
  }
 });
 bill.innerHTML=h||"कोई आइटम नहीं";
 total.innerText=t.toFixed(2);
}

function payMode(m){
 payment=m;
 cash.classList.remove("active");
 upi.classList.remove("active");
 (m=="Cash"?cash:upi).classList.add("active");
 upiBox.classList.toggle("hidden",m!="UPI");
}

function payUPI(){
 let amt=total.innerText;
 let url=`upi://pay?pa=${settings.upi}&pn=KisanBazaar&am=${amt}&cu=INR`;
 location.href=url;
}

function sendOrder(){
 if(!cname.value||!cphone.value||!addr.value) return alert("सब भरें");
 let msg=`*किसान बाज़ार ऑर्डर*\nनाम:${cname.value}\nमोबाइल:${cphone.value}\nपता:${addr.value}\n\n`;
 db.forEach(i=>{
  if(cart[i.id]) msg+=`${i.name} ${cart[i.id].qty}${cart[i.id].unit}\n`;
 });
 msg+=`\nकुल ₹${total.innerText}\nPayment:${payment}`;
 window.open(`https://wa.me/${settings.phone}?text=${encodeURIComponent(msg)}`);
}

function staffLogin(){
 if(prompt("Staff PIN")=="5566"){
  isStaff=!isStaff;
  staffPanel.classList.toggle("hidden",!isStaff);
  shopPhone.value=settings.phone;
  upiId.value=settings.upi;
  draw();
 }
}

function saveSettings(){
 settings.phone=shopPhone.value;
 settings.upi=upiId.value;
 saveAll();
 alert("सेटिंग सेव हो गई");
 draw();
}

function addItem(){
 if(!sname.value||!skg.value) return alert("नाम और दाम भरें");
 let r=new FileReader();
 r.onload=e=>{
  db.push({
   id:Date.now(),
   name:sname.value,
   price:{kg:+skg.value,gm:+sgm.value,pc:+spc.value},
   img:simg.files[0]?e.target.result:"https://via.placeholder.com/300"
  });
  saveAll(); draw();
 };
 if(simg.files[0]) r.readAsDataURL(simg.files[0]);
}

draw();
</script>

</body>
</html>
