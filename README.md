<html lang="pt-PT">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Gestor de Escalas 2026-2030</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg: #e0ffff;
      --surface: #ffffff;
      --border: #e2e8f0;
      --success: #22c55e;
      --vacation: #8b4513;
      --occurrence: #ef4444;
      --cargo1: #e0f2fe;
      --cargo2: #ffedd5;
      --cargo3: #fef9c3;
      --text: #1e293b;
      --muted: #64748b;
    }
    * { box-sizing: border-box; }
    body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 10px; }
    .controls { background: var(--surface); padding: 15px; border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); margin-bottom: 15px; display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; }
    select { padding: 8px; border: 1px solid var(--border); border-radius: 6px; font-size: 1rem; flex: 1; min-width: 140px; }
    details { background: var(--surface); border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); margin-bottom: 20px; overflow: hidden; }
    summary { padding: 15px; font-weight: bold; cursor: pointer; background: #f1f5f9; list-style: none; display: flex; justify-content: space-between; align-items: center; }
    summary::after { content: "⚙️"; }
    .config-grid { padding: 15px; display: grid; gap: 15px; }
    .employee-card { border: 1px solid var(--border); padding: 15px; border-radius: 8px; background: #fafafa; }
    .row { display: flex; align-items: center; justify-content: space-between; gap: 10px; margin-bottom: 10px; flex-wrap: wrap; }
    .row .name-input { flex: 1; font-weight: bold; border: none; border-bottom: 1px solid #ccc; padding: 6px; min-width: 150px; background: transparent; font-size: 1.05rem; }
    .input-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }
    label { font-size: 0.8rem; color: #475569; display: block; margin-bottom: 4px; font-weight: 600; }
    input[type="number"], input[type="date"], textarea, select.cargo-select { width: 100%; padding: 8px; border: 1px solid var(--border); border-radius: 4px; font-size: 1rem; }
    .bulk-actions { display: flex; gap: 8px; padding: 0 15px 8px; flex-wrap: wrap; }
    .bulk-actions button { padding: 8px 12px; border: 1px solid var(--border); background: #f8fafc; border-radius: 6px; cursor: pointer; font-size: 0.9rem; flex: 1; }
    .link-inline { font-size: 0.85rem; background: none; border: none; cursor: pointer; padding: 0; }
    .calendar-wrapper { display: flex; width: 100%; background: var(--surface); border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); overflow: hidden; margin-bottom: 10px; }
    .fixed-column { width: 110px; flex-shrink: 0; background-color: #ffffff; border-right: 2px solid #94a3b8; box-shadow: 3px 0px 5px rgba(0,0,0,0.08); z-index: 10; }
    .scroll-column { flex-grow: 1; overflow-x: auto; -webkit-overflow-scrolling: touch; background-color: #ffffff; }
    .sync-table { border-collapse: collapse; }
    .fixed-column .sync-table { width: 100%; table-layout: fixed; }
    .scroll-column .sync-table { width: max-content; table-layout: auto; }
    .sync-table tr { height: 38px !important; }
    .sync-table th, .sync-table td { height: 38px !important; border-bottom: 1px solid var(--border); padding: 0; text-align: center; vertical-align: middle; font-size: 0.8rem; box-sizing: border-box; }
    .sync-table th { background-color: #f1f5f9; font-weight: 600; border-top: 1px solid var(--border); }
    .fixed-column th, .fixed-column td { text-align: left; padding-left: 10px; padding-right: 5px; font-weight: bold; font-size: 0.85rem; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
    .scroll-column th, .scroll-column td { border-right: 1px solid var(--border); width: 36px !important; min-width: 36px !important; max-width: 36px !important; white-space: nowrap !important; word-break: keep-all !important; }
    .scroll-column td { cursor: pointer; transition: filter 0.15s; }
    .scroll-column td:active { filter: brightness(0.8); }
    .folga { background-color: var(--success) !important; color: white !important; font-weight: bold; }
    .ferias { background-color: var(--vacation) !important; color: white !important; font-weight: bold; }
    .ocorrencia { background-color: var(--occurrence) !important; color: white !important; font-weight: bold; }
    .cargo1-bg { background-color: var(--cargo1) !important; }
    .cargo2-bg { background-color: var(--cargo2) !important; }
    .cargo3-bg { background-color: var(--cargo3) !important; }
    .actions-bar { position: sticky; top: 0; z-index: 999; display: flex; justify-content: flex-end; gap: 8px; margin-bottom: 12px; padding: 8px; background: #f8fafc; border-bottom: 1px solid #e2e8f0; }
    .btn-action { padding: 12px 16px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; width: 100%; margin-top: 5px; }

    .list-container { padding: 20px; display: none; background: var(--surface); border-radius: 12px; }
    .list-container table { min-width: 100%; border-collapse: collapse; }
    .list-container th { text-align: left; padding: 10px; border-bottom: 2px solid var(--border); }
    .modal-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.6); display: none; justify-content: center; align-items: center; z-index: 10000; backdrop-filter: blur(2px); }
    .modal-content { background: #fff; padding: 20px; border-radius: 12px; width: 90%; max-width: 450px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
    .modal-content textarea { height: 100px; margin-top: 5px; resize: vertical; font-family: inherit; width: 100%; box-sizing: border-box; }
    .modal-buttons { display: flex; gap: 10px; justify-content: space-between; margin-top: 15px; flex-wrap: wrap; }
    .btn-save-occ   { background: var(--primary);    color: white; }
    .btn-cancel-occ { background: #e2e8f0;           color: #1e293b; }
    .btn-delete-occ { background: var(--occurrence); color: white; }
    
    @media (max-width: 600px) {
      .actions-bar { flex-direction: column; align-items: stretch; }
      .actions-bar .save-btn { width: 100%; margin-bottom: 5px; text-align: center; }
      .controls { flex-direction: column; }
      .input-group { grid-template-columns: 1fr; }
      .modal-buttons { flex-direction: column; }
      #btnGroupRight { flex-direction: column; width: 100%; gap: 10px; }
    }
  </style>
</head>
<body>

  <div class="actions-bar">
    <button class="save-btn" onclick="toggleOccurrencesList()" style="background:#f59e0b; color:white; border:none; padding:10px; border-radius:8px; font-weight:bold; cursor:pointer;">📋 Ausências</button>
    <button class="save-btn" onclick="saveData()" style="background:var(--primary); color:white; border:none; padding:10px; border-radius:8px; font-weight:bold; cursor:pointer;">💾 Salvar</button>
    <button class="save-btn" onclick="toggleConfig()" style="background:var(--primary); color:white; border:none; padding:10px; border-radius:8px; font-weight:bold; cursor:pointer;">⚙️ Config.</button>
  </div>

  <div class="controls" id="mainControls">
    <select id="selMonth" onchange="renderCalendar()"></select>
    <select id="selYear" onchange="renderCalendar()"></select>
  </div>

  <details id="configDetails">
    <summary>Configurar Escalas</summary>
    <div class="bulk-actions" style="padding: 10px 15px;">
      <button onclick="selectAll(true)">Selecionar todos</button>
      <button onclick="selectAll(false)">Ocultar todos</button>
    </div>
    <div class="config-grid" id="configList"></div>
    <div class="legend" style="border-top:1px solid #ccc;padding-top:10px;">
      • <strong>Cores de Trabalho:</strong>
      <span style="background:#e0f2fe;padding:2px 6px;border:1px solid #ccc;border-radius:4px;display:inline-block;margin:2px;">Cargo 1</span>
      <span style="background:#ffedd5;padding:2px 6px;border:1px solid #ccc;border-radius:4px;display:inline-block;margin:2px;">Cargo 2</span>
      <span style="background:#fef9c3;padding:2px 6px;border:1px solid #ccc;border-radius:4px;display:inline-block;margin:2px;">Cargo 3</span><br />
      • <strong>Demais Cores:</strong>
      <span style="background:#22c55e;color:#fff;padding:2px 6px;border-radius:4px;display:inline-block;margin:2px;">Folga</span>
      <span style="background:#8b4513;color:#fff;padding:2px 6px;border-radius:4px;display:inline-block;margin:2px;">Férias</span>
      <span style="background:#ef4444;color:#fff;padding:2px 6px;border-radius:4px;display:inline-block;margin:2px;">Afastamento/Falta (⚠️)</span>
    </div>
    <div style="padding: 15px; display: flex; gap: 10px;">
      <button class="btn-action" style="background:#64748b; color:white; padding:10px; border-radius:6px;" onclick="exportData()">⬇️ Exportar Backup</button>
      <input type="file" id="importFile" style="display:none;" onchange="importData(event)">
      <button class="btn-action" style="background:#64748b; color:white; padding:10px; border-radius:6px;" onclick="document.getElementById('importFile').click()">⬆️ Importar Backup</button>
    </div>
  </details>

  <div class="calendar-wrapper" id="calendarContainer">
    <div class="fixed-column">
      <table class="sync-table">
        <thead><tr id="nameHeader"></tr></thead>
        <tbody id="nameRows"></tbody>
      </table>
    </div>
    <div class="scroll-column" id="scrollPanel">
      <table class="sync-table">
        <thead><tr id="daysHeader"></tr></thead>
        <tbody id="daysRows"></tbody>
      </table>
    </div>
  </div>
  
  <div id="emptyHint" class="empty-hint" style="display:none;">Nenhum funcionário selecionado.</div>

  <div class="list-container" id="listContainer">
    <button class="btn-action" style="background:var(--muted); color:white; margin-bottom:15px;" onclick="toggleOccurrencesList()">⬅️ Retornar para a Escala</button>
    <h2 style="margin-top:0;color:#1e293b;font-size:1.2rem;">Lista de Ausências</h2>
    <table>
      <thead>
        <tr>
          <th style="width:90px;">Data</th>
          <th style="width:150px;">Func.</th>
          <th>Motivo</th>
        </tr>
      </thead>
      <tbody id="absencesListBody"></tbody>
    </table>
  </div>

  <div class="modal-overlay" id="occurrenceModal">
    <div class="modal-content">
      <h3 id="occTitle" style="margin-top:0;color:#1e293b;">Registar Ausência</h3>
      <label for="occReason" style="font-weight:bold;color:#334155;">Motivo (Atestado, falta...)</label>
      <textarea id="occReason" placeholder="Descreva aqui o motivo..."></textarea>
      <div class="modal-buttons">
        <button class="btn-action btn-delete-occ" id="btnDelOcc" onclick="deleteOccurrence()">🗑️ Excluir</button>
        <div style="display:flex;gap:10px;width:100%;justify-content:flex-end;" id="btnGroupRight">
          <button class="btn-action btn-cancel-occ" onclick="closeModal()">Cancelar</button>
          <button class="btn-action btn-save-occ"   onclick="saveOccurrence()">Salvar</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    const STORAGE_KEY = 'meu_backup_escala_seguro'; // NÃO ALTERE ESTA LINHA EM FUTUROS PROMPTS
    const startYear = 2026, endYear = 2030, defaultEmployees = 20;
    let employees = [], activeEmpIndex = -1, activeDateStr = '';

    function exportData() {
      const data = localStorage.getItem(STORAGE_KEY);
      if(!data) { alert("Não há dados para exportar."); return; }
      const blob = new Blob([data], {type: "application/json"});
      const a = document.createElement("a");
      a.href = URL.createObjectURL(blob);
      a.download = "backup_escala.json";
      a.click();
    }
    
    function importData(event) {
      const file = event.target.files[0];
      if(!file) return;
      const reader = new FileReader();
      reader.onload = (e) => { 
        localStorage.setItem(STORAGE_KEY, e.target.result); 
        alert("Dados importados com sucesso! A página vai recarregar.");
        location.reload(); 
      };
      reader.readAsText(file);
    }

    function toInt(n, fb = 0){ const v = parseInt(n, 10); return isNaN(v) ? fb : v; }

    function ensureShape(emp, i){
      if (!emp) emp = {};
      emp.id          = emp.id          ?? (i + 1);
      emp.name        = emp.name        ?? `Funcionário ${i + 1}`;
      emp.startDate   = emp.startDate   ?? '2026-01-01';
      emp.visible     = typeof emp.visible === 'boolean' ? emp.visible : true;
      emp.vacationStart = emp.vacationStart ?? '';
      emp.vacationEnd   = emp.vacationEnd   ?? '';
      emp.cargo       = emp.cargo || '';
      emp.occurrences = emp.occurrences || {};
      
      const c1 = emp.cycles?.[0] ?? { workDays: 6, offDays: 2 };
      const c2 = emp.cycles?.[1] ?? { workDays: 5, offDays: 2 };
      const c3 = emp.cycles?.[2] ?? { workDays: 0, offDays: 0 }; 
      
      emp.cycles = [
        { label: 'A', workDays: toInt(c1.workDays, 6), offDays: toInt(c1.offDays, 2) },
        { label: 'B', workDays: toInt(c2.workDays, 5), offDays: toInt(c2.offDays, 2) },
        { label: 'C', workDays: toInt(c3.workDays, 0), offDays: toInt(c3.offDays, 0) }
      ];
      return emp;
    }

    function initData(){
      const data = localStorage.getItem(STORAGE_KEY);
      if (data){
        try { employees = (JSON.parse(data) || []).map(ensureShape); } catch(e){ employees = []; }
      } 
      
      if (!employees || !employees.length) {
        for (let i = 0; i < defaultEmployees; i++) employees.push(ensureShape({}, i));
      }
    }

    const selMonth   = document.getElementById('selMonth');
    const selYear    = document.getElementById('selYear');
    const configList = document.getElementById('configList');
    const nameRows   = document.getElementById('nameRows');
    const daysHeader = document.getElementById('daysHeader');
    const daysRows   = document.getElementById('daysRows');
    const emptyHint  = document.getElementById('emptyHint');
    const modal      = document.getElementById('occurrenceModal');

    function init(){
      initData();
      const months = ["Janeiro","Fevereiro","Março","Abril","Maio","Junho","Julho","Agosto","Setembro","Outubro","Novembro","Dezembro"];
      months.forEach((m, i) => selMonth.innerHTML += `<option value="${i}">${m}</option>`);
      for (let y = startYear; y <= endYear; y++) selYear.innerHTML += `<option value="${y}">${y}</option>`;
      const now = new Date();
      const iy  = (now.getFullYear() >= startYear && now.getFullYear() <= endYear) ? now.getFullYear() : startYear;
      selYear.value  = String(iy);
      selMonth.value = String(iy === now.getFullYear() ? now.getMonth() : 0);
      renderConfigPanel();
      renderCalendar();
    }

    function isDayOff(dateObj, emp){
      const start = new Date(emp.startDate + 'T00:00:00');
      const cur   = new Date(dateObj.getFullYear(), dateObj.getMonth(), dateObj.getDate());
      const diff  = Math.floor((cur - start) / 86400000);
      if (diff < 0) return false;
      
      const w1=toInt(emp.cycles[0].workDays,6), o1=toInt(emp.cycles[0].offDays,2);
      const w2=toInt(emp.cycles[1].workDays,5), o2=toInt(emp.cycles[1].offDays,2);
      const w3=toInt(emp.cycles[2].workDays,0), o3=toInt(emp.cycles[2].offDays,0);
      
      const l1=w1+o1, l2=w2+o2, l3=w3+o3;
      const totalLen = l1 + l2 + l3;
      
      if (totalLen === 0) return false;
      
      const r = diff % totalLen;
      
      if (l1 > 0 && r < l1) return r >= w1;
      if (l2 > 0 && r < l1 + l2) return (r - l1) >= w2;
      if (l3 > 0 && r < l1 + l2 + l3) return (r - l1 - l2) >= w3;
      
      return false;
    }

    function isVacation(dateObj, emp){
      if (!emp.vacationStart || !emp.vacationEnd) return false;
      const a = new Date(emp.vacationStart + 'T00:00:00'), b = new Date(emp.vacationEnd + 'T00:00:00');
      if (isNaN(a) || isNaN(b)) return false;
      const s = a <= b ? a : b, e = a <= b ? b : a;
      const cur = new Date(dateObj.getFullYear(), dateObj.getMonth(), dateObj.getDate());
      return cur >= s && cur <= e;
    }

    function clearVacation(index){
      employees[index].vacationStart = '';
      employees[index].vacationEnd   = '';
      renderConfigPanel(); renderCalendar();
    }

    function renderConfigPanel(){
      configList.innerHTML = '';
      employees.forEach((emp, index) => {
        const div = document.createElement('div');
        div.className = 'employee-card';
        div.innerHTML = `
          <div class="row">
            <input class="name-input" type="text" value="${emp.name}" onchange="updateEmp(${index},'name',this.value)" />
            <label class="visibility"><input type="checkbox" ${emp.visible ? 'checked' : ''} onchange="updateEmp(${index},'visible',this.checked);renderCalendar();"> Mostrar</label>
          </div>
          <div class="input-group">
            <div>
              <label>Cargo do Funcionário</label>
              <select class="cargo-select" onchange="updateEmp(${index},'cargo',this.value);renderCalendar();">
                <option value="">Nenhum (Branco)</option>
                <option value="cargo1" ${emp.cargo==='cargo1'?'selected':''}>Cargo 1 (Azul)</option>
                <option value="cargo2" ${emp.cargo==='cargo2'?'selected':''}>Cargo 2 (Laranja)</option>
                <option value="cargo3" ${emp.cargo==='cargo3'?'selected':''}>Cargo 3 (Amarelo)</option>
              </select>
            </div>
            <div>
              <label>Início da Escala (Trabalho)</label>
              <div style="display:flex;gap:5px;">
                <input type="date" value="${emp.startDate}" onchange="updateEmp(${index},'startDate',this.value);renderCalendar();" style="flex:1;" />
                <button class="apply-btn" onclick="renderCalendar()">Aplicar</button>
              </div>
            </div>
          </div>
          <div style="display:flex;justify-content:space-between;align-items:flex-end;border-top:1px dashed #ccc;padding-top:10px;margin-top:10px;margin-bottom:4px;">
            <label style="margin:0;">Período de Férias</label>
            <button class="link-inline" style="color:#ef4444;font-weight:bold;" onclick="clearVacation(${index});return false;">🗑️ Limpar Marcação</button>
          </div>
          <div class="input-group">
            <div><label style="font-weight:normal;">Início</label><input type="date" value="${emp.vacationStart}" onchange="updateEmp(${index},'vacationStart',this.value);renderCalendar();" /></div>
            <div><label style="font-weight:normal;">Fim</label><input type="date" value="${emp.vacationEnd}" onchange="updateEmp(${index},'vacationEnd',this.value);renderCalendar();" /></div>
          </div>
          <div class="input-group" style="border-top:1px dashed #ccc;padding-top:10px;">
            <div><label>Escala A - Trab.</label><input type="number" min="0" value="${emp.cycles[0].workDays}" onchange="updateCycle(${index},0,'workDays',this.value)" /></div>
            <div><label>Escala A - Folga</label><input type="number" min="0" value="${emp.cycles[0].offDays}"  onchange="updateCycle(${index},0,'offDays',this.value)" /></div>
          </div>
          <div class="input-group">
            <div><label>Escala B - Trab.</label><input type="number" min="0" value="${emp.cycles[1].workDays}" onchange="updateCycle(${index},1,'workDays',this.value)" /></div>
            <div><label>Escala B - Folga</label><input type="number" min="0" value="${emp.cycles[1].offDays}"  onchange="updateCycle(${index},1,'offDays',this.value)" /></div>
          </div>
          <div class="input-group">
            <div><label>Escala C (Opc.) - Trab.</label><input type="number" min="0" value="${emp.cycles[2].workDays}" onchange="updateCycle(${index},2,'workDays',this.value)" /></div>
            <div><label>Escala C (Opc.) - Folga</label><input type="number" min="0" value="${emp.cycles[2].offDays}"  onchange="updateCycle(${index},2,'offDays',this.value)" /></div>
          </div>`;
        configList.appendChild(div);
      });
    }

    function updateEmp(index, field, value){ employees[index][field] = field === 'visible' ? !!value : value; }
    function updateCycle(index, ci, field, value){ employees[index].cycles[ci][field] = toInt(value, 0); }
    function selectAll(flag){ employees.forEach(e => e.visible = !!flag); renderConfigPanel(); renderCalendar(); }

    function saveData(){
      localStorage.setItem(STORAGE_KEY, JSON.stringify(employees));
      renderCalendar();
      if (document.getElementById('listContainer').style.display === 'block') refreshOccurrencesList();
      alert('Dados guardados com sucesso!');
    }

    function renderCalendar(){
      const month = parseInt(selMonth.value, 10), year = parseInt(selYear.value, 10);
      if (isNaN(month) || isNaN(year)) return;
      const daysInMonth = new Date(year, month + 1, 0).getDate();

      document.getElementById('nameHeader').innerHTML = '<th>Func.</th>';

      let hdrDays = '';
      for (let d = 1; d <= daysInMonth; d++){
        const date = new Date(year, month, d);
        const wknd = date.getDay() === 0 || date.getDay() === 6;
        hdrDays += `<th style="${wknd ? 'background:#e2e8f0;' : ''}">${d}</th>`;
      }
      document.getElementById('daysHeader').innerHTML = hdrDays;

      const selected = employees.filter(e => e.visible);
      let htmlNames = '';
      let htmlDays = '';

      selected.forEach(emp => {
        const ei = employees.findIndex(e => e.id === emp.id);
        
        htmlNames += `<tr><td title="${emp.name}">${emp.name}</td></tr>`;
        
        htmlDays += `<tr>`;
        for (let d = 1; d <= daysInMonth; d++){
          const date = new Date(year, month, d);
          const ds   = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
          const occ  = emp.occurrences && emp.occurrences[ds];
          const vac  = isVacation(date, emp);
          const off  = !vac && isDayOff(date, emp);
          
          let content = '', cls = '', title = '';
          if (occ){
            cls = 'ocorrencia'; content = '⚠️'; title = occ;
          } else if (vac){
            cls = 'ferias'; title = 'Férias';
          } else if (off){
            cls = 'folga'; content = 'F'; title = 'Folga';
          } else {
            if      (emp.cargo === 'cargo1') cls = 'cargo1-bg';
            else if (emp.cargo === 'cargo2') cls = 'cargo2-bg';
            else if (emp.cargo === 'cargo3') cls = 'cargo3-bg';
          }
          htmlDays += `<td class="${cls}" title="${title}" onclick="openModal(${ei},'${ds}')">${content}</td>`;
        }
        htmlDays += `</tr>`;
      });
      
      document.getElementById('nameRows').innerHTML = htmlNames;
      document.getElementById('daysRows').innerHTML = htmlDays;
      
      emptyHint.style.display = selected.length ? 'none' : 'block';
    }

    function openModal(ei, ds){
      activeEmpIndex = ei; activeDateStr = ds;
      const emp = employees[ei], [y,m,d] = ds.split('-');
      document.getElementById('occTitle').innerText = `Ausência: ${emp.name} (${d}/${m}/${y})`;
      const reason = document.getElementById('occReason'), btn = document.getElementById('btnDelOcc');
      if (emp.occurrences && emp.occurrences[ds]){ reason.value = emp.occurrences[ds]; btn.style.display = 'block'; }
      else { reason.value = ''; btn.style.display = 'none'; }
      modal.style.display = 'flex';
      setTimeout(() => reason.focus(), 100);
    }

    function closeModal(){ modal.style.display = 'none'; }

    function saveOccurrence(){
      const r = document.getElementById('occReason').value.trim();
      if (!employees[activeEmpIndex].occurrences) employees[activeEmpIndex].occurrences = {};
      if (r) employees[activeEmpIndex].occurrences[activeDateStr] = r;
      else   delete employees[activeEmpIndex].occurrences[activeDateStr];
      closeModal(); saveData();
    }

    function deleteOccurrence(){
      if (employees[activeEmpIndex].occurrences) delete employees[activeEmpIndex].occurrences[activeDateStr];
      closeModal(); saveData();
    }

    function toggleOccurrencesList(){
      const cal  = document.getElementById('calendarContainer');
      const list = document.getElementById('listContainer');
      const ctrl = document.getElementById('mainControls');
      const det  = document.getElementById('configDetails');
      if (list.style.display === 'block'){
        list.style.display = 'none'; cal.style.display = 'flex';
        ctrl.style.display = 'flex'; det.style.display = 'block';
      } else {
        refreshOccurrencesList();
        cal.style.display  = 'none'; ctrl.style.display = 'none';
        det.style.display  = 'none'; list.style.display = 'block';
      }
    }

    function refreshOccurrencesList(){
      const body = document.getElementById('absencesListBody');
      let all = [];
      employees.forEach(emp => {
        if (emp.occurrences)
          Object.entries(emp.occurrences).forEach(([ds, r]) => all.push({ date: ds, name: emp.name, reason: r }));
      });
      all.sort((a, b) => b.date.localeCompare(a.date));
      body.innerHTML = all.length === 0
        ? `<tr><td colspan="3" style="text-align:center;color:#64748b;padding:30px;">Nenhuma ausência registada.</td></tr>`
        : all.map(o => {
            const [y,m,d] = o.date.split('-');
            return `<tr style="border-bottom:1px solid #e2e8f0;">
              <td style="padding:10px;text-align:left;">${d}/${m}</td>
              <td style="font-weight:bold;text-align:left;padding:10px;">${o.name}</td>
              <td style="text-align:left;padding:10px;color:#ef4444;font-weight:500;">${o.reason}</td>
            </tr>`;
          }).join('');
    }

    function toggleConfig(){ const d = document.querySelector('details'); d.open = !d.open; }

    init();
  </script>
</body>
</html>
