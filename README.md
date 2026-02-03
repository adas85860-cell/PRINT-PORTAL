<!DOCTYPE html>
<html lang="en" data-theme="default">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Studio Print Portal</title>
    
    <!-- Libraries -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/cropperjs/1.6.1/cropper.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/cropperjs/1.6.1/cropper.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        :root {
            --sidebar-width: 260px;
            --primary: #2563eb;
        }

        /* Themes */
        [data-theme="light"] { --bg: #f8fafc; --sidebar: #ffffff; --card: #ffffff; --text: #1e293b; --border: #e2e8f0; }
        [data-theme="dark"] { --bg: #0f172a; --sidebar: #1e293b; --card: #334155; --text: #f1f5f9; --border: #475569; }
        [data-theme="default"] { --bg: #f0f4ff; --sidebar: #ffffff; --card: #ffffff; --text: #1e3a8a; --border: #cbd5e1; }

        body { background-color: var(--bg); color: var(--text); font-family: 'Inter', sans-serif; transition: 0.3s; }
        
        .sidebar {
            width: var(--sidebar-width);
            height: 100vh;
            position: fixed;
            left: 0;
            top: 0;
            background: var(--sidebar);
            border-right: 1px solid var(--border);
            z-index: 50;
        }

        .main-content { margin-left: var(--sidebar-width); padding: 2rem; min-height: 100vh; }

        /* A4 Page Styling */
        .a4-canvas {
            width: 210mm;
            min-height: 297mm;
            background: white;
            margin: 0 auto;
            padding: 10mm;
            box-shadow: 0 0 15px rgba(0,0,0,0.1);
            display: flex;
            flex-wrap: wrap;
            align-content: flex-start;
            gap: 5mm;
        }

        /* Card Sizing (Standard CR-80) */
        .id-card-slot {
            width: 85.6mm;
            height: 54mm;
            border: 1px solid #eee;
            position: relative;
            background: #fff;
            overflow: hidden;
        }

        .id-card-slot img { width: 100%; height: 100%; object-fit: fill; }

        .photo-slot { border: 1px solid #ddd; object-fit: cover; }

        @media print {
            .no-print { display: none !important; }
            .main-content { margin-left: 0; padding: 0; }
            body { background: white !important; }
            .a4-canvas { box-shadow: none; margin: 0; border: none; padding: 10mm; }
            @page { size: A4; margin: 0; }
        }

        .loader { border: 3px solid #f3f3f3; border-top: 3px solid #3498db; border-radius: 50%; width: 20px; height: 20px; animation: spin 1s linear infinite; display: inline-block; vertical-align: middle; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360mm); } }
    </style>
</head>
<body>

    <!-- 1. Layout & Sidebar -->
    <aside class="sidebar no-print flex flex-col p-5 shadow-lg">
        <div class="mb-10 px-2 flex items-center gap-3">
            <div class="bg-blue-600 p-2 rounded-lg text-white">
                <i class="fas fa-print fa-lg"></i>
            </div>
            <h1 class="text-xl font-bold text-blue-600">STUDIO PORTAL</h1>
        </div>

        <nav class="space-y-1 flex-1">
            <button onclick="showSection('home')" class="w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-blue-50 transition">
                <i class="fas fa-home text-blue-500"></i> <span id="txt-home">Home</span>
            </button>
            <button onclick="location.reload()" class="w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-blue-50 transition">
                <i class="fas fa-sync-alt text-green-500"></i> <span id="txt-refresh">Refresh Page</span>
            </button>
            
            <div class="pt-6 pb-2 text-xs font-bold text-slate-400 uppercase px-4">Services</div>
            
            <button onclick="showSection('aadhaar')" class="w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-blue-50 transition">
                <i class="fas fa-id-card text-emerald-500"></i> <span>Aadhaar Tool</span>
            </button>
            <button onclick="showSection('pan')" class="w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-blue-50 transition">
                <i class="fas fa-file-invoice text-orange-500"></i> <span>PAN Card Tool</span>
            </button>
            <button onclick="showSection('photos')" class="w-full flex items-center gap-3 px-4 py-3 rounded-xl hover:bg-blue-50 transition">
                <i class="fas fa-images text-purple-500"></i> <span>Photo Maker</span>
            </button>
        </nav>

        <div class="mt-auto border-t pt-4 space-y-4">
            <div>
                <label class="text-[10px] font-bold text-slate-400 uppercase px-2">Language</label>
                <select onchange="changeLang(this.value)" class="w-full mt-1 p-2 rounded-lg border text-sm outline-none">
                    <option value="en">English</option>
                    <option value="bn">বাংলা (Bengali)</option>
                </select>
            </div>
            <div>
                <label class="text-[10px] font-bold text-slate-400 uppercase px-2">Theme Mode</label>
                <div class="grid grid-cols-3 gap-1 bg-slate-100 p-1 rounded-lg mt-1">
                    <button onclick="setTheme('default')" class="p-2 text-xs rounded hover:bg-white">Def</button>
                    <button onclick="setTheme('light')" class="p-2 text-xs rounded hover:bg-white">Light</button>
                    <button onclick="setTheme('dark')" class="p-2 text-xs rounded hover:bg-white">Dark</button>
                </div>
            </div>
        </div>
    </aside>

    <!-- 2. Main Content -->
    <main class="main-content">
        
        <!-- Action Buttons (Top Sticky) -->
        <div class="no-print flex justify-end gap-3 mb-6">
            <button onclick="window.print()" class="bg-blue-600 text-white px-6 py-2.5 rounded-xl font-bold shadow-lg hover:bg-blue-700 transition">
                <i class="fas fa-print mr-2"></i> Print A4 Sheet
            </button>
            <button onclick="clearA4()" class="bg-red-50 text-red-600 px-6 py-2.5 rounded-xl font-bold border border-red-100 hover:bg-red-100 transition">
                <i class="fas fa-trash-alt mr-2"></i> Clear Sheet
            </button>
        </div>

        <!-- Tool: Universal ID Card Tool (Aadhaar/PAN) -->
        <section id="sec-id-tool" class="tool-section">
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm no-print mb-8">
                <h2 id="tool-title" class="text-xl font-bold mb-4">Aadhaar / PAN / ID Card Cropper</h2>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <label class="block text-sm font-semibold mb-2">Upload Document (PDF or Image)</label>
                        <input type="file" id="id-input" accept="image/*,application/pdf" class="w-full p-2 border rounded-xl">
                        <div id="loading" class="hidden mt-2 text-sm text-blue-600">
                            <div class="loader mr-2"></div> Processing file...
                        </div>
                    </div>
                    <div id="crop-actions" class="hidden flex items-end gap-2">
                        <button onclick="addCroppedToA4()" class="bg-emerald-600 text-white px-6 py-2 rounded-xl font-bold hover:bg-emerald-700 w-full">
                            <i class="fas fa-plus mr-2"></i> Add Cropped Area to Sheet
                        </button>
                    </div>
                </div>

                <!-- Crop Area -->
                <div id="cropper-container" class="mt-6 hidden border rounded-xl bg-slate-50 overflow-hidden" style="max-height: 500px;">
                    <img id="image-to-crop" style="max-width: 100%;">
                </div>
            </div>
        </section>

        <!-- Tool: College Photo Maker -->
        <section id="sec-photos" class="tool-section hidden">
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm no-print mb-8">
                <h2 class="text-xl font-bold mb-4">College Photo Maker</h2>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <label class="block text-sm font-semibold mb-2">Upload Multiple Photos</label>
                        <input type="file" id="photo-input" multiple accept="image/*" class="w-full p-2 border rounded-xl">
                    </div>
                    <div>
                        <label class="block text-sm font-semibold mb-2">Layout Selection</label>
                        <select id="photo-layout" class="w-full p-2 border rounded-xl" onchange="applyPhotoLayout()">
                            <option value="1">1 Photo per Row</option>
                            <option value="2" selected>2 Photos per Row (Standard)</option>
                            <option value="3">3 Photos per Row</option>
                            <option value="4">4 Photos per Row</option>
                        </select>
                    </div>
                </div>
            </div>
        </section>

        <!-- A4 LIVE PREVIEW -->
        <div class="no-print mb-2 text-center">
            <span class="text-[10px] font-black uppercase tracking-widest text-slate-400">A4 Live Print Sheet Preview</span>
        </div>
        <div id="a4-page" class="a4-canvas">
            <!-- Cropped items and photos appear here -->
        </div>

    </main>

    <!-- Hidden Canvas for PDF Processing -->
    <canvas id="pdf-canvas" class="hidden"></canvas>

    <script>
        // PDF.js Config
        pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';

        let cropper;
        const idInput = document.getElementById('id-input');
        const photoInput = document.getElementById('photo-input');
        const imageToCrop = document.getElementById('image-to-crop');
        const cropperContainer = document.getElementById('cropper-container');
        const cropActions = document.getElementById('crop-actions');
        const a4Page = document.getElementById('a4-page');
        const loading = document.getElementById('loading');

        // --- SECTION TOGGLE ---
        function showSection(type) {
            document.getElementById('sec-id-tool').classList.add('hidden');
            document.getElementById('sec-photos').classList.add('hidden');
            
            if(type === 'home' || type === 'aadhaar' || type === 'pan') {
                document.getElementById('sec-id-tool').classList.remove('hidden');
                document.getElementById('tool-title').innerText = type === 'aadhaar' ? 'Aadhaar Card Tool' : (type === 'pan' ? 'PAN Card Tool' : 'Document Cropper');
            } else {
                document.getElementById('sec-photos').classList.remove('hidden');
            }
        }

        // --- PDF & IMAGE LOADING LOGIC ---
        idInput.onchange = async (e) => {
            const file = e.target.files[0];
            if (!file) return;

            loading.classList.remove('hidden');
            cropperContainer.classList.add('hidden');

            if (file.type === 'application/pdf') {
                const reader = new FileReader();
                reader.onload = async function() {
                    const typedarray = new Uint8Array(this.result);
                    const pdf = await pdfjsLib.getDocument(typedarray).promise;
                    const page = await pdf.getPage(1);
                    const viewport = page.getViewport({ scale: 2.5 });
                    const canvas = document.getElementById('pdf-canvas');
                    const context = canvas.getContext('2d');
                    canvas.height = viewport.height;
                    canvas.width = viewport.width;
                    await page.render({ canvasContext: context, viewport: viewport }).promise;
                    startCropper(canvas.toDataURL('image/png'));
                };
                reader.readAsArrayBuffer(file);
            } else {
                const reader = new FileReader();
                reader.onload = (ev) => startCropper(ev.target.result);
                reader.readAsDataURL(file);
            }
        };

        function startCropper(src) {
            imageToCrop.src = src;
            cropperContainer.classList.remove('hidden');
            cropActions.classList.remove('hidden');
            loading.classList.add('hidden');

            if (cropper) cropper.destroy();
            cropper = new Cropper(imageToCrop, {
                aspectRatio: 8.56 / 5.4,
                viewMode: 1,
                guides: true,
            });
        }

        // --- CROP & ADD TO A4 ---
        function addCroppedToA4() {
            if (!cropper) return;
            const canvas = cropper.getCroppedCanvas({ width: 1000 });
            const imgUrl = canvas.toDataURL('image/png');

            const div = document.createElement('div');
            div.className = 'id-card-slot shadow-sm';
            div.innerHTML = `
                <img src="${imgUrl}">
                <button onclick="this.parentElement.remove()" class="no-print absolute top-0 right-0 bg-red-500 text-white p-1 text-[8px]">REMOVE</button>
            `;
            a4Page.appendChild(div);
        }

        // --- PHOTO MAKER LOGIC ---
        photoInput.onchange = (e) => {
            Array.from(e.target.files).forEach(file => {
                const reader = new FileReader();
                reader.onload = (ev) => {
                    const img = document.createElement('img');
                    img.src = ev.target.result;
                    img.className = 'photo-slot';
                    applyPhotoLayout(img);
                    
                    const container = document.createElement('div');
                    container.className = 'relative inline-block';
                    container.appendChild(img);
                    container.innerHTML += `<button onclick="this.parentElement.remove()" class="no-print absolute top-0 right-0 bg-red-500 text-white p-1 text-[8px]">X</button>`;
                    
                    a4Page.appendChild(container);
                };
                reader.readAsDataURL(file);
            });
        };

        function applyPhotoLayout(specificImg = null) {
            const cols = document.getElementById('photo-layout').value;
            const width = (190 / cols) - 5; // A4 is 210mm wide, 20mm margin
            
            const imgs = specificImg ? [specificImg] : document.querySelectorAll('.photo-slot');
            imgs.forEach(img => {
                img.style.width = width + 'mm';
            });
        }

        // --- UTILS ---
        function clearA4() {
            if(confirm("Clear all items from the print sheet?")) a4Page.innerHTML = '';
        }

        function setTheme(theme) {
            document.documentElement.setAttribute('data-theme', theme);
        }

        function changeLang(lang) {
            const t = {
                en: { home: "Home", refresh: "Refresh Page" },
                bn: { home: "হোম", refresh: "পেজ রিফ্রেশ" }
            };
            document.getElementById('txt-home').innerText = t[lang].home;
            document.getElementById('txt-refresh').innerText = t[lang].refresh;
        }
    </script>
</body>
</html>
