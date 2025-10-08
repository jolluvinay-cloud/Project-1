<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Event Registration Portal</title>
  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --accent:#08a0f7; --muted:#9aa7b2; --glass: rgba(255,255,255,0.03);
      --radius:12px; --shadow: 0 6px 18px rgba(2,6,23,0.6);
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      background: linear-gradient(180deg,#071025 0%, #081224 60%);
      color:#e6eef6;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      padding:28px;
      display:flex;
      align-items:flex-start;
      justify-content:center;
      gap:24px;
      font-size:15px;
      line-height:1.4;
    }

    .container{
      max-width:1100px;
      width:100%;
      display:grid;
      grid-template-columns: 420px 1fr;
      gap:22px;
      align-items:start;
    }

    /* Card */
    .card{
      background:linear-gradient(180deg,var(--card), rgba(8,12,20,0.6));
      border-radius:var(--radius);
      box-shadow:var(--shadow);
      padding:20px;
      border:1px solid rgba(255,255,255,0.03);
    }

    header.portal{
      grid-column:1 / -1;
      display:flex;
      gap:18px;
      align-items:center;
      justify-content:space-between;
      padding:18px 22px;
      margin-bottom:6px;
      border-radius:var(--radius);
      background: linear-gradient(90deg, rgba(8,14,27,0.6), rgba(4,8,15,0.35));
      border: 1px solid rgba(255,255,255,0.03);
    }
    .event-info{display:flex; gap:14px; align-items:center;}
    .logo{
      width:72px; height:72px; border-radius:10px;
      background:linear-gradient(135deg,var(--accent), #6ad1ff);
      display:flex;align-items:center;justify-content:center;
      font-weight:700;color:#042033;font-size:20px;
      box-shadow:0 6px 18px rgba(8,160,247,0.12);
    }
    .event-text h1{margin:0;font-size:20px}
    .event-text p{margin:2px 0 0;color:var(--muted);font-size:13px}

    form .field{margin-bottom:12px}
    label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
    input[type="text"], input[type="email"], input[type="tel"], input[type="number"], select, textarea, input[type="date"]{
      width:100%;
      padding:10px 12px;
      border-radius:10px;
      border:1px solid rgba(255,255,255,0.03);
      background:var(--glass);
      color:inherit;
      outline:none;
      transition:box-shadow .12s ease, border-color .12s ease;
    }
    input:focus, textarea:focus, select:focus{
      box-shadow:0 6px 20px rgba(8,160,247,0.12);
      border-color: rgba(8,160,247,0.6);
    }
    .row{display:flex;gap:10px}
    .row .col{flex:1}

    .btn{
      display:inline-flex;align-items:center;gap:8px;
      background:linear-gradient(90deg,var(--accent), #33c6ff);
      color:#042033;padding:10px 14px;border-radius:10px;border:none;
      font-weight:600;cursor:pointer;
      box-shadow:0 8px 24px rgba(8,160,247,0.12);
    }
    .btn.ghost{
      background:transparent;color:var(--muted);
      border:1px solid rgba(255,255,255,0.04); box-shadow:none;
    }

    .small{font-size:13px;color:var(--muted)}
    .muted{color:var(--muted)}

    /* registrants table */
    .table-wrap{overflow:auto; margin-top:6px}
    table{width:100%; border-collapse:collapse; min-width:660px}
    th,td{padding:10px 12px; text-align:left; border-bottom:1px dashed rgba(255,255,255,0.03); font-size:14px}
    th{font-size:13px;color:var(--muted); font-weight:600}
    tr:hover td{background:linear-gradient(90deg, rgba(255,255,255,0.01), transparent)}

    .actions button{margin-right:8px}

    /* responsive */
    @media (max-width:980px){
      .container{grid-template-columns:1fr; padding-bottom:40px}
      header.portal{flex-direction:column;align-items:flex-start;gap:8px}
      body{padding:18px}
    }

    /* Toast & modal */
    .toast{
      position:fixed; right:20px; bottom:20px; background:#042033; padding:12px 16px; border-radius:10px;
      box-shadow:0 8px 30px rgba(0,0,0,0.6); color:#aef3ff; display:flex; gap:10px; align-items:center; z-index:60;
      transform:translateY(8px); opacity:0; transition:all .22s ease;
    }
    .toast.show{opacity:1; transform:none}
    .modal-backdrop{position:fixed; inset:0; background:rgba(2,6,23,0.6); display:flex; align-items:center; justify-content:center; z-index:100; }
    .modal{width:520px; max-width:92%; background:linear-gradient(180deg,#061022,#071426); padding:18px; border-radius:12px; box-shadow:var(--shadow)}
    .modal h3{margin:0 0 8px}
    .muted-small{font-size:13px;color:var(--muted)}

    /* tiny helpers */
    .flex{display:flex;align-items:center;gap:10px}
    .spacer{flex:1}
    .chip{padding:6px 10px;border-radius:999px;background:rgba(255,255,255,0.02);font-weight:600}
    .empty{padding:30px;text-align:center;color:var(--muted)}
  </style>
</head>
<body>
  <div class="container" role="main" aria-labelledby="portalTitle">
    <header class="portal card" aria-live="polite">
      <div class="event-info">
        <div class="logo" aria-hidden="true">EV</div>
        <div class="event-text">
          <h1 id="portalTitle">Tech Meetup 2025 — Registration</h1>
          <p id="eventDate" class="muted">Sat, Oct 25, 2025 · 10:00 AM — 5:00 PM · Downtown Conference Center</p>
        </div>
      </div>
      <div class="flex">
        <div class="chip" id="capacityChip">Capacity: <span id="capacity">200</span></div>
        <button class="btn ghost" id="editEventBtn" title="Edit event info">Edit event</button>
        <button class="btn" id="exportBtn" title="Export registrants CSV">Export CSV</button>
      </div>
    </header>

    <!-- left: registration form -->
    <section class="card" aria-labelledby="formTitle">
      <h2 id="formTitle" style="margin-top:0;margin-bottom:6px">Register</h2>
      <p class="small muted">Fill form to register an attendee. Required fields are marked with *</p>

      <form id="regForm" novalidate>
        <div class="row">
          <div class="col field">
            <label for="fullName">Full name *</label>
            <input id="fullName" name="fullName" type="text" required minlength="2" placeholder="Jane Doe" autocomplete="name" />
            <div class="small muted" id="nameHint">Enter first & last name.</div>
          </div>
          <div class="col field">
            <label for="ticketType">Ticket type *</label>
            <select id="ticketType" name="ticketType" required>
              <option value="">Select</option>
              <option value="General">General</option>
              <option value="Student">Student (50% off)</option>
              <option value="VIP">VIP</option>
            </select>
          </div>
        </div>

        <div class="row">
          <div class="col field">
            <label for="email">Email *</label>
            <input id="email" name="email" type="email" required placeholder="you@example.com" autocomplete="email" />
          </div>
          <div class="col field">
            <label for="phone">Phone</label>
            <input id="phone" name="phone" type="tel" placeholder="+91 98765 43210" autocomplete="tel" />
          </div>
        </div>

        <div class="row">
          <div class="col field">
            <label for="attendees">Number of seats *</label>
            <input id="attendees" name="attendees" type="number" required min="1" max="10" value="1" />
          </div>
          <div class="col field">
            <label for="regDate">Registration date *</label>
            <input id="regDate" name="regDate" type="date" required />
          </div>
        </div>

        <div class="field">
          <label for="notes">Notes (company, food preference)</label>
          <textarea id="notes" name="notes" rows="3" placeholder="Acme Corp — vegetarian"></textarea>
        </div>

        <div style="display:flex;gap:10px;align-items:center;margin-top:6px">
          <button class="btn" type="submit" id="submitBtn">Register attendee</button>
          <button class="btn ghost" type="button" id="resetFormBtn">Reset</button>
          <div class="spacer"></div>
          <div class="small muted" id="registeredCount">0 registered</div>
        </div>
      </form>
    </section>

    <!-- right: registrants list -->
    <section class="card" aria-labelledby="listTitle">
      <h2 id="listTitle" style="margin-top:0;margin-bottom:6px">Registrants</h2>
      <div class="small muted">Manage registrations below — edit, delete or export.</div>

      <div class="table-wrap" id="listContainer">
        <table aria-describedby="listTitle">
          <thead>
            <tr>
              <th>#</th><th>Name</th><th>Email</th><th>Ticket</th><th>Seats</th><th>Date</th><th class="muted-small">Actions</th>
            </tr>
          </thead>
          <tbody id="registrantsTbody">
            <tr class="empty-row"><td colspan="7" class="empty">No registrations yet — be the first!</td></tr>
          </tbody>
        </table>
      </div>
    </section>
  </div>

  <!-- modal for editing event info / confirmation -->
  <div id="modalRoot" aria-hidden="true"></div>

  <!-- toast -->
  <div id="toast" class="toast" role="status" aria-live="polite" aria-atomic="true" style="display:none">
    <strong id="toastTitle">Saved</strong>
    <span id="toastMsg" style="opacity:0.95;color:#bff0ff;font-weight:600"></span>
  </div>

  <script>
    // Simple app state & helpers
    const LS_KEY = 'event_reg_portal_v1';
    const eventInfo = {
      title: 'Tech Meetup 2025 — Registration',
      dateStr: 'Sat, Oct 25, 2025 · 10:00 AM — 5:00 PM · Downtown Conference Center',
      capacity: 200
    };

    // load saved registrants
    function loadData(){
      const raw = localStorage.getItem(LS_KEY);
      if(!raw) return {registrants: []};
      try { return JSON.parse(raw); } catch(e) { return {registrants: []}; }
    }
    function saveData(data){
      localStorage.setItem(LS_KEY, JSON.stringify(data));
    }

    // UI elements
    const form = document.getElementById('regForm');
    const tbody = document.getElementById('registrantsTbody');
    const registeredCount = document.getElementById('registeredCount');
    const exportBtn = document.getElementById('exportBtn');
    const editEventBtn = document.getElementById('editEventBtn');
    const modalRoot = document.getElementById('modalRoot');
    const toast = document.getElementById('toast');
    const toastTitle = document.getElementById('toastTitle');
    const toastMsg = document.getElementById('toastMsg');
    const capacitySpan = document.getElementById('capacity');

    let store = loadData();
    store.registrants = store.registrants || [];

    // utilities
    const uid = () => 'r_' + Math.random().toString(36).slice(2,9);
    function showToast(title,msg,timeout=2200){
      toastTitle.textContent = title;
      toastMsg.textContent = ' ' + (msg||'');
      toast.style.display = 'flex';
      // trigger animation
      setTimeout(()=>toast.classList.add('show'),20);
      setTimeout(()=>{ toast.classList.remove('show'); }, timeout);
      setTimeout(()=>{ toast.style.display='none'; }, timeout+300);
    }

    // render table
    function renderRegistrants(){
      const items = store.registrants;
      capacitySpan.textContent = eventInfo.capacity;
      registeredCount.textContent = items.length + ' registered';
      if(!items.length){
        tbody.innerHTML = '<tr class="empty-row"><td colspan="7" class="empty">No registrations yet — be the first!</td></tr>';
        return;
      }
      tbody.innerHTML = items.map((r, i) => {
        const date = new Date(r.regDate).toLocaleDateString();
        return `<tr data-id="${r.id}">
          <td>${i+1}</td>
          <td>${escapeHTML(r.fullName)}</td>
          <td>${escapeHTML(r.email)}</td>
          <td>${escapeHTML(r.ticketType)}</td>
          <td>${escapeHTML(String(r.attendees))}</td>
          <td>${escapeHTML(r.regDate)}</td>
          <td class="actions">
            <button class="btn ghost" data-action="edit" aria-label="Edit ${escapeHTML(r.fullName)}">Edit</button>
            <button class="btn ghost" data-action="delete" aria-label="Delete ${escapeHTML(r.fullName)}">Delete</button>
          </td>
        </tr>`;
      }).join('');
    }

    function escapeHTML(s = '') {
      return String(s)
        .replace(/&/g,'&amp;')
        .replace(/</g,'&lt;')
        .replace(/>/g,'&gt;')
        .replace(/"/g,'&quot;');
    }

    // initial render
    renderRegistrants();

    // form submit
    form.addEventListener('submit', (e) => {
      e.preventDefault();
      if(!form.checkValidity()){
        // trigger HTML5 validation UI
        form.reportValidity();
        return;
      }

      const data = new FormData(form);
      const registrant = {
        id: uid(),
        fullName: data.get('fullName').trim(),
        email: data.get('email').trim(),
        phone: data.get('phone')?.trim()||'',
        ticketType: data.get('ticketType'),
        attendees: Number(data.get('attendees')||1),
        regDate: data.get('regDate'),
        notes: data.get('notes')||'',
        createdAt: new Date().toISOString()
      };

      // capacity check (simple)
      const totalSeats = store.registrants.reduce((s,r)=>s + Number(r.attendees),0) + registrant.attendees;
      if(totalSeats > eventInfo.capacity){
        showModal({
          title: 'Capacity exceeded',
          message: `Adding ${registrant.attendees} seats would exceed capacity (${eventInfo.capacity}). Current booked: ${totalSeats - registrant.attendees}.`,
          actions: [{label:'Close',fn:closeModal}]
        });
        return;
      }

      store.registrants.push(registrant);
      saveData(store);
      renderRegistrants();
      showToast('Registered', `${registrant.fullName} — ${registrant.ticketType}`);
      form.reset();
      // default attendees back to 1
      document.getElementById('attendees').value = 1;
    });

    // Reset button
    document.getElementById('resetFormBtn').addEventListener('click', ()=> form.reset());

    // Delegated edit/delete actions
    tbody.addEventListener('click', (e)=>{
      const action = e.target.closest('button')?.dataset?.action;
      if(!action) return;
      const tr = e.target.closest('tr');
      const id = tr?.dataset?.id;
      if(!id) return;
      const idx = store.registrants.findIndex(x=>x.id === id);
      if(idx === -1) return;

      if(action === 'delete'){
        showModal({
          title: 'Delete registration?',
          message: `Remove ${store.registrants[idx].fullName} from registrants? This action cannot be undone.`,
          actions: [
            {label:'Cancel', fn: closeModal},
            {label:'Delete', fn: ()=> {
              store.registrants.splice(idx,1);
              saveData(store);
              renderRegistrants();
              closeModal();
              showToast('Deleted', 'Registration removed.');
            }, danger:true}
          ]
        });
      }

      if(action === 'edit'){
        // pre-fill form and open small modal to confirm editing
        const r = store.registrants[idx];
        showModal({
          title: 'Edit registration',
          content: renderEditForm(r),
          onMount: ()=> {
            // wire submit inside modal
            const editForm = document.getElementById('editRegForm');
            editForm.addEventListener('submit', evt => {
              evt.preventDefault();
              if(!editForm.checkValidity()){ editForm.reportValidity(); return; }
              const formData = new FormData(editForm);
              r.fullName = formData.get('fullName').trim();
              r.email = formData.get('email').trim();
              r.phone = formData.get('phone')?.trim()||'';
              r.ticketType = formData.get('ticketType');
              r.attendees = Number(formData.get('attendees')||1);
              r.regDate = formData.get('regDate');
              r.notes = formData.get('notes')||'';
              saveData(store);
              renderRegistrants();
              closeModal();
              showToast('Updated','Registration updated.');
            });
          },
          actions: [
            {label:'Close', fn: closeModal}
          ]
        });
      }
    });

    // Export CSV
    exportBtn.addEventListener('click', ()=> {
      if(!store.registrants.length){
        showToast('No data','No registrants to export.');
        return;
      }
      const csv = toCSV(store.registrants);
      const blob = new Blob([csv], {type:'text/csv;charset=utf-8;'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `registrants_${new Date().toISOString().slice(0,10)}.csv`;
      document.body.appendChild(a);
      a.click();
      a.remove();
      URL.revokeObjectURL(url);
      showToast('Exported','CSV downloaded.');
    });

    function toCSV(items){
      const keys = ['id','fullName','email','phone','ticketType','attendees','regDate','notes','createdAt'];
      const header = keys.join(',');
      const rows = items.map(it => keys.map(k => `"${String(it[k]||'').replace(/"/g,'""')}"`).join(','));
      return [header, ...rows].join('\n');
    }

    // Edit event (open modal to change title/date/capacity)
    editEventBtn.addEventListener('click', ()=>{
      showModal({
        title: 'Edit event details',
        content: `<form id="editEventForm" style="display:grid;gap:10px">
          <label class="muted-small">Event title<input id="evTitle" name="evTitle" required type="text" value="${escapeHTML(eventInfo.title)}" style="width:100%;padding:8px;border-radius:8px;background:transparent;border:1px solid rgba(255,255,255,0.04)"></label>
          <label class="muted-small">Event description / date<input id="evDateStr" name="evDateStr" required type="text" value="${escapeHTML(eventInfo.dateStr)}" style="width:100%;padding:8px;border-radius:8px;background:transparent;border:1px solid rgba(255,255,255,0.04)"></label>
          <label class="muted-small">Capacity<input id="evCapacity" name="evCapacity" required type="number" min="1" value="${eventInfo.capacity}" style="width:100%;padding:8px;border-radius:8px;background:transparent;border:1px solid rgba(255,255,255,0.04)"></label>
        </form>`,
        actions: [
          {label:'Cancel', fn: closeModal},
          {label:'Save', fn: ()=>{
            const formEl = document.getElementById('editEventForm');
            if(!formEl.checkValidity()){ formEl.reportValidity(); return; }
            eventInfo.title = document.getElementById('evTitle').value.trim();
            eventInfo.dateStr = document.getElementById('evDateStr').value.trim();
            eventInfo.capacity = Number(document.getElementById('evCapacity').value) || eventInfo.capacity;
            document.getElementById('portalTitle').textContent = eventInfo.title;
            document.getElementById('eventDate').textContent = eventInfo.dateStr;
            capacitySpan.textContent = eventInfo.capacity;
            closeModal();
            showToast('Event updated');
          }}
        ],
        onMount: ()=> {
          // nothing extra
        }
      });
    });

    // Modal system
    let currentModal = null;
    function showModal(opts = {}){
      // opts: title, message, content(html), actions: [{label, fn, danger}], onMount
      closeModal();
      const backdrop = document.createElement('div');
      backdrop.className = 'modal-backdrop';
      backdrop.tabIndex = -1;
      const modal = document.createElement('div');
      modal.className = 'modal card';
      let html = '';
      if(opts.title) html += `<h3>${escapeHTML(opts.title)}</h3>`;
      if(opts.message) html += `<p class="muted-small">${escape
