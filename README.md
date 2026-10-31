# mother-baby
<!DOCTYPE html>
<html lang="ml">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Mother And Baby Shop</title>
    <link rel="manifest" href="manifest.json">
    <meta name="theme-color" content="#ff6b9d">
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; }
        body { background: #f5f5f5; color: #333; padding-bottom: 70px; }
        header { background: #ff6b9d; color: white; padding: 15px; text-align: center; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        header h1 { font-size: 1.3rem; }
        .container { padding: 15px; }
        .hidden { display: none; }
        .card { background: white; border-radius: 10px; padding: 15px; margin-bottom: 15px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
        input, select, button { width: 100%; padding: 12px; margin: 5px 0; border: 1px solid #ddd; border-radius: 8px; font-size: 16px; }
        button { background: #ff6b9d; color: white; border: none; font-weight: bold; cursor: pointer; }
        button.secondary { background: #4CAF50; }
        button.danger { background: #f44336; }
        button.whatsapp { background: #25D366; }
        .btn-group { display: flex; gap: 10px; }
        .btn-group button { flex: 1; }
        table { width: 100%; border-collapse: collapse; margin: 10px 0; }
        th, td { padding: 10px; text-align: left; border-bottom: 1px solid #eee; }
        th { background: #f9f9f9; font-size: 0.9rem; }
        .item-row { display: flex; justify-content: space-between; align-items: center; padding: 10px; background: #f9f9f9; margin: 5px 0; border-radius: 8px; }
        .stock-badge { background: #e3f2fd; color: #1976d2; padding: 4px 8px; border-radius: 12px; font-size: 0.85rem; }
        .low-stock { background: #ffebee !important; color: #c62828 !important; }
        nav { position: fixed; bottom: 0; left: 0; right: 0; background: white; display: flex; box-shadow: 0 -2px 10px rgba(0,0,0,0.1); z-index: 100; }
        nav button { background: white; color: #666; border: none; border-radius: 0; padding: 12px 5px; font-size: 0.75rem; flex: 1; }
        nav button.active { color: #ff6b9d; font-weight: bold; border-top: 3px solid #ff6b9d; }
        .total-box { background: #e8f5e9; padding: 15px; border-radius: 8px; margin: 10px 0; text-align: right; font-size: 1.1rem; }
        .bill-preview { background: white; padding: 20px; border: 2px dashed #ccc; border-radius: 10px; font-family: monospace; white-space: pre-line; margin: 10px 0; font-size: 0.9rem; }
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 10px; background: #fff3e0; margin: 5px 0; border-radius: 8px; }
        .qty-controls { display: flex; align-items: center; gap: 5px; }
        .qty-controls button { width: 35px; padding: 5px; }
        .qty-controls span { min-width: 30px; text-align: center; font-weight: bold; }
        h3 { margin-bottom: 10px; color: #555; }
    </style>
</head>
<body>
    <header>
        <h1>🍼 Mother And Baby</h1>
    </header>

    <div class="container">
        <div id="tab-items" class="tab-content">
            <div class="card">
                <h3>➕ New Item Add cheyyanam</h3>
                <input type="text" id="item-name" placeholder="Item Name (ex: Bucket)">
                <select id="item-unit">
                    <option value="pieces">Pieces (എண்ணം)</option>
                    <option value="packets">Packets (പாக்கറ്റ്)</option>
                    <option value="kg">Kg</option>
                    <option value="litre">Litre</option>
                </select>
                <input type="number" id="item-price" placeholder="Selling Price (₹)" step="0.01">
                <input type="number" id="item-stock" placeholder="Initial Stock" value="0">
                <button onclick="addItem()">Add Item</button>
            </div>
            <div id="items-list"></div>
        </div>

        <div id="tab-bill" class="tab-content hidden">
            <div class="card">
                <h3>🛒 New Bill (Customer)</h3>
                <input type="text" id="customer-name" placeholder="Customer Name (optional)">
                <input type="tel" id="customer-phone" placeholder="Phone (10 digit, for WhatsApp)">
                <select id="bill-item-select" onchange="addToCart()">
                    <option value="">-- Select Item to add --</option>
                </select>
                <div id="cart-items"></div>
                <div class="total-box" id="bill-total">Total: ₹0</div>
                <div class="btn-group">
                    <button class="whatsapp" onclick="generateAndShareBill()">📱 WhatsApp Bill</button>
                    <button class="secondary" onclick="clearBill()">Clear</button>
                </div>
                <div id="bill-preview" class="bill-preview hidden"></div>
            </div>
        </div>

        <div id="tab-wholesale" class="tab-content hidden">
            <div class="card">
                <h3>📦 Wholesale vanguka (Stock koodum)</h3>
                <input type="text" id="ws-item-name" placeholder="Item Name" list="items-datalist">
                <datalist id="items-datalist"></datalist>
                <input type="number" id="ws-qty" placeholder="Quantity vangiyathu">
                <input type="number" id="ws-price" placeholder="Price per unit (₹) vangiyathu" step="0.01">
                <button class="secondary" onclick="addPurchase()">Add Purchase</button>
            </div>
            <div id="purchases-list"></div>
        </div>

        <div id="tab-stock" class="tab-content hidden">
            <div class="card">
                <h3>📊 Current Stock</h3>
                <div id="stock-list"></div>
            </div>
            <div class="card">
                <h3>⚙️ Settings</h3>
                <label>GST Rate (%):</label>
                <input type="number" id="gst-rate" value="18" onchange="updateGST()">
            </div>
            <div class="card">
                <h3>💾 Backup (IMPORTANT!)</h3>
                <button onclick="exportData()">📤 Download Backup File</button>
                <input type="file" id="import-file" accept=".json" style="display:none" onchange="importData(event)">
                <button class="secondary" onclick="document.getElementById('import-file').click()">📥 Restore from File</button>
                <p style="font-size:0.8rem; color:#666; margin-top:5px;">Backup file Downloads-il save aakum. Phone maarumbo data safe aakaan ithu use cheyyanam.</p>
            </div>
        </div>
    </div>

    <nav>
        <button onclick="showTab('items')" id="nav-items" class="active">📋 Items</button>
        <button onclick="showTab('bill')" id="nav-bill">🧾 Bill</button>
        <button onclick="showTab('wholesale')" id="nav-wholesale">📦 Buy</button>
        <button onclick="showTab('stock')" id="nav-stock">📊 Stock</button>
    </nav>

    <script>
        let data = { items: [], bills: [], purchases: [], gstRate: 18 };
        let cart = [];

        function loadData() {
            const saved = localStorage.getItem('mandb_data');
            if (saved) data = JSON.parse(saved);
            document.getElementById('gst-rate').value = data.gstRate;
            renderAll();
        }

        function saveData() {
            localStorage.setItem('mandb_data', JSON.stringify(data));
        }

        function genId() { return Date.now() + Math.random(); }

        function addItem() {
            const name = document.getElementById('item-name').value.trim();
            const unit = document.getElementById('item-unit').value;
            const price = parseFloat(document.getElementById('item-price').value) || 0;
            const stock = parseInt(document.getElementById('item-stock').value) || 0;
            if (!name) return alert('Name venam!');
            data.items.push({ id: genId(), name, unit, price, stock });
            saveData(); renderAll();
            document.getElementById('item-name').value = '';
            document.getElementById('item-price').value = '';
            document.getElementById('item-stock').value = '0';
        }

        function deleteItem(id) {
            if (confirm('Delete item?')) {
                data.items = data.items.filter(i => i.id !== id);
                saveData(); renderAll();
            }
        }

        function editPrice(id) {
            const item = data.items.find(i => i.id === id);
            const p = prompt('New Selling Price:', item.price);
            if (p !== null) { item.price = parseFloat(p) || item.price; saveData(); renderAll(); }
        }

        function addToCart() {
            const sel = document.getElementById('bill-item-select');
            const id = sel.value;
            if (!id) return;
            const item = data.items.find(i => i.id == id);
            if (!item || item.stock <= 0) { alert('Stock illah!'); sel.value=''; return; }
            const ex = cart.find(c => c.id === item.id);
            if (ex) { if (ex.qty < item.stock) ex.qty++; else alert('Max stock: '+item.stock); }
            else cart.push({ id: item.id, name: item.name, price: item.price, unit: item.unit, qty: 1, max: item.stock });
            sel.value = ''; renderCart();
        }

        function chgQty(id, d) {
            const c = cart.find(x => x.id === id);
            if (!c) return;
            const n = c.qty + d;
            if (n < 1 || n > c.max) return;
            c.qty = n; renderCart();
        }

        function rmCart(id) { cart = cart.filter(c => c.id !== id); renderCart(); }

        function renderCart() {
            const div = document.getElementById('cart-items');
            div.innerHTML = '';
            let sub = 0;
            cart.forEach(c => {
                const t = c.price * c.qty; sub += t;
                div.innerHTML += `<div class="cart-item"><div><strong>${c.name}</strong><br><small>₹${c.price}×${c.qty}=₹${t.toFixed(2)}</small></div><div class="qty-controls"><button onclick="chgQty(${c.id},-1)">-</button><span>${c.qty}</span><button onclick="chgQty(${c.id},1)">+</button><button class="danger" onclick="rmCart(${c.id})" style="width:auto;">×</button></div></div>`;
            });
            const gst = sub * (data.gstRate/100);
            document.getElementById('bill-total').innerHTML = `Sub: ₹${sub.toFixed(2)}<br>GST(${data.gstRate}%): ₹${gst.toFixed(2)}<br><strong>Total: ₹${(sub+gst).toFixed(2)}</strong>`;
        }

        function clearBill() {
            cart = [];
            document.getElementById('customer-name').value='';
            document.getElementById('customer-phone').value='';
            document.getElementById('bill-preview').classList.add('hidden');
            renderCart();
        }

        function generateAndShareBill() {
            if (!cart.length) return alert('Cart empty!');
            const name = document.getElementById('customer-name').value || 'Customer';
            let phone = document.getElementById('customer-phone').value.replace(/\D/g,'');
            let sub = 0;
            let txt = `*MOTHER AND BABY* 🍼\n${new Date().toLocaleString('en-IN')}\nCustomer: ${name}\n----------------\n`;
            cart.forEach(c => { const t=c.price*c.qty; sub+=t; txt+=`${c.name}\n${c.qty} ${c.unit} × ₹${c.price} = ₹${t.toFixed(2)}\n`; });
            const gst = sub*(data.gstRate/100);
            txt += `----------------\nSubtotal: ₹${sub.toFixed(2)}\nGST(${data.gstRate}%): ₹${gst.toFixed(2)}\n*TOTAL: ₹${(sub+gst).toFixed(2)}*\n----------------\nThank you! 🙏`;

            data.bills.push({ id: genId(), date: new Date().toISOString(), name, phone, items: [...cart], total: sub+gst });
            cart.forEach(c => { const i=data.items.find(x=>x.id===c.id); if(i) i.stock-=c.qty; });
            saveData(); renderAll();

            document.getElementById('bill-preview').textContent = txt;
            document.getElementById('bill-preview').classList.remove('hidden');

            const enc = encodeURIComponent(txt);
            let url = 'https://wa.me/?text=' + enc;
            if (phone.length === 10) url = `https://wa.me/91${phone}?text=${enc}`;
            else if (phone.length > 10) url = `https://wa.me/${phone}?text=${enc}`;
            window.open(url, '_blank');
            setTimeout(clearBill, 1500);
        }

        function addPurchase() {
            const name = document.getElementById('ws-item-name').value.trim();
            const qty = parseInt(document.getElementById('ws-qty').value) || 0;
            const price = parseFloat(document.getElementById('ws-price').value) || 0;
            if (!name || qty <= 0) return alert('Details venam!');
            let item = data.items.find(i => i.name.toLowerCase() === name.toLowerCase());
            if (!item) {
                item = { id: genId(), name, unit: 'pieces', price: 0, stock: 0 };
                data.items.push(item);
                alert('New item create cheythu. Selling price Items tab-il set cheyyanam.');
            }
            item.stock += qty;
            data.purchases.push({ id: genId(), date: new Date().toISOString(), item: name, qty, price, total: qty*price });
            saveData(); renderAll();
            document.getElementById('ws-item-name').value='';
            document.getElementById('ws-qty').value='';
            document.getElementById('ws-price').value='';
        }

        function renderAll() {
            renderItems(); renderSelect(); renderCart(); renderStock(); renderPurchases(); renderList();
        }

        function renderItems() {
            const c = document.getElementById('items-list');
            c.innerHTML = '<h3>📋 Items</h3>';
            if (!data.items.length) return c.innerHTML += '<p>Empty</p>';
            data.items.forEach(i => {
                const cls = i.stock < 5 ? 'low-stock' : '';
                c.innerHTML += `<div class="item-row"><div><strong>${i.name}</strong><br><small>₹${i.price}/${i.unit} | <span class="stock-badge ${cls}">${i.stock}</span></small></div><div><button onclick="editPrice(${i.id})" style="width:auto;padding:5px 10px;font-size:0.8rem;">₹</button> <button class="danger" onclick="deleteItem(${i.id})" style="width:auto;padding:5px 10px;font-size:0.8rem;">×</button></div></div>`;
            });
        }

        function renderSelect() {
            const s = document.getElementById('bill-item-select');
            s.innerHTML = '<option value="">-- Select --</option>';
            data.items.filter(i => i.stock > 0).forEach(i => {
                s.innerHTML += `<option value="${i.id}">${i.name} (₹${i.price}, Stock:${i.stock})</option>`;
            });
        }

        function renderStock() {
            const c = document.getElementById('stock-list');
            if (!data.items.length) return c.innerHTML = '<p>No stock</p>';
            let h = '<table><tr><th>Item</th><th>Qty</th><th>Value</th></tr>';
            data.items.forEach(i => {
                const low = i.stock < 5 ? 'style="color:red;font-weight:bold;"' : '';
                h += `<tr><td>${i.name}</td><td ${low}>${i.stock} ${i.unit}</td><td>₹${(i.stock*i.price).toFixed(2)}</td></tr>`;
            });
            c.innerHTML = h + '</table>';
        }

        function renderPurchases() {
            const c = document.getElementById('purchases-list');
            c.innerHTML = '<h3>📦 Recent Purchases</h3>';
            const r = data.purchases.slice(-5).reverse();
            if (!r.length) return c.innerHTML += '<p>None yet</p>';
            r.forEach(p => {
                c.innerHTML += `<div class="item-row"><div><strong>${p.item}</strong><br><small>${p.qty} @ ₹${p.price} = ₹${p.total.toFixed(2)} | ${new Date(p.date).toLocaleDateString()}</small></div></div>`;
            });
        }

        function renderList() {
            const dl = document.getElementById('items-datalist');
            dl.innerHTML = data.items.map(i => `<option value="${i.name}">`).join('');
        }

        function updateGST() {
            data.gstRate = parseFloat(document.getElementById('gst-rate').value) || 18;
            saveData(); renderCart();
        }

        function exportData() {
            const b = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
            const a = document.createElement('a');
            a.href = URL.createObjectURL(b);
            a.download = `mandb-backup-${new Date().toISOString().split('T')[0]}.json`;
            a.click();
        }

        function importData(e) {
            const f = e.target.files[0];
            if (!f) return;
            const r = new FileReader();
            r.onload = ev => { try { data = JSON.parse(ev.target.result); saveData(); renderAll(); alert('Restored!'); } catch(err) { alert('Error'); } };
            r.readAsText(f);
        }

        function showTab(t) {
            document.querySelectorAll('.tab-content').forEach(x => x.classList.add('hidden'));
            document.getElementById('tab-'+t).classList.remove('hidden');
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
            document.getElementById('nav-'+t).classList.add('active');
        }

        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js').catch(()=>{});
        }

        loadData();
    </script>
</body>
</html>