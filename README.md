[index.html](https://github.com/user-attachments/files/26177291/index.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>食堂大冒险 | 今天吃什么</title>
    
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="食堂大冒险">
    <link rel="apple-touch-icon" href="https://img.icons8.com/pixel-line/96/hamburger.png">
    <link rel="icon" href="https://img.icons8.com/pixel-line/96/hamburger.png">

    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css"/>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=ZCOOL+KuaiLe&display=swap');
        
        :root {
            --gb-dark: #2d3322;
            --gb-light: #f4f1de;
            --gb-accent: #e07a5f;
            --gb-green: #81b29a;
            --gb-yellow: #f2cc8f;
        }

        body { 
            background-color: var(--gb-light);
            color: var(--gb-dark); 
            font-family: 'ZCOOL KuaiLe', cursive; 
            overflow: hidden;
            /* 防止手机端长按弹出菜单 */
            -webkit-touch-callout: none;
            -webkit-user-select: none;
            padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
        }
        
        /* 经典像素面板 */
        .gb-panel {
            border: 4px solid var(--gb-dark);
            background: #fff;
            box-shadow: 4px 4px 0px rgba(0,0,0,0.1), inset -2px -2px 0px var(--gb-yellow);
        }

        /* 核心按钮 */
        .gb-btn-main {
            background: var(--gb-accent);
            color: white;
            border: 4px solid var(--gb-dark);
            box-shadow: 0 6px 0 var(--gb-dark);
            transition: all 0.05s;
            text-shadow: 2px 2px 0 rgba(0,0,0,0.2);
        }
        .gb-btn-main:active { transform: translateY(4px); box-shadow: 0 2px 0 var(--gb-dark); }

        /* 转盘机械感打磨 */
        .wheel-outer {
            width: 280px; height: 280px;
            border-radius: 50%;
            border: 8px solid var(--gb-dark);
            background: var(--gb-dark);
            position: relative;
            box-shadow: 0 10px 0px rgba(0,0,0,0.1);
        }
        .wheel-main {
            position: absolute; inset: 8px;
            border-radius: 50%;
            border: 4px solid var(--gb-dark);
            transition: transform 4s cubic-bezier(0.15, 0, 0.15, 1.15);
        }

        /* 属性标签选择器 */
        .tag-checkbox { display: none; }
        .tag-label {
            display: inline-block;
            padding: 4px 10px;
            margin: 2px;
            border: 2px solid var(--gb-dark);
            font-size: 12px;
            background: #fff;
        }
        .tag-checkbox:checked + .tag-label {
            background: var(--gb-green);
            box-shadow: inset 2px 2px 0 rgba(0,0,0,0.2);
        }

        /* 扫描动画 */
        .scan-line {
            position: absolute; width: 100%; height: 4px;
            background: var(--gb-accent);
            top: 0; left: 0;
            animation: scanning 2s linear infinite;
        }
        @keyframes scanning { from { top: 0; } to { top: 100%; } }

        #food-list::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col p-4 animate__animated animate__fadeIn">

    <header class="mb-4">
        <div class="gb-panel p-3 flex justify-between items-center bg-white">
            <div class="flex flex-col">
                <span class="text-[10px] opacity-60 italic tracking-tighter">HUNGRY HERO STATS</span>
                <div class="flex items-center gap-1">
                    <span id="ui-hp-icon">🍖</span>
                    <div class="w-24 h-3 border-2 border-dark bg-gray-100 overflow-hidden">
                        <div id="hp-bar" class="h-full bg-green-400 transition-all duration-1000" style="width: 20%"></div>
                    </div>
                </div>
            </div>
            <div class="flex flex-col items-end">
                <div class="bg-red-500 text-white text-[10px] px-1 border border-dark font-bold">纠结: <span id="reject-val">0</span>/3</div>
                <span class="text-[10px] mt-1 font-bold">LV.99 🏰</span>
            </div>
        </div>
    </header>

    <main id="page-home" class="flex-1 flex flex-col items-center justify-around py-2">
        <div class="relative">
            <div class="absolute -top-10 left-1/2 -translate-x-1/2 z-20 text-5xl animate-bounce">📍</div>
            <div class="wheel-outer">
                <div id="wheel" class="wheel-main overflow-hidden"></div>
                <div class="absolute inset-0 m-auto w-12 h-12 bg-white border-4 border-dark rounded-full z-10 flex items-center justify-center shadow-lg">
                    <div class="w-2 h-2 bg-red-400 animate-pulse rounded-full"></div>
                </div>
            </div>
        </div>

        <div id="result-box" class="h-16 flex flex-col items-center justify-center hidden animate__animated animate__jackInTheBox">
            <div class="gb-panel px-8 py-2 bg-yellow-100 text-center">
                <p class="text-[10px] text-gray-500">✨ YOU GOT IT! ✨</p>
                <h2 id="result-text" class="text-xl font-black text-red-600 tracking-widest italic"></h2>
            </div>
        </div>

        <button onclick="handleDecide()" id="main-btn" class="gb-btn-main w-72 py-5 text-xl font-bold tracking-widest italic rounded-none">
            🕹️ 判定启动 ！
        </button>
    </main>

    <main id="page-list" class="flex-1 hidden flex flex-col overflow-hidden animate__animated animate__fadeIn">
        <div class="flex justify-between items-end mb-4 px-1">
            <h2 class="text-2xl font-black italic">📜 秘录图鉴</h2>
            <button onclick="openModal()" class="gb-panel bg-green-400 px-4 py-1.5 text-xs font-bold active:scale-95 shadow-none">＋ 录入灵感</button>
        </div>
        <div id="food-list" class="flex-1 overflow-y-auto space-y-3 pb-24 px-1"></div>
    </main>

    <nav class="mt-4 flex gap-3 h-16">
        <button onclick="nav('home')" id="nav-home" class="flex-1 gb-panel font-bold text-center bg-white border-b-8">探索 🏰</button>
        <button onclick="nav('list')" id="nav-list" class="flex-1 gb-panel font-bold text-center bg-gray-200">图鉴 📜</button>
    </nav>

    <div id="add-modal" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-6 backdrop-blur-sm">
        <div class="gb-panel p-6 w-full max-w-sm animate__animated animate__zoomIn">
            <h3 class="font-bold mb-4 flex items-center gap-2">📝 录入新灵感</h3>
            <div class="flex gap-2 mb-4">
                <input id="new-name" type="text" placeholder="食谱名称..." class="flex-1 border-4 border-dark p-3 outline-none font-bold placeholder:text-gray-300">
                <button onclick="triggerCamera()" class="gb-panel bg-blue-400 p-3 shadow-none active:scale-90">📷</button>
            </div>
            
            <p class="text-[10px] font-bold mb-2 opacity-60">口味属性 (ATTRIBUTES):</p>
            <div class="flex flex-wrap mb-6" id="tag-container">
                </div>

            <div class="flex gap-2">
                <button onclick="saveFood()" class="flex-1 gb-btn-main py-4 bg-green-500 font-bold rounded-none">确认存入</button>
                <button onclick="closeModal()" class="flex-1 py-4 text-gray-500 font-bold gb-panel border-none shadow-none rounded-none bg-gray-100">取消</button>
            </div>
        </div>
    </div>

    <div id="ai-loading" class="fixed inset-0 bg-black/80 hidden z-[60] flex flex-col items-center justify-center text-white">
        <div class="w-64 h-64 gb-panel relative overflow-hidden bg-gray-800">
            <img id="preview-img" class="w-full h-full object-cover opacity-60">
            <div class="scan-line"></div>
        </div>
        <p class="mt-8 text-white tracking-[8px] animate-pulse">AI 图像解析中...</p>
    </div>

    <input type="file" id="camera-file" accept="image/*" capture="camera" class="hidden" onchange="processPhoto(event)">

    <script>
        const RETRO_PALETTE = ['#f4adac', '#98d1df', '#bedcba', '#f9e8b7', '#cecae4'];
        
        let foods = JSON.parse(localStorage.getItem('my_food_v4')) || [
            { name: "螺蛳粉", tags: ["🌶️ 辣", "🧂 咸"] },
            { name: "萨莉亚", tags: ["🍯 甜", "🍗 炸"] },
            { name: "重庆火锅", tags: ["🌶️ 辣"] },
            { name: "便利店", tags: ["🥗 轻"] },
            { name: "肯德基", tags: ["🍗 炸"] }
        ];

        let rotation = 0;
        let isRunning = false;
        let rejects = 0;

        const flavors = ["🍋 酸", "🍯 甜", "🌶️ 辣", "🧂 咸", "🍗 炸", "🥗 轻"];
        document.getElementById('tag-container').innerHTML = flavors.map(f => `
            <input type="checkbox" id="t-${f}" class="tag-checkbox" value="${f}">
            <label for="t-${f}" class="tag-label transition-all">${f}</label>
        `).join('');

        function updateWheel() {
            const wheel = document.getElementById('wheel');
            if(foods.length === 0) { wheel.innerHTML = ""; wheel.style.background = "#eee"; return; }
            const step = 360 / foods.length;
            wheel.style.background = `conic-gradient(${foods.map((f, i) => `${RETRO_PALETTE[i % RETRO_PALETTE.length]} ${i*step}deg ${(i+1)*step}deg`).join(',')})`;
            wheel.innerHTML = foods.map((f, i) => `<div style="position:absolute; width:100%; height:100%; transform:rotate(${i*step + step/2}deg); text-align:center; padding-top:25px; color:#fff; font-weight:bold; font-size:12px; text-shadow:2px 2px 0 #000;">${f.name}</div>`).join('');
        }

        function handleDecide() {
            if (isRunning || foods.length === 0) return;
            isRunning = true;
            document.getElementById('result-box').classList.add('hidden');
            const btn = document.getElementById('main-btn');
            btn.style.opacity = "0.7";
            btn.innerText = "判定中...";

            rejects++;
            document.getElementById('reject-val').innerText = rejects;
            if(rejects > 3) {
                alert("⚠️ 纠结症警报：刷新频率过高！勇者需冷静片刻... 🚰");
                rejects = 0; document.getElementById('reject-val').innerText = "0";
                isRunning = false; btn.style.opacity = "1"; btn.innerText = "🕹️ 判定启动 ！";
                return;
            }

            rotation += (2160 + Math.floor(Math.random() * 1440));
            document.getElementById('wheel').style.transform = `rotate(${rotation}deg)`;
            
            setTimeout(() => {
                isRunning = false;
                btn.style.opacity = "1"; btn.innerText = "🕹️ 再次判定 !";
                const actual = rotation % 360;
                const idx = Math.floor(((360 - (actual % 360)) % 360) / (360/foods.length));
                document.getElementById('result-text').innerText = foods[idx].name;
                document.getElementById('result-box').classList.remove('hidden');
                document.getElementById('hp-bar').style.width = "100%";
            }, 4000);
        }

        function renderList() {
            const list = document.getElementById('food-list');
            if(foods.length === 0) {
                list.innerHTML = `<div class="py-20 text-center opacity-40 text-sm italic">图鉴空空如也...</div>`;
                return;
            }
            list.innerHTML = foods.map((f, i) => `
                <div class="gb-panel p-3 flex justify-between items-center bg-white mb-2 shadow-none animate__animated animate__fadeInUp">
                    <div>
                        <div class="font-bold mb-1 tracking-tighter">${f.name}</div>
                        <div class="flex gap-1">${f.tags.map(t => `<span class="text-[10px] border border-dark px-1 bg-gray-50">${t}</span>`).join('')}</div>
                    </div>
                    <button onclick="deleteFood(${i})" class="text-red-500 font-bold px-2 text-xs">丢弃 X</button>
                </div>
            `).join('');
        }

        function triggerCamera() { document.getElementById('camera-file').click(); }
        function processPhoto(e) {
            const loading = document.getElementById('ai-loading');
            document.getElementById('preview-img').src = URL.createObjectURL(e.target.files[0]);
            loading.classList.remove('hidden');
            setTimeout(() => { 
                loading.classList.add('hidden'); 
                document.getElementById('new-name').value = "AI发现的美食"; 
            }, 2500);
        }

        function saveFood() {
            const name = document.getElementById('new-name').value;
            const checkedTags = Array.from(document.querySelectorAll('.tag-checkbox:checked')).map(cb => cb.value);
            if(name) {
                foods.unshift({ name, tags: checkedTags });
                saveAndSync();
                closeModal();
                document.getElementById('new-name').value = "";
                document.querySelectorAll('.tag-checkbox').forEach(cb => cb.checked = false);
            }
        }

        function deleteFood(i) {
            if(confirm("丢弃这份灵感？")) {
                foods.splice(i, 1);
                saveAndSync();
            }
        }

        function saveAndSync() {
            localStorage.setItem('my_food_v4', JSON.stringify(foods));
            updateWheel();
            if(!document.getElementById('page-list').classList.contains('hidden')) renderList();
        }

        function nav(p) {
            document.getElementById('page-home').classList.toggle('hidden', p !== 'home');
            document.getElementById('page-list').classList.toggle('hidden', p !== 'list');
            if(p === 'list') renderList();
            document.getElementById('nav-home').classList.toggle('bg-white', p === 'home');
            document.getElementById('nav-list').classList.toggle('bg-white', p === 'list');
        }

        function openModal() { document.getElementById('add-modal').classList.remove('hidden'); }
        function closeModal() { document.getElementById('add-modal').classList.add('hidden'); }
        
        window.onload = updateWheel;
    </script>
</body>
</html>
