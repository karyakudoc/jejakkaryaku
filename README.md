<!doctype html>
<html lang="id"><head><script src="/_sdk/telemetry_sdk.js"></script>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Jejak Karya</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.263.0/dist/umd/lucide.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;700&amp;family=Fraunces:wght@600;700&amp;display=swap" rel="stylesheet">
  <style>
    body { font-family: 'DM Sans', sans-serif; }
    .font-display { font-family: 'Fraunces', serif; }
    .modal-backdrop { background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); }
    .card-item { transition: transform 0.3s cubic-bezier(.34,1.56,.64,1), box-shadow 0.3s; }
    .card-item:hover { transform: translateY(-8px) rotate(-1deg); box-shadow: 0 16px 32px rgba(0,0,0,0.15); }
    @keyframes slideUp { from { opacity:0; transform: translateY(20px); } to { opacity:1; transform: translateY(0); } }
    .animate-in { animation: slideUp 0.4s ease forwards; opacity: 0; }
    .hero-gradient { background: linear-gradient(135deg, rgba(251,113,133,0.7), rgba(139,92,246,0.6), rgba(59,130,246,0.5)); }
    .tag-seni { background: #fde68a; color: #92400e; }
    .tag-prakarya { background: #a7f3d0; color: #065f46; }
    .tag-puisi { background: #c4b5fd; color: #4c1d95; }
    .tag-lainnya { background: #fecaca; color: #991b1b; }
  </style>
  <script src="/_sdk/resizing_sdk.js" type="text/javascript"></script>
 </head>
 <body data-template-id="__page-root" class="min-h-screen" style="background: linear-gradient(rgb(253, 244, 255), rgb(239, 246, 255), rgb(254, 252, 232));">
  <header class="relative overflow-hidden"><img data-template-id="hero-image" class="canva-image w-full h-56 sm:h-72 object-cover" loading="lazy" src="https://images.pexels.com/photos/8467274/pexels-photo-8467274.jpeg?auto=compress&amp;cs=tinysrgb&amp;w=1280" alt="Two children showcasing colorful paintings in an art classroom setting.">
   <div class="absolute inset-0 hero-gradient flex items-end p-6 sm:p-8">
    <div>
     <h1 data-template-id="app-title" class="canva-text font-display drop-shadow-lg" style="color: rgb(255, 255, 255); font-weight: 700; font-style: normal; font-size: 36px;">✨ Jejak Karya</h1>
     <p data-template-id="app-subtitle" class="canva-text mt-1 drop-shadow" style="color: rgb(255, 255, 255); font-weight: 400; font-style: normal; font-size: 16px;">Galeri Portofolio Siswa</p>
    </div>
   </div>
  </header>
  <main class="max-w-6xl mx-auto px-4 py-8">
   <div class="flex flex-wrap items-center gap-3 mb-6"><select id="filter-category" class="px-4 py-2.5 rounded-full border-2 border-purple-200 text-sm bg-white font-medium focus:border-purple-400 focus:outline-none"> <option value="">🎨 Semua Kategori</option> <option value="Seni Rupa">🖼️ Seni Rupa</option> <option value="Prakarya">✂️ Prakarya</option> <option value="Puisi">📝 Puisi</option> <option value="Lainnya">⭐ Lainnya</option> </select> <button data-template-id="btn-add" class="canva-button ml-auto px-5 py-2.5 rounded-full font-medium text-sm flex items-center gap-2 shadow-lg shadow-pink-200/50 hover:scale-105 transition-transform" onclick="toggleForm()" style="background: linear-gradient(135deg, rgb(236, 72, 153), rgb(139, 92, 246)); color: rgb(255, 255, 255); font-weight: 600; font-style: normal; font-size: 16px;">＋ Tambah Karya</button>
   </div>
   <div id="add-form" class="hidden mb-8">
    <form data-template-id="form-section" class="canva-card p-6 rounded-2xl space-y-4 border-2 border-dashed border-purple-200" onsubmit="submitKarya(event)" style="background: rgb(255, 255, 255);">
     <h2 data-template-id="form-title" class="canva-text font-display" style="color: rgb(17, 24, 39); font-weight: 600; font-style: normal; font-size: 24px;">🎨 Tambah Karya Baru</h2>
     <div class="grid sm:grid-cols-2 gap-4">
      <div>
       <label data-template-id="label-nama" class="canva-text text-sm font-medium block mb-1" for="inp-nama" style="color: rgb(55, 65, 81); font-weight: 400; font-style: normal; font-size: 16px;">Nama Siswa</label><input id="inp-nama" type="text" required="" class="w-full px-4 py-2.5 border-2 border-gray-200 rounded-xl text-sm focus:border-purple-400 focus:outline-none" data-template-id="input-nama" placeholder="Masukkan nama..." style="font-weight: 400; font-style: normal; font-size: 16px;">
      </div>
      <div>
       <label data-template-id="label-kelas" class="canva-text text-sm font-medium block mb-1" for="inp-kelas" style="color: rgb(55, 65, 81); font-weight: 400; font-style: normal; font-size: 16px;">Kelas</label><select id="inp-kelas" required="" class="w-full px-4 py-2.5 border-2 border-gray-200 rounded-xl text-sm focus:border-purple-400 focus:outline-none"><option value="">Pilih Kelas</option><option>Kelas 1</option><option>Kelas 2</option><option>Kelas 3</option><option>Kelas 4</option><option>Kelas 5</option><option>Kelas 6</option></select>
      </div>
     </div>
     <div>
      <label data-template-id="label-judul" class="canva-text text-sm font-medium block mb-1" for="inp-judul" style="color: rgb(55, 65, 81); font-weight: 400; font-style: normal; font-size: 16px;">Judul Karya</label><input id="inp-judul" type="text" required="" class="w-full px-4 py-2.5 border-2 border-gray-200 rounded-xl text-sm focus:border-purple-400 focus:outline-none" data-template-id="input-judul" placeholder="Masukkan judul..." style="font-weight: 400; font-style: normal; font-size: 16px;">
     </div>
     <div>
      <label data-template-id="label-kategori" class="canva-text text-sm font-medium block mb-1" for="inp-kategori" style="color: rgb(55, 65, 81); font-weight: 400; font-style: normal; font-size: 16px;">Kategori</label><select id="inp-kategori" required="" class="w-full px-4 py-2.5 border-2 border-gray-200 rounded-xl text-sm focus:border-purple-400 focus:outline-none"><option value="">Pilih Kategori</option><option>Seni Rupa</option><option>Prakarya</option><option>Puisi</option><option>Lainnya</option></select>
     </div>
     <div>
      <label data-template-id="label-deskripsi" class="canva-text text-sm font-medium block mb-1" for="inp-desc" style="color: rgb(55, 65, 81); font-weight: 400; font-style: normal; font-size: 16px;">Deskripsi</label><textarea id="inp-desc" rows="3" class="w-full px-4 py-2.5 border-2 border-gray-200 rounded-xl text-sm focus:border-purple-400 focus:outline-none" data-template-id="input-deskripsi" style="font-weight: 400; font-style: normal; font-size: 16px;">Ceritakan tentang karya ini...</textarea>
     </div>
     <div class="flex gap-3"><button data-template-id="btn-submit" type="submit" class="canva-button px-5 py-2.5 rounded-full font-medium text-sm shadow-md hover:scale-105 transition-transform" style="background: linear-gradient(135deg, rgb(16, 185, 129), rgb(6, 182, 212)); color: rgb(255, 255, 255); font-weight: 600; font-style: normal; font-size: 16px;">💾 Simpan</button> <button data-template-id="btn-cancel" type="button" class="canva-button px-5 py-2.5 rounded-full font-medium text-sm" onclick="toggleForm()" style="background: rgb(243, 232, 255); color: rgb(124, 58, 237); font-weight: 400; font-style: normal; font-size: 16px;">Batal</button>
     </div>
     <div id="form-status" class="text-sm hidden"></div>
    </form>
   </div>
   <div id="gallery" class="grid sm:grid-cols-2 lg:grid-cols-3 gap-5"></div>
   <div id="empty-state" class="text-center py-16 hidden"><img data-template-id="empty-image" class="canva-image w-44 h-44 object-cover rounded-3xl mx-auto mb-5 shadow-lg" loading="lazy" src="https://images.pexels.com/photos/7898415/pexels-photo-7898415.jpeg?auto=compress&amp;cs=tinysrgb&amp;w=400" alt="Children's hands holding paintbrushes while painting with watercolors during a creative art session.">
    <p data-template-id="empty-text" class="canva-text text-lg" style="color: rgb(107, 114, 128); font-weight: 400; font-style: italic; font-size: 16px;">Belum ada karya. Yuk tambahkan! 🌈</p>
   </div>
  </main>
  <div id="modal" class="fixed inset-0 modal-backdrop z-50 hidden flex items-center justify-center p-4">
   <div data-template-id="modal-bg" class="canva-card w-full max-w-lg rounded-3xl p-6 max-h-[80vh] overflow-y-auto relative shadow-2xl" style="background: rgb(255, 255, 255);"><button onclick="closeModal()" class="absolute top-4 right-4 w-8 h-8 rounded-full bg-gray-100 flex items-center justify-center hover:bg-gray-200 transition-colors"><i data-lucide="x" style="width:16px;height:16px"></i></button>
    <div id="modal-category" class="inline-block text-xs font-bold uppercase tracking-wider px-3 py-1 rounded-full mb-2"></div>
    <h2 id="modal-title" class="font-display text-xl font-bold text-gray-900"></h2>
    <p id="modal-meta" class="text-sm text-gray-500 mt-1"></p>
    <p id="modal-desc" class="text-gray-700 mt-4 text-sm leading-relaxed"></p>
    <hr class="my-4 border-dashed border-purple-200">
    <h3 data-template-id="comments-title" class="canva-text font-medium text-sm mb-3" style="color: rgb(17, 24, 39); font-weight: 600; font-style: normal; font-size: 19px;">💬 Komentar</h3>
    <div id="modal-comments" class="space-y-2 mb-4"></div>
    <form onsubmit="submitComment(event)" class="flex gap-2"><input id="inp-comment" type="text" required="" class="flex-1 px-4 py-2.5 border-2 border-gray-200 rounded-full text-sm focus:border-purple-400 focus:outline-none" data-template-id="input-comment" placeholder="Tulis komentar..." style="font-weight: 400; font-style: normal; font-size: 16px;"> <button data-template-id="btn-comment" type="submit" class="canva-button px-4 py-2.5 rounded-full text-sm font-medium shadow-md hover:scale-105 transition-transform" style="background: linear-gradient(135deg, rgb(139, 92, 246), rgb(59, 130, 246)); color: rgb(255, 255, 255); font-weight: 600; font-style: normal; font-size: 16px;">Kirim</button>
    </form>
   </div>
  </div>
  <template id="card-template">
   <div data-template-id="karya-card" class="canva-card card-item rounded-2xl p-5 cursor-pointer border-2 border-transparent hover:border-purple-200">
    <div class="flex items-start justify-between"><span data-template-id="card-category" class="canva-tag text-xs px-3 py-1 rounded-full font-bold"></span> <span class="text-xs text-gray-400 card-date"></span>
    </div>
    <h3 data-template-id="card-title" class="canva-text font-semibold mt-3 text-base card-title-text"></h3>
    <p class="text-sm text-gray-500 mt-1 card-meta"></p>
    <p class="text-sm text-gray-600 mt-2 line-clamp-2 card-desc"></p>
   </div>
  </template>
  <script src="/_sdk/editing_sdk.js"></script>
  <script src="/_sdk/data_sdk.js"></script>
  <script>
    let allData = [];
    let currentKarya = null;

    const dataHandler = {
      onDataChanged(data) {
        allData = data;
        renderGallery();
      }
    };

    async function initApp() {
      const r = await window.dataSdk.init(dataHandler);
      if (!r.isOk) console.error('SDK init failed');
      lucide.createIcons();
    }
    initApp();

    function toggleForm() {
      document.getElementById('add-form').classList.toggle('hidden');
    }

    function getCategoryClass(cat) {
      const map = { 'Seni Rupa': 'tag-seni', 'Prakarya': 'tag-prakarya', 'Puisi': 'tag-puisi', 'Lainnya': 'tag-lainnya' };
      return map[cat] || 'tag-lainnya';
    }

    function renderGallery() {
      const filter = document.getElementById('filter-category').value;
      const karyas = allData.filter(d => d.type === 'karya' && (!filter || d.category === filter));
      const gallery = document.getElementById('gallery');
      const empty = document.getElementById('empty-state');

      if (karyas.length === 0) { gallery.innerHTML = ''; empty.classList.remove('hidden'); return; }
      empty.classList.add('hidden');

      const existing = new Map([...gallery.children].map(el => [el.dataset.id, el]));
      const sorted = karyas.sort((a, b) => (b.created_at || '').localeCompare(a.created_at || ''));
      const fragment = document.createDocumentFragment();

      sorted.forEach((item, i) => {
        let el = existing.get(item.__backendId);
        if (el) { updateCard(el, item); existing.delete(item.__backendId); }
        else { el = createCard(item, i); }
        fragment.appendChild(el);
      });
      existing.forEach(el => el.remove());
      gallery.appendChild(fragment);
    }

    function createCard(item, index) {
      const tpl = document.getElementById('card-template');
      const clone = tpl.content.cloneNode(true);
      const div = clone.firstElementChild;
      div.dataset.id = item.__backendId;
      div.classList.add('animate-in');
      div.style.animationDelay = `${index * 0.05}s`;
      div.addEventListener('click', () => openModal(item.__backendId));
      updateCard(div, item);
      return div;
    }

    function updateCard(el, item) {
      el.querySelector('.card-title-text').textContent = item.title || '';
      el.querySelector('.card-meta').textContent = `${item.nama} • ${item.kelas}`;
      el.querySelector('.card-desc').textContent = item.description || '';
      el.querySelector('.card-date').textContent = item.created_at ? new Date(item.created_at).toLocaleDateString('id-ID') : '';
      const cat = el.querySelector('[data-template-id="card-category"]');
      cat.textContent = item.category || '';
      cat.className = `canva-tag text-xs px-3 py-1 rounded-full font-bold ${getCategoryClass(item.category)}`;
    }

    function openModal(id) {
      currentKarya = allData.find(d => d.__backendId === id);
      if (!currentKarya) return;
      document.getElementById('modal-title').textContent = currentKarya.title;
      document.getElementById('modal-meta').textContent = `${currentKarya.nama} • ${currentKarya.kelas}`;
      document.getElementById('modal-desc').textContent = currentKarya.description || '';
      const catEl = document.getElementById('modal-category');
      catEl.textContent = currentKarya.category || '';
      catEl.className = `inline-block text-xs font-bold uppercase tracking-wider px-3 py-1 rounded-full mb-2 ${getCategoryClass(currentKarya.category)}`;
      renderComments();
      document.getElementById('modal').classList.remove('hidden');
    }

    function closeModal() { document.getElementById('modal').classList.add('hidden'); currentKarya = null; }

    function renderComments() {
      const container = document.getElementById('modal-comments');
      const comments = allData.filter(d => d.type === 'comment' && d.title === currentKarya.title);
      container.innerHTML = comments.map(c => `<div class="bg-purple-50 rounded-xl px-4 py-2.5 text-sm"><span class="font-semibold text-purple-700">${c.nama || 'Anonim'}</span>: ${c.comment}</div>`).join('');
    }

    async function submitKarya(e) {
      e.preventDefault();
      if (allData.length >= 999) { showStatus('Batas data tercapai (999).', true); return; }
      const btn = e.target.querySelector('[data-template-id="btn-submit"]');
      btn.disabled = true; btn.style.opacity = '0.5';
      const record = { type: 'karya', nama: document.getElementById('inp-nama').value, kelas: document.getElementById('inp-kelas').value, title: document.getElementById('inp-judul').value, category: document.getElementById('inp-kategori').value, description: document.getElementById('inp-desc').value || '', comment: '', created_at: new Date().toISOString() };
      const r = await window.dataSdk.create(record);
      btn.disabled = false; btn.style.opacity = '1';
      if (r.isOk) { e.target.reset(); toggleForm(); }
      else showStatus('Gagal menyimpan.', true);
    }

    async function submitComment(e) {
      e.preventDefault();
      if (allData.length >= 999) return;
      const inp = document.getElementById('inp-comment');
      const record = { type: 'comment', nama: '', kelas: '', title: currentKarya.title, description: '', category: '', comment: inp.value, created_at: new Date().toISOString() };
      const r = await window.dataSdk.create(record);
      if (r.isOk) { inp.value = ''; renderComments(); }
    }

    function showStatus(msg, isError) {
      const el = document.getElementById('form-status');
      el.textContent = msg;
      el.className = `text-sm ${isError ? 'text-red-600' : 'text-green-600'}`;
      el.classList.remove('hidden');
      setTimeout(() => el.classList.add('hidden'), 3000);
    }

    document.getElementById('filter-category').addEventListener('change', renderGallery);
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'a0fd40c38439e793',t:'MTc4MjE1MjI3OS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script>
</body></html>
