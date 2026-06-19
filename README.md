
<html lang="pt-PT">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Gestor de Escalas 2026-2030</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg: #f8fafc;
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
    
    /* BLOQUEIO TOTAL DA PÁGINA PARA NÃO ARRASTAR */
    html, body {
      margin: 0;
      padding: 0;
      width: 100vw;
      overflow-x: hidden;
    }

    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background-color: var(--bg);
      color: var(--text);
      padding: 10px;
      box-sizing: border-box;
    }

    h1 { font-size: 1.2rem; text-align: center; margin-bottom: 15px; }

    .controls { background: var(--surface); padding: 15px; border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); margin-bottom: 15px; display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; }
    select { padding: 8px; border: 1px solid var(--border); border-radius: 6px; font-size: 1rem; flex: 1; min-width: 140px; }
    details { background: var(--surface); border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); margin-bottom: 20px; overflow: hidden; }
    summary { padding: 15px; font-weight: bold; cursor: pointer; background: #f1f5f9; list-style: none; display: flex; justify-content: space-between; align-items: center; }
    summary::after { content: "⚙️"; }

    .config-grid { padding: 15px; display: grid; gap: 15px; }
    .employee-card { border: 1px solid var(--border); padding: 15px; border-radius: 8px; background: #fafafa; }
    .row { display: flex; align-items: center; justify-content: space-between; gap: 10px; margin-bottom: 10px; flex-wrap: wrap; }
    .row .name-input { flex: 1; font-weight:bold; border:none; border-bottom:1px solid #ccc; padding:6px; min-width: 150px; background: transparent; font-size: 1.05rem;}
    .row .visibility { white-space: nowrap; font-size: 0.9rem; color: var(--muted); font-weight: bold;}
    .input-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }
    label { font-size: 0.8rem; color: #475569; display: block; margin-bottom: 4px; font-weight: 600;}
    input[type="number"], input[type="date"], textarea, select.cargo-select { width: 100%; padding: 8px; border: 1px solid var(--border); border-radius: 4px; box-sizing: border-box; font-size: 1rem; }
    .bulk-actions { display:flex; gap:8px; padding: 0 15px 8px; flex-wrap: wrap; }
    .bulk-actions button { padding: 8px 12px; border: 1px solid var(--border); background:#f8fafc; border-radius:6px; cursor:pointer; font-size:0.9rem; flex: 1; }
    .link-inline { font-size: 0.85rem; background: none; border: none; cursor: pointer; padding: 0; }

    /* =========================================
       ZONA DA TABELA (SCROLL BLINDADO)
       ========================================= */
    .calendar-container {
      width: 100%; 
      max-width: 100vw;
      overflow-x: auto; 
      overflow-y: hidden;
      -webkit-overflow-scrolling: touch; 
      background: var(--surface);
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      padding-bottom: 15px;
      position: relative;
    }

    #scheduleTable { 
      table-layout: fixed; 
      width: max-content; 
      border-collapse: separate !important; 
      border-spacing: 0 !important; 
      min-width: 800px;
    }
    
    #scheduleTable th, #scheduleTable td { 
      border-bottom: 1px solid var(--border); 
      border-right: 1px solid var(--border); 
      text-align: center; 
      padding: 10px 0; 
      font-size: 0.85rem;
      background-color: #ffffff;
    }
    
    #scheduleTable th { background-color: #f1f5f9; font-weight: 600; border-top: 1px solid var(--border); }

    /* A CLASSE MÁGICA: APLICAÇÃO DIRETA NO NOME
       Isto impede que o telemóvel ignore o comando
    */
    .fixar-nome {
      position: -webkit-sticky !important; 
      position: sticky !important;         
      left: 0 !important;
      
      width: 120px !important;      
      min-width: 120px !important;
      max-width: 120px !important;
      
      text-align: left !important;
      padding-left: 10px !important;
      font-weight: bold;
      
      background-color: #ffffff !important;
      z-index: 50 !important; 
      
      border-right: 2px solid #94a3b8 !important; 
      outline: 1px solid #e2e8f0; /* Previne falhas gráficas no iOS */
      box-shadow: 3px 0px 6px rgba(0,0,0,0.1) !important; 
      
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    th.fixar-nome {
      background-color: #f1f5f9 !important;
      z-index: 60 !important; 
    }

    /* QUADRADOS DOS DIAS A EXATOS 40PX */
    .dia-quadrado {
      width: 40px !important;
      min-width: 40px !important;
      max-width: 40px !important;
      box-sizing: border-box;
      cursor: pointer;
      transition: filter 0.2s;
    }
    .dia-quadrado:active { filter: brightness(0.8); }

    /* Cores */
    .folga { background-color: var(--success) !important; color: white !important; font-weight: bold; }
    .ferias { background-color: var(--vacation) !important; color: white !important; font-weight: bold; }
    .ocorrencia { background-color: var(--occurrence) !important; color: white !important; font-weight: bold; }
    .cargo1-bg { background-color: var(--cargo1) !important; }
    .cargo2-bg { background-color: var(--cargo2) !important; }
    .cargo3-bg { background-color: var(--cargo3) !important; }

    .legend { color: var(--muted); font-size: 0.85rem; margin: 6px 0 12px; text-align: center; line-height: 1.6; }
    .save-btn { padding: 10px 14px; background: var(--primary); color: white; border: none; border-radius: 8px; font-size: 0.95rem; cursor: pointer; font-weight: bold; }
    .apply-btn { padding: 8px 12px; background: #3b82f6; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 0.9rem; }
    .empty-hint { color: var(--muted); text-align:center; padding: 14px; font-size:0.9rem; }
    .actions-bar { position:sticky; top:0; z-index:999; display:flex; justify-content:flex-end; gap:8px; margin-bottom:12px; padding:8px; background:#f8fafc; border-bottom:1px solid #e2e8f0; }

    /* Listas e Modal */
    .list-container { padding: 20px; display: none; background: var(--surface); border-radius: 12px; }
    .list-container table { min-width: 100%; border-collapse: collapse; }
    .list-container th { text-align: left; padding: 10px; border-bottom: 2px solid var(--border); }

    .modal-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.6); display: none; justify-content: center; align-items: center; z-index: 10000; backdrop-filter: blur(2px); }
    .modal-content { background: #fff; padding: 20px; border-radius: 12px; width: 90%; max-width: 450px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
    .modal-content textarea { height: 100px; margin-top: 5px; resize: vertical; font-family: inherit; width: 100%; box-sizing: border-box; }
    .modal-buttons { display: flex; gap: 10px; justify-content: space-between; margin-top: 15px; flex-wrap: wrap; }
    .btn-action { padding: 12px 16px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; flex: 1; text-align: center; font-size: 0.95rem; }
    .btn-save-occ { background: var(--primary); color: white; }
    .btn-cancel-occ { background: #e2e8f0; color: #1e293b; }
    .btn-delete-occ { background: var(--occurrence); color: white; }

    @media (max-width: 600px) {
      .actions-bar { flex-direction: column; align-items: stretch; }
      .actions-bar .save-btn { width: 100% !important; margin-bottom: 5px; text-align: center; }
      .controls { flex-direction: column; }
      .input-group { grid-template-columns: 1fr; }
      .row .visibility { margin-top: 5px; }
      .apply-btn { width: auto; }
      .modal-buttons { flex-direction: column; }
      #btnGroupRight { flex-direction: column; width: 100%; gap: 10px; }
      .btn-delete-occ { width: 100%; margin-bottom: 10px; }
    }
  </style>
</head>
<body>
  <h1>📅 Escala de Folgas</h1>

  <div class="actions-bar">
    <button class="save-btn" style="background:#f59e0b;" onclick="toggleOccurrencesList()">📋 Relatório de Ausências</button>
    <button class="save-btn" onclick="saveData()">💾 Salvar Tudo</button>
    <button class="save-btn" onclick="toggleConfig()">⚙️ Configurações</button>
  </div>

  <div class="controls" id="mainControls">
    <select id="selMonth" onchange="renderCalendar()"></select>
    <select id="selYear" onchange="renderCalendar()"></select>
  </div>

  <details id="configDetails">
    <summary>Configurar Escalas (Toque aqui)</summary>
    <div class="bulk-actions">
      <button onclick="selectAll(true)">Selecionar todos</button>
      <button onclick="selectAll(false)">Ocultar todos</button>
    </div>
    
    <div class="config-grid" id="configList"></div>
    
    <div class="legend" style="border-top: 1px solid #ccc; padding-top: 10px;">
      • <strong>Cores de Trabalho:</strong> 
      <span style="background:#e0f2fe;padding:2px 6px;border:1px solid #ccc;border-radius:4px;display:inline-block;margin:2px;">Cargo 1</span> 
      <span style="background:#ffedd5;padding:2px 6px;border:1px solid #ccc;border-radius:4px;display:inline-block;margin:2px;">Cargo 2</span> 
      <span style="background:#fef9c3;padding:2px 6px;border:1px solid #ccc;border-radius:4px;display:inline-block;margin:2px;">Cargo 3</span><br />
      • <strong>Demais Cores:</strong> 
      <span style="background:#22c55e;color:#fff;padding:2px 6px;border-radius:4px;display:inline-block;margin:2px;">Folga</span> 
      <span style="background:#8b4513;color:#fff;padding:2px 6px;border-radius:4px;display:inline-block;margin:2px;">Férias (Marrom)</span> 
      <span style="background:#ef4444;color:#fff;padding:2px 6px;border-radius:4px;display:inline-block;margin:2px;">Afastamento/Falta (⚠️)</span>
    </div>
  </details>

  <div class="calendar-container" id="calendarContainer">
    <table id="scheduleTable">
      <thead>
        <tr id="headerRow"></tr>
      </thead>
      <tbody id="bodyRows"></tbody>
    </table>
    <div id="emptyHint" class="empty-hint" style="display:none;">Nenhum funcionário selecionado.</div>
  </div>

  <div class="list-container" id="listContainer">
    <button class="save-btn" style="width: 100%; margin-bottom: 15px; background: var(--muted);" onclick="toggleOccurrencesList()">⬅️ Retornar para a Escala</button>
    <h2 style="margin-top: 0; color: #1e293b; font-size: 1.2rem;">Lista de Ausências</h2>
    <table>
      <thead>
        <tr>
          <th style="width: 90px;">Data</th>
          <th style="width: 150px;">Func.</th>
          <th>Motivo</th>
        </tr>
      </thead>
      <tbody id="absencesListBody"></tbody>
    </table>
  </div>

  <div class="modal-overlay" id="occurrenceModal">
    <div class="modal-content">
      <h3 id="occTitle" style="margin-top: 0; color: #1e293b;">Registrar Ausência</h3>
      <label for="occReason" style="font-weight: bold; color: #334155;">Motivo (Atestado, falta...)</label>
      <textarea id="occReason" placeholder="Descreva aqui o motivo..."></textarea>
      
      <div class="modal-buttons">
        <button class="btn-action btn-delete-occ" id="btnDelOcc" onclick="deleteOccurrence()">🗑️ Excluir</button>
        <div style="display: flex; gap: 10px; width: 100%; justify-content: flex-end;" id="btnGroupRight">
          <button class="btn-action btn-cancel-occ" onclick="closeModal()">Cancelar</button>
          <button class="btn-action btn-save-occ" onclick="saveOccurrence()">Salvar</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    const startYear = 2026;
    const endYear = 2030;
    const defaultEmployees = 10;
    const STORAGE_KEY_V2 = 'escalaData_v2';
    const LEGACY_KEY_V1 = 'escalaData_v1';

    let employees = [];
    let activeEmpIndex = -1;
    let activeDateStr = '';

    function toInt(n, fallback = 0){
      const v = parseInt(n, 10); return isNaN(v) ? fallback : v;
    }

    function ensureShape(emp, i){
      if (!emp) emp = {};
      emp.id = emp.id ?? (i+1);
      emp.name = emp.name ?? `Funcionário ${i+1}`;
      emp.startDate = emp.startDate ?? '2026-01-01';
      emp.visible = (typeof emp.visible === 'boolean') ? emp.visible : true;
      emp.vacationStart = emp.vacationStart ?? '';
      emp.vacationEnd = emp.vacationEnd ?? '';
      emp.cargo = emp.cargo || ''; 
      emp.occurrences = emp.occurrences || {}; 
      
      const c1 = emp.cycles && emp.cycles[0] ? emp.cycles[0] : { label:'A', workDays: 6, offDays: 2 };
      const c2 = emp.cycles && emp.cycles[1] ? emp.cycles[1] : { label:'B', workDays: 5, offDays: 2 };
      emp.cycles = [
        { label: 'A', workDays: toInt(c1.workDays,6), offDays: toInt(c1.offDays,2) },
        { label: 'B', workDays: toInt(c2.workDays,5), offDays: toInt(c2.offDays,2) }
      ];
      return emp;
    }

    function initData() {
      const savedV2 = localStorage.getItem(STORAGE_KEY_V2);
      if (savedV2) {
        try { employees = (JSON.parse(savedV2) || []).map(ensureShape); } catch(e){ employees = []; }
      } else {
        const savedV1 = localStorage.getItem(LEGACY_KEY_V1);
        if (savedV1) {
          try {
            const legacy = JSON.parse(savedV1) || [];
            employees = legacy.map((emp, i) => ensureShape({
              id: emp.id ?? (i+1), name: emp.name ?? `Funcionário ${i+1}`,
              startDate: emp.startDate ?? '2026-01-01', visible: true,
              cycles: [{ label: 'A', workDays: toInt(emp.workDays, 6), offDays: toInt(emp.offDays, 2) }, { label: 'B', workDays: 5, offDays: 2 }]
            }, i));
          } catch(e) {}
        }
        if (!employees || employees.length === 0) {
          for (let i = 0; i < defaultEmployees; i++) { employees.push(ensureShape({}, i)); }
        }
      }
    }

    const selMonth = document.getElementById('selMonth');
    const selYear = document.getElementById('selYear');
    const configList = document.getElementById('configList');
    const headerRow = document.getElementById('headerRow');
    const bodyRows = document.getElementById('bodyRows');
    const emptyHint = document.getElementById('emptyHint');
    const modal = document.getElementById('occurrenceModal');

    function init() {
      initData();
      const months = ["Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho", "Julho", "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"];
      months.forEach((m, i) => { selMonth.innerHTML += `<option value="${i}">${m}</option>`; });
      for (let y = startYear; y <= endYear; y++) { selYear.innerHTML += `<option value="${y}">${y}</option>`; }
      
      const now = new Date();
      const initYear = (now.getFullYear() >= startYear && now.getFullYear() <= endYear) ? now.getFullYear() : startYear;
      selYear.value = String(initYear);
      selMonth.value = String((initYear === now.getFullYear()) ? now.getMonth() : 0);

      renderConfigPanel();
      renderCalendar();
    }

    function isDayOff(dateObj, emp) {
      const start = new Date(emp.startDate + 'T00:00:00');
      const current = new Date(dateObj.getFullYear(), dateObj.getMonth(), dateObj.getDate());
      const diffDays = Math.floor((current - start) / (1000 * 60 * 60 * 24));
      if (diffDays < 0) return false;

      const w1 = toInt(emp.cycles[0].workDays, 6), o1 = toInt(emp.cycles[0].offDays, 2);
      const w2 = toInt(emp.cycles[1].workDays, 5), o2 = toInt(emp.cycles[1].offDays, 2);
      const len1 = w1 + o1, len2 = w2 + o2;

      if (len1 === 0 && len2 === 0) return false;
      if (len1 === 0) return (diffDays % len2) >= w2;
      if (len2 === 0) return (diffDays % len1) >= w1;

      const r = diffDays % (len1 + len2);
      return r < len1 ? (r >= w1) : ((r - len1) >= w2);
    }

    function isVacation(dateObj, emp){
      if (!emp.vacationStart || !emp.vacationEnd) return false;
      const a = new Date(emp.vacationStart + 'T00:00:00'), b = new Date(emp.vacationEnd + 'T00:00:00');
      if (isNaN(a) || isNaN(b)) return false;
      const start = a <= b ? a : b, end = a <= b ? b : a;
      const cur = new Date(dateObj.getFullYear(), dateObj.getMonth(), dateObj.getDate());
      return cur >= start && cur <= end;
    }

    function clearVacation(index) {
      employees[index].vacationStart = '';
      employees[index].vacationEnd = '';
      renderConfigPanel();
      renderCalendar();
    }

    function renderConfigPanel() {
      configList.innerHTML = '';
      employees.forEach((emp, index) => {
        const div = document.createElement('div');
        div.className = 'employee-card';
        div.innerHTML = `
          <div class="row">
            <input class="name-input" type="text" value="${emp.name}" onchange="updateEmp(${index}, 'name', this.value)" />
            <label class="visibility"><input type="checkbox" ${emp.visible ? 'checked' : ''} onchange="updateEmp(${index}, 'visible', this.checked); renderCalendar();" /> Mostrar</label>
          </div>
          
          <div class="input-group">
            <div>
              <label>Cargo do Funcionário</label>
              <select class="cargo-select" onchange="updateEmp(${index}, 'cargo', this.value); renderCalendar();">
                <option value="">Nenhum (Branco)</option>
                <option value="cargo1" ${emp.cargo === 'cargo1' ? 'selected' : ''}>Cargo 1 (Azul)</option>
                <option value="cargo2" ${emp.cargo === 'cargo2' ? 'selected' : ''}>Cargo 2 (Laranja)</option>
                <option value="cargo3" ${emp.cargo === 'cargo3' ? 'selected' : ''}>Cargo 3 (Amarelo)</option>
              </select>
            </div>
            <div>
              <label>Início da Escala (Trabalho)</label>
              <div style="display:flex; gap:5px;">
                <input type="date" value="${emp.startDate}" onchange="updateEmp(${index}, 'startDate', this.value); renderCalendar();" style="flex:1;" />
                <button class="apply-btn" onclick="renderCalendar()">Aplicar</button>
              </div>
            </div>
          </div>

          <div style="display:flex; justify-content:space-between; align-items:flex-end; border-top: 1px dashed #ccc; padding-top: 10px; margin-top: 10px; margin-bottom: 4px;">
            <label style="margin:0;">Período de Férias</label>
            <button class="link-inline" style="color:#ef4444; font-weight:bold;" onclick="clearVacation(${index}); return false;">🗑️ Limpar Marcação</button>
          </div>
          <div class="input-group">
            <div><label style="font-weight:normal;">Início</label><input type="date" value="${emp.vacationStart}" onchange="updateEmp(${index}, 'vacationStart', this.value); renderCalendar();" /></div>
            <div><label style="font-weight:normal;">Fim</label><input type="date" value="${emp.vacationEnd}" onchange="updateEmp(${index}, 'vacationEnd', this.value); renderCalendar();" /></div>
          </div>

          <div class="input-group" style="border-top: 1px dashed #ccc; padding-top: 10px;">
            <div><label>Escala A - Trab.</label><input type="number" min="0" value="${emp.cycles[0].workDays}" onchange="updateCycle(${index}, 0, 'workDays', this.value)" /></div>
            <div><label>Escala A - Folga</label><input type="number" min="0" value="${emp.cycles[0].offDays}" onchange="updateCycle(${index}, 0, 'offDays', this.value)" /></div>
          </div>
          <div class="input-group">
            <div><label>Escala B - Trab.</label><input type="number" min="0" value="${emp.cycles[1].workDays}" onchange="updateCycle(${index}, 1, 'workDays', this.value)" /></div>
            <div><label>Escala B - Folga</label><input type="number" min="0" value="${emp.cycles[1].offDays}" onchange="updateCycle(${index}, 1, 'offDays', this.value)" /></div>
          </div>
        `;
        configList.appendChild(div);
      });
    }

    function updateEmp(index, field, value) { employees[index][field] = field === 'visible' ? !!value : value; }
    function updateCycle(index, cycleIdx, field, value) { employees[index].cycles[cycleIdx][field] = toInt(value, 0); }
    function selectAll(flag) { employees.forEach(emp => emp.visible = !!flag); renderConfigPanel(); renderCalendar(); }

    function saveData() {
      localStorage.setItem(STORAGE_KEY_V2, JSON.stringify(employees));
      renderCalendar();
      if(document.getElementById('listContainer').style.display === 'block') refreshOccurrencesList();
      alert('Dados salvos com sucesso!');
    }

    function renderCalendar() {
      const month = parseInt(selMonth.value, 10);
      const year = parseInt(selYear.value, 10);
      if (isNaN(month) || isNaN(year)) return;

      const daysInMonth = new Date(year, month + 1, 0).getDate();
      
      // Aplicar classe explícita fixar-nome
      let htmlHeader = '<th class="fixar-nome">Func.</th>';
      for (let d = 1; d <= daysInMonth; d++) {
        const date = new Date(year, month, d);
        htmlHeader += `<th class="dia-quadrado" style="${(date.getDay() === 0 || date.getDay() === 6) ? 'background:#e2e8f0' : ''}">${d}</th>`;
      }
      headerRow.innerHTML = htmlHeader;

      const selected = employees.filter(emp => emp.visible);
      let htmlBody = '';
      selected.forEach(emp => {
        const empIndex = employees.findIndex(e => e.id === emp.id);
        
        // Aplicar classe explícita fixar-nome
        htmlBody += `<tr><td class="fixar-nome" title="${emp.name}">${emp.name}</td>`;

        for (let d = 1; d <= daysInMonth; d++) {
          const date = new Date(year, month, d);
          const dateStr = `${year}-${String(month + 1).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
          
          const occReason = emp.occurrences && emp.occurrences[dateStr];
          const vac = isVacation(date, emp);
          const isOff = !vac && isDayOff(date, emp);

          let content = '', classes = 'dia-quadrado ', title = '';

          if (occReason) {
            classes += 'ocorrencia'; content = '⚠️'; title = `${occReason}`;
          } else if (vac) {
            classes += 'ferias'; title = 'Férias';
          } else if (isOff) {
            classes += 'folga'; content = 'F'; title = 'Folga';
          } else {
            if (emp.cargo === 'cargo1') classes += 'cargo1-bg';
            else if (emp.cargo === 'cargo2') classes += 'cargo2-bg';
            else if (emp.cargo === 'cargo3') classes += 'cargo3-bg';
          }

          htmlBody += `<td class="${classes}" title="${title}" onclick="openModal(${empIndex}, '${dateStr}')">${content}</td>`;
        }
        htmlBody += `</tr>`;
      });
      bodyRows.innerHTML = htmlBody;
      emptyHint.style.display = selected.length ? 'none' : 'block';
    }

    function openModal(empIndex, dateStr) {
      activeEmpIndex = empIndex;
      activeDateStr = dateStr;
      const emp = employees[empIndex];
      const [y, m, d] = dateStr.split('-');
      document.getElementById('occTitle').innerText = `Ausência: ${emp.name} (${d}/${m}/${y})`;
      
      const reasonInput = document.getElementById('occReason');
      const btnDel = document.getElementById('btnDelOcc');
      
      if (emp.occurrences && emp.occurrences[dateStr]) {
        reasonInput.value = emp.occurrences[dateStr];
        btnDel.style.display = 'block';
      } else {
        reasonInput.value = '';
        btnDel.style.display = 'none';
      }
      modal.style.display = 'flex';
      setTimeout(() => reasonInput.focus(), 100);
    }

    function closeModal() { modal.style.display = 'none'; }

    function saveOccurrence() {
      const reason = document.getElementById('occReason').value.trim();
      if (!employees[activeEmpIndex].occurrences) employees[activeEmpIndex].occurrences = {};
      
      if (reason) employees[activeEmpIndex].occurrences[activeDateStr] = reason;
      else delete employees[activeEmpIndex].occurrences[activeDateStr];
      
      closeModal();
      saveData();
    }

    function deleteOccurrence() {
      if (employees[activeEmpIndex].occurrences) delete employees[activeEmpIndex].occurrences[activeDateStr];
      closeModal();
      saveData();
    }

    function toggleOccurrencesList() {
      const calContainer = document.getElementById('calendarContainer');
      const listContainer = document.getElementById('listContainer');
      const controls = document.getElementById('mainControls');
      const details = document.getElementById('configDetails');

      if (listContainer.style.display === 'block') {
        listContainer.style.display = 'none';
        calContainer.style.display = 'block';
        controls.style.display = 'flex';
        details.style.display = 'block';
      } else {
        refreshOccurrencesList();
        calContainer.style.display = 'none';
        controls.style.display = 'none';
        details.style.display = 'none';
        listContainer.style.display = 'block';
      }
    }

    function refreshOccurrencesList() {
      const listBody = document.getElementById('absencesListBody');
      let allOccurrences = [];

      employees.forEach(emp => {
        if (emp.occurrences) {
          for (const [dateStr, reason] of Object.entries(emp.occurrences)) {
            allOccurrences.push({ date: dateStr, name: emp.name, reason: reason });
          }
        }
      });

      allOccurrences.sort((a, b) => b.date.localeCompare(a.date));
      listBody.innerHTML = '';
      if (allOccurrences.length === 0) {
        listBody.innerHTML = `<tr><td colspan="3" style="text-align: center; color: #64748b; padding: 30px;">Nenhuma ausência registada.</td></tr>`;
      } else {
        allOccurrences.forEach(occ => {
          const [y, m, d] = occ.date.split('-');
          listBody.innerHTML += `
            <tr style="border-bottom: 1px solid #e2e8f0;">
              <td style="padding: 10px; text-align: left;">${d}/${m}</td>
              <td style="font-weight: bold; text-align: left; padding: 10px;">${occ.name}</td>
              <td style="text-align: left; padding: 10px; color: #ef4444; font-weight: 500;">${occ.reason}</td>
            </tr>
          `;
        });
      }
    }

    function toggleConfig() {
      const d = document.querySelector('details');
      d.open = !d.open;
    }

    init();
  </script>
</body>
</html>
