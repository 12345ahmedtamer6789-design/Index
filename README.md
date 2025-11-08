<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>مدير المصاريف المتقدم 💰</title>

<!-- Chart.js for graphs -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Tajawal:wght@400;600&display=swap');
  :root{
    --bg: linear-gradient(135deg,#d7e1ec,#f8fbff);
    --card-bg:#fff;
    --text:#000;
    --accent:#007bff;
    --danger:#dc3545;
    --muted:#666;
  }
  body.dark{
    --bg: linear-gradient(135deg,#050010,#120025,#220045,#000);
    --card-bg:#1b002b;
    --text:#fff;
    --accent: linear-gradient(90deg,#ff004c,#7b00ff,#0014ff);
    --danger:#ff2e63;
    --muted:#bbb;
  }
  *{box-sizing:border-box}
  body{
    font-family:'Tajawal',sans-serif;
    margin:0;
    min-height:100vh;
    background:var(--bg);
    color:var(--text);
    direction:rtl;
    padding:18px;
    transition:0.3s;
  }
  .app{
    max-width:900px;
    margin:auto;
    display:grid;
    gap:18px;
    grid-template-columns: 1fr;
  }

  /* header */
  .header{
    display:flex;
    gap:10px;
    align-items:center;
    justify-content:space-between;
  }
  .brand{font-weight:700;font-size:20px}
  .controls{display:flex;gap:8px;align-items:center}

  /* main card */
  .card{
    background:var(--card-bg);
    padding:14px;
    border-radius:12px;
    box-shadow:0 6px 20px rgba(0,0,0,0.08);
  }

  /* form */
  .form-row{display:flex;gap:8px;flex-wrap:wrap}
  input,select,button,textarea{
    font-family:inherit;
    font-size:15px;
  }
  input[type="text"], input[type="number"], select, textarea{
    padding:10px;
    border-radius:8px;
    border:1px solid rgba(0,0,0,0.12);
    background:transparent;
    color:var(--text);
    flex:1 1 auto;
  }
  .btn{padding:10px 12px;border-radius:8px;border:none;cursor:pointer;background:var(--accent);color:#fff}
  .btn.ghost{background:transparent;border:1px solid rgba(0,0,0,0.08);color:var(--text)}
  .btn.warn{background:var(--danger)}

  /* list */
  .list{margin-top:12px;display:flex;flex-direction:column;gap:8px}
  .item{display:flex;justify-content:space-between;align-items:center;padding:10px;border-radius:8px;background:rgba(0,0,0,0.02)}
  .meta{font-size:13px;color:var(--muted)}
  .right{display:flex;gap:8px;align-items:center}

  /* sidebar small stats */
  .stats{display:flex;gap:8px;flex-wrap:wrap}
  .stat{flex:1 1 150px;padding:10px;border-radius:8px;background:rgba(0,0,0,0.03);text-align:center}
  .big{font-size:18px;font-weight:700}

  /* settings modal */
  .modal{
    position:fixed;inset:0;background:rgba(0,0,0,0.45);display:none;place-items:center;padding:20px;
  }
  .modal.open{display:grid}
  .modal .card{max-width:480px;width:100%}

  /* responsive */
  @media(min-width:800px){
    .app{grid-template-columns: 1fr 360px}
  }

  /* ad placeholder */
  .adbox{background:linear-gradient(90deg,#f0f0f0,#fafafa);padding:10px;border-radius:8px;border:1px dashed #ccc;text-align:center;color:var(--muted)}
  .small{font-size:13px}
  .danger-text{color:var(--danger)}

  /* widget link */
  .widget-link{display:block;text-align:center;margin-top:10px;color:var(--muted);text-decoration:underline}
</style>
</head>
<body>

<div class="app">

  <!-- Main column -->
  <div>
    <div class="header">
      <div class="brand">💼 مدير المصاريف المتقدّم</div>
      <div class="controls">
        <button class="btn ghost" id="openWidget">ويدجت (عرض سريع)</button>
        <button class="btn ghost" id="openSettings">الإعدادات ⚙️</button>
        <button class="btn" id="toggleTheme">🌙</button>
      </div>
    </div>

    <!-- Input card -->
    <div class="card" id="inputCard">
      <div style="display:flex;justify-content:space-between;align-items:center;gap:10px;flex-wrap:wrap">
        <div style="flex:1 1 60%">
          <div class="form-row" style="align-items:center">
            <select id="category">
              <option value="عام">عام</option>
              <option value="أكل">أكل</option>
              <option value="مواصلات">مواصلات</option>
              <option value="فواتير">فواتير</option>
              <option value="ترفيه">ترفيه</option>
              <option value="أخرى">أخرى</option>
            </select>
            <input type="text" id="desc" placeholder="وصف المصروف">
            <input type="number" id="amount" placeholder="المبلغ">
            <input type="date" id="date" style="max-width:160px">
            <button class="btn" onclick="addExpense()">أضف</button>
          </div>
        </div>

        <div style="flex:0 0 220px;text-align:center">
          <div class="small">إجمالي هذا الشهر</div>
          <div class="big" id="monthTotal">0 ج</div>
          <div class="meta" id="budgetInfo"></div>
        </div>
      </div>

      <div class="list" id="list"></div>
    </div>

    <!-- Graphs card -->
    <div class="card" style="margin-top:12px">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <div style="font-weight:600">📊 إحصائيات المصاريف</div>
        <div class="small meta">عرض حسب: <select id="graphRange"><option value="month">هذا الشهر</option><option value="year">هذا العام</option></select></div>
      </div>
      <canvas id="chart" style="width:100%;height:220px;margin-top:12px"></canvas>
    </div>

    <!-- Export / Import / Ads placeholder -->
    <div style="display:flex;gap:12px;margin-top:12px;flex-wrap:wrap">
      <div class="card" style="flex:1 1 300px">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div style="font-weight:600">🔁 النسخ الاحتياطي / الاستيراد</div>
          <div class="small meta">حفظ محليًا</div>
        </div>
        <div style="margin-top:10px;display:flex;gap:8px;flex-wrap:wrap">
          <button class="btn ghost" onclick="exportJSON()">تصدير JSON</button>
          <button class="btn ghost" onclick="exportCSV()">تصدير CSV</button>
          <input type="file" id="fileImport" style="display:none" accept=".json,.csv" onchange="importFile(event)">
          <button class="btn" onclick="document.getElementById('fileImport').click()">استيراد ملف</button>
        </div>
      </div>

      <div class="card" style="flex:1 1 240px">
        <div style="font-weight:600">🧾 إعلان (مكان جاهز)</div>
        <div class="adbox small">
          هنا تحط كود الإعلانات (AdSense) لو الموقع<br>
          لو هتحوّل التطبيق لــ APK هتضيف AdMob داخل الـ WebView أو مباشرة في التطبيق.
        </div>
      </div>
    </div>

  </div>

  <!-- Right column -->
  <div>
    <div class="card">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <div style="font-weight:600">الملخص السريع</div>
        <div class="small meta">تحديث فوري</div>
      </div>

      <div style="margin-top:10px" class="stats">
        <div class="stat">
          <div class="small meta">إجمالي المسجل</div>
          <div class="big" id="totalAll">0 ج</div>
        </div>
        <div class="stat">
          <div class="small meta">أكبر مصروف</div>
          <div class="big" id="maxExpense">0 ج</div>
        </div>
        <div class="stat">
          <div class="small meta">عدد العناصر</div>
          <div class="big" id="count">0</div>
        </div>
      </div>

      <div style="margin-top:12px">
        <button class="btn ghost" onclick="clearAll()">مسح الكل</button>
        <button class="btn warn" onclick="promptDeleteOld()">مسح القديمة</button>
      </div>
    </div>

    <div class="card" style="margin-top:12px">
      <div style="font-weight:600">الإعدادات السريعة</div>
      <div style="margin-top:10px;display:flex;flex-direction:column;gap:8px">
        <label class="small">تشغيل الاهتزاز عند الإضافة <input type="checkbox" id="optVibrate"></label>
        <label class="small">تشغيل إشعارات المتصفح <input type="checkbox" id="optNotify"></label>
        <label class="small">عرض ويدجت/صفحة سريعة <button class="btn ghost" onclick="openWidget()">افتح</button></label>
        <div class="small meta">ملاحظة: الإعدادات تحفظ تلقائياً.</div>
      </div>
      <div style="margin-top:12px">
        <div class="small meta">لتحويل لتطبيق (APK):</div>
        <div class="small">استعمل Capacitor / Cordova / Appmaker بعد اختبار التطبيق.</div>
      </div>
    </div>

  </div>

</div>

<!-- Settings modal (hidden) -->
<div id="modal" class="modal">
  <div class="card">
    <div style="display:flex;justify-content:space-between;align-items:center">
      <div style="font-weight:700">⚙️ الإعدادات المتقدمة</div>
      <div><button class="btn ghost" onclick="closeSettings()">إغلاق</button></div>
    </div>

    <div style="margin-top:10px;display:flex;flex-direction:column;gap:10px">
      <label>العملة:
        <input type="text" id="settingCurrency" placeholder="جنيه" style="max-width:120px">
      </label>

      <label>تعيين ميزانية شهرية:
        <input type="number" id="settingBudget" placeholder="مثلاً: 2000" style="max-width:160px">
      </label>

      <label>اليوم الذي يبدأ به الشهر المالي:
        <select id="settingMonthStart" style="max-width:160px">
          <option value="1">1</option><option value="5">5</option><option value="10">10</option>
          <option value="15">15</option><option value="20">20</option>
        </select>
      </label>

      <label>خيارات اللغة:
        <select id="settingLang" style="max-width:160px"><option value="ar">العربية</option><option value="en">English</option></select>
      </label>

      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="btn" onclick="saveSettings()">حفظ الإعدادات</button>
        <button class="btn ghost" onclick="resetSettings()">إعادة الإعدادات الافتراضية</button>
      </div>

      <hr>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="btn ghost" onclick="exportJSON()">تصدير نسخة احتياطية</button>
        <button class="btn warn" onclick="confirmClear()">مسح كل البيانات</button>
      </div>
    </div>
  </div>
</div>

<!-- Widget page (simple overlay) -->
<div id="widgetModal" class="modal">
  <div class="card" style="max-width:320px;text-align:center">
    <div style="font-weight:700">ويدجت — عرض سريع</div>
    <div style="margin-top:10px">
      <div class="small meta">إجمالي هذا الشهر</div>
      <div class="big" id="widgetTotal">0 ج</div>
      <div style="margin-top:8px" class="meta" id="widgetRemaining"></div>
      <a class="widget-link" href="#" onclick="closeWidget()">اغلق</a>
    </div>
  </div>
</div>

<script>
/* ==========================
   Data model + storage keys
   ========================== */
let STORAGE_KEY = 'exp_app_v1';
let SETTINGS_KEY = 'exp_app_settings_v1';

let data = {
  expenses: []  // each: {desc, amount, category, dateISO}
};
let settings = {
  currency: 'جنيه',
  darkMode: false,
  vibrate: true,
  notify: false,
  budget: 0,
  monthStart: 1,
  lang: 'ar'
};

/* Load saved */
function loadAll(){
  try{
    const saved = JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}');
    if(saved && saved.expenses) data = saved;
  }catch(e){ console.warn('no saved data') }
  try{
    const s = JSON.parse(localStorage.getItem(SETTINGS_KEY) || '{}');
    if(s) settings = Object.assign(settings, s);
  }catch(e){}
  // apply settings to UI
  document.getElementById('optVibrate').checked = !!settings.vibrate;
  document.getElementById('optNotify').checked = !!settings.notify;
  document.getElementById('settingCurrency').value = settings.currency;
  document.getElementById('settingBudget').value = settings.budget || '';
  document.getElementById('settingMonthStart').value = settings.monthStart;
  document.getElementById('settingLang').value = settings.lang;
  if(settings.darkMode) document.body.classList.add('dark');
}

/* Save */
function saveAll(){
  localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
}

/* Save settings */
function saveSettings(){
  settings.currency = document.getElementById('settingCurrency').value || settings.currency;
  settings.budget = Number(document.getElementById('settingBudget').value) || 0;
  settings.monthStart = Number(document.getElementById('settingMonthStart').value) || 1;
  settings.lang = document.getElementById('settingLang').value || 'ar';
  settings.vibrate = document.getElementById('optVibrate').checked;
  settings.notify = document.getElementById('optNotify').checked;
  localStorage.setItem(SETTINGS_KEY, JSON.stringify(settings));
  alert('تم حفظ الإعدادات');
  updateAllDisplays();
}

/* Reset settings */
function resetSettings(){
  if(!confirm('هل تريد إعادة الإعدادات الافتراضية؟')) return;
  localStorage.removeItem(SETTINGS_KEY);
  settings = {currency:'جنيه',darkMode:false,vibrate:true,notify:false,budget:0,monthStart:1,lang:'ar'};
  loadAll();
  updateAllDisplays();
}

/* ==========================
   Utils
   ========================== */
function formatCurrency(n){ return n + ' ' + (settings.currency || 'ج'); }
function todayISO(){ return new Date().toISOString().slice(0,10); }

/* ==========================
   Adding / showing expenses
   ========================== */
function addExpense(){
  const desc = document.getElementById('desc').value.trim();
  const amount = Number(document.getElementById('amount').value);
  const category = document.getElementById('category').value;
  const dateInput = document.getElementById('date').value || todayISO();
  if(!desc || !amount) return alert('ادخل الوصف والمبلغ');
  const item = {desc,amount,category,dateISO:dateInput};
  data.expenses.push(item);
  saveAll();
  document.getElementById('desc').value = '';
  document.getElementById('amount').value = '';
  document.getElementById('date').value = '';
  if(settings.vibrate && navigator.vibrate) navigator.vibrate(180);
  if(settings.notify) showNotification('تم إضافة مصروف', `${desc} - ${formatCurrency(amount)}`);
  updateAllDisplays();
}

/* delete single */
function deleteExpense(index){
  if(!confirm('حذف هذا المصروف؟')) return;
  data.expenses.splice(index,1);
  saveAll();
  updateAllDisplays();
}

/* clear all */
function clearAll(){
  if(!confirm('هل أنت متأكد من مسح كل المصروفات؟')) return;
  data.expenses = [];
  saveAll();
  updateAllDisplays();
}

/* delete older than N days example */
function promptDeleteOld(){
  const days = Number(prompt('احذف المصاريف الأقدم من (عدد أيام):', '365'));
  if(!days) return;
  const cutoff = Date.now() - days*24*60*60*1000;
  data.expenses = data.expenses.filter(e => new Date(e.dateISO).getTime() >= cutoff);
  saveAll();
  updateAllDisplays();
}

/* ==========================
   Export / Import
   ========================== */
function exportJSON(){
  const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = 'expenses_backup.json'; a.click();
  URL.revokeObjectURL(url);
}
function exportCSV(){
  const rows = [['الوصف','التصنيف','المبلغ','التاريخ']];
  data.expenses.forEach(e => rows.push([e.desc, e.category, e.amount, e.dateISO]));
  const txt = rows.map(r => r.map(c => `"${String(c).replace(/"/g,'""')}"`).join(',')).join('\n');
  const blob = new Blob([txt], {type:'text/csv'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href=url; a.download='expenses.csv'; a.click(); URL.revokeObjectURL(url);
}
function importFile(ev){
  const f = ev.target.files[0];
  if(!f) return;
  const reader = new FileReader();
  reader.onload = () => {
    try{
      if(f.name.endsWith('.json')){
        const obj = JSON.parse(reader.result);
        if(obj && obj.expenses) data = obj; else data = {expenses: obj.expenses || obj};
        saveAll();
        updateAllDisplays();
        alert('تم الاستيراد');
      } else {
        // CSV simple parse
        const text = reader.result;
        const lines = text.split('\n').map(l=>l.trim()).filter(Boolean);
        const newExp = [];
        for(let i=1;i<lines.length;i++){
          const cols = lines[i].split(',');
          newExp.push({desc: stripQuotes(cols[0]), category: stripQuotes(cols[1])||'عام', amount: Number(stripQuotes(cols[2])||0), dateISO: stripQuotes(cols[3])||todayISO()});
        }
        data.expenses = data.expenses.concat(newExp);
        saveAll(); updateAllDisplays(); alert('CSV تم استيراده');
      }
    }catch(err){ alert('حدث خطأ في الاستيراد'); console.error(err) }
  };
  reader.readAsText(f);
}
function stripQuotes(s){ return (s||'').replace(/^"|"$/g,'').trim(); }

/* ==========================
   Notifications
   ========================== */
function requestNotificationPermission(){
  if(!("Notification" in window)) return false;
  if(Notification.permission === 'granted') return true;
  if(Notification.permission !== 'denied'){
    Notification.requestPermission().then(p => {
      if(p === 'granted') settings.notify = true;
      document.getElementById('optNotify').checked = settings.notify;
      localStorage.setItem(SETTINGS_KEY, JSON.stringify(settings));
    });
  }
  return Notification.permission === 'granted';
}
function showNotification(title, body){
  if(!settings.notify) return;
  if(Notification.permission !== 'granted') requestNotificationPermission();
  if(Notification.permission === 'granted'){
    const n = new Notification(title, {body, icon: ''});
    setTimeout(()=> n.close(), 5000);
  }
}

/* ==========================
   Budget & calculations
   ========================== */
function getMonthPeriod(){
  // determine current period based on settings.monthStart
  const now = new Date();
  const startDay = settings.monthStart || 1;
  let start, end;
  if(now.getDate() >= startDay){
    start = new Date(now.getFullYear(), now.getMonth(), startDay);
    end = new Date(now.getFullYear(), now.getMonth()+1, startDay);
  } else {
    start = new Date(now.getFullYear(), now.getMonth()-1, startDay);
    end = new Date(now.getFullYear(), now.getMonth(), startDay);
  }
  return {start, end};
}

function computeTotals(){
  const totalAll = data.expenses.reduce((s,e)=>s+Number(e.amount),0);
  const maxExpense = data.expenses.reduce((m,e)=>Math.max(m,Number(e.amount)),0);
  const count = data.expenses.length;
  // month total
  const period = getMonthPeriod();
  const monthTotal = data.expenses.reduce((s,e)=>{
    const d = new Date(e.dateISO);
    return (d >= period.start && d < period.end) ? s+Number(e.amount) : s;
  },0);
  return {totalAll,maxExpense,count,monthTotal,period};
}

/* ==========================
   UI updates & rendering
   ========================== */
let chart=null;
function updateAllDisplays(){
  saveAll();
  // settings checkbox sync
  settings.vibrate = document.getElementById('optVibrate').checked;
  settings.notify = document.getElementById('optNotify').checked;
  localStorage.setItem(SETTINGS_KEY, JSON.stringify(settings));

  // list render
  const list = document.getElementById('list');
  const sorted = data.expenses.slice().sort((a,b)=> new Date(b.dateISO)-new Date(a.dateISO));
  list.innerHTML = sorted.map((e,i)=>`
    <div class="item">
      <div style="max-width:65%">
        <div style="font-weight:600">${escapeHtml(e.desc)}</div>
        <div class="meta">${escapeHtml(e.category)} — ${e.dateISO}</div>
      </div>
      <div class="right">
        <div style="font-weight:700">${formatCurrency(e.amount)}</div>
        <button class="btn ghost" onclick="deleteExpense(${data.expenses.indexOf(sorted[i])})">حذف</button>
      </div>
    </div>
  `).join('') || '<div class="meta">لا توجد مصاريف بعد</div>';

  // totals
  const totals = computeTotals();
  document.getElementById('totalAll').innerText = formatCurrency(totals.totalAll);
  document.getElementById('maxExpense').innerText = formatCurrency(totals.maxExpense || 0);
  document.getElementById('count').innerText = totals.count;
  document.getElementById('monthTotal').innerText = formatCurrency(totals.monthTotal);
  // budget info
  if(settings.budget && settings.budget>0){
    const rem = settings.budget - totals.monthTotal;
    const info = rem < 0 ? `تجاوزت الميزانية بـ ${formatCurrency(Math.abs(rem))}` : `المتبقي من الميزانية: ${formatCurrency(rem)}`;
    document.getElementById('budgetInfo').innerHTML = info;
    // notify if close or over
    if(totals.monthTotal >= settings.budget && settings.notify) showNotification('تنبيه: تجاوز الميزانية', `لقد تجاوزت ميزانيتك لشهرك (${formatCurrency(settings.budget)})`);
  } else {
    document.getElementById('budgetInfo').innerText = 'لم يتم تعيين ميزانية';
  }

  // widget
  document.getElementById('widgetTotal').innerText = formatCurrency(totals.monthTotal);
  const remaining = settings.budget ? (settings.budget - totals.monthTotal) : 0;
  document.getElementById('widgetRemaining').innerText = settings.budget ? `المتبقي: ${formatCurrency(remaining)}` : 'لا توجد ميزانية';

  // chart
  renderChart();
}

/* Chart rendering using Chart.js (category sums) */
function renderChart(){
  const range = document.getElementById('graphRange').value;
  const now = new Date();
  const categories = {};
  data.expenses.forEach(e=>{
    const d = new Date(e.dateISO);
    if(range==='month' && !(d.getFullYear()===now.getFullYear() && d.getMonth()===now.getMonth())) return;
    if(range==='year' && d.getFullYear()!==now.getFullYear()) return;
    categories[e.category] = (categories[e.category] || 0) + Number(e.amount);
  });
  const labels = Object.keys(categories);
  const values = labels.map(l=>categories[l]);
  const ctx = document.getElementById('chart').getContext('2d');
  if(chart) chart.destroy();
  chart = new Chart(ctx, {
    type:'doughnut',
    data:{labels, datasets:[{data:values}]},
    options:{plugins:{legend:{position:'bottom'}}}
  });
}

/* ==========================
   Helpers & small UI controls
   ========================== */
function escapeHtml(s){ return (s||'').replace(/[&<>"']/g, c=>({ '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;' })[c]); }

document.getElementById('toggleTheme').addEventListener('click', ()=>{
  document.body.classList.toggle('dark');
  settings.darkMode = document.body.classList.contains('dark');
  localStorage.setItem(SETTINGS_KEY, JSON.stringify(settings));
});

document.getElementById('openSettings').addEventListener('click', ()=>{ openSettings(); });
document.getElementById('openWidget').addEventListener('click', ()=>{ openWidget(); });
document.getElementById('graphRange').addEventListener('change', ()=> renderChart());
document.getElementById('optNotify').addEventListener('change', ()=> {
  if(document.getElementById('optNotify').checked) requestNotificationPermission();
});

/* Modal controls */
function openSettings(){ document.getElementById('modal').classList.add('open'); }
function closeSettings(){ document.getElementById('modal').classList.remove('open'); }
function openWidget(){ document.getElementById('widgetModal').classList.add('open'); }
function closeWidget(){ document.getElementById('widgetModal').classList.remove('open'); }

/* confirm clear used in modal */
function confirmClear(){ if(confirm('مسح كل البيانات للأبد؟')){ clearAll(); localStorage.removeItem(SETTINGS_KEY); alert('تم المسح'); } }

/* helper when clicking widget close */
document.getElementById('widgetModal').addEventListener('click', (e)=>{ if(e.target.id==='widgetModal') closeWidget(); });

/* Init */
loadAll();
updateAllDisplays();

/* ==========================
   Extra: open as PWA / widget note
   ========================== */
/*
  ملاحظة حول الويجت:
  - هذا التطبيق يحتوي على صفحة ويدجت بسيطة (زر "ويدجت" يفتح نافذة سريعة).
  - لجعلها تظهر على شاشة الهاتف كـ "ويدجت" حقيقي: حول المشروع إلى PWA (اضف manifest و service worker) أو استخدم أدوات مثل "PWA to APK".
  - بعد تحويله لتطبيق اندرويد يمكن إضافة ويدجت حقيقية من خلال كود Native (خارج نطاق HTML).
*/

/* ==========================
   Utility: request permissions on load if wanted
   ========================== */
if(settings.notify && Notification.permission !== 'granted') requestNotificationPermission();

</script>

</body>
</html>
