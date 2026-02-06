<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>T-COM Mobile (Cloud Sync)</title>
    <style>
        body { font-family: 'Kanit', sans-serif; background-color: #f0f2f5; padding: 20px; display: flex; justify-content: center; }
        .container { background: white; padding: 2rem; border-radius: 20px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); width: 100%; max-width: 450px; text-align: center; }
        .status { font-size: 12px; color: #4caf50; margin-bottom: 10px; }
        input { width: 100%; padding: 15px; border: 2px solid #ddd; border-radius: 12px; font-size: 18px; box-sizing: border-box; }
        .result-box { margin-top: 25px; padding: 20px; border-radius: 15px; background: #f8f9fa; display: none; animation: fadeIn 0.3s; }
        .loc-tag { font-size: 40px; font-weight: bold; color: #007bff; display: block; margin-top: 10px; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<div class="container">
    <h2>ทีคอมโมบาย 📱</h2>
    <div id="loading" class="status">🔄 กำลังเชื่อมต่อฐานข้อมูล...</div>
    
    <input list="productList" id="searchInput" placeholder="พิมพ์ชื่อสินค้า..." oninput="checkMatch()">
    <datalist id="productList"></datalist>

    <div id="result" class="result-box">
        <div id="itemName" style="font-size: 16px; color: #666;"></div>
        <div class="loc-tag" id="itemLoc"></div>
    </div>
</div>

<script>
    // 🚩 วาง File ID ของ Google Sheets ของคุณที่นี่
    const SPREADSHEET_ID = 'Teera';
    const SHEET_NAME = 'Sheet1'; 
    const URL = `https://docs.google.com/spreadsheets/d/${SPREADSHEET_ID}/gviz/tq?tqx=out:json&sheet=${SHEET_NAME}`;

    let inventory = [];

    // ดึงข้อมูลจาก Google Sheets
    fetch(URL)
        .then(res => res.text())
        .then(data => {
            const json = JSON.parse(data.substr(47).slice(0, -2));
            const rows = json.table.rows;
            const list = document.getElementById('productList');
            
            rows.forEach(row => {
                const name = row.c[0]?.v;
                const loc = row.c[1]?.v;
                if(name) {
                    inventory.push({ name, loc });
                    let option = document.createElement('option');
                    option.value = name;
                    list.appendChild(option);
                }
            });
            document.getElementById('loading').innerText = '✅ ฐานข้อมูลพร้อมใช้งาน';
        });

    function checkMatch() {
        const val = document.getElementById('searchInput').value;
        const resDiv = document.getElementById('result');
        const match = inventory.find(i => i.name === val);

        if(match) {
            document.getElementById('itemName').innerText = match.name;
            document.getElementById('itemLoc').innerText = match.loc;
            resDiv.style.display = 'block';
        } else {
            resDiv.style.display = 'none';
        }
    }
</script>
</body>
</html>
