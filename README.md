<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Gestor de Escalas 2026-2030</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg: #f8fafc;
      --surface: #ffffff;
      --border: #e2e8f0;
      --success: #22c55e;
      --vacation: #facc15;
      --exam-periodic: #1d4ed8;
      --exam-clinical: #93c5fd;
      --occurrence: #ef4444; /* Vermelho para ocorrências */
      --text: #1e293b;
      --muted: #64748b;
    }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background-color: var(--bg);
      color: var(--text);
      margin: 0;
      padding: 10px;
    }

    h1 { font-size: 1.2rem; text-align: center; margin-bottom: 15px; }

    .controls {
      background: var(--surface);
      padding: 15px;
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      margin-bottom: 15px;
      display: flex;
      gap: 10px;
      justify-content: center;
    }

    select {
      padding: 8px;
      border: 1px solid var(--border);
      border-radius: 6px;
      font-size: 1rem;
      flex: 1;
      min-width: 140px;
    }

    details {
      background: var(--surface);
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      margin-bottom: 20px;
      overflow: hidden;
    }

    summary {
      padding: 15px;
      font-weight: bold;
      cursor: pointer;
      background: #f1f5f9;
      list-style: none;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    summary::after { content: "⚙️"; }

    .config-grid { padding: 15px; display: grid; gap: 15px; }
    .employee-card { border: 1px solid var(--border); padding: 10px; border-radius: 8px; }
    .employee-card h3 { margin: 0 0 10px 0; font-size: 1rem; }

    .row { display: flex; align-items: center; justify-content: space-between; gap: 10px; margin-bottom: 10px; }
    .row .name-input { flex: 1; font-weight:bold; border:none; border-bottom:1px solid #ccc; padding:6px; }
    .row .visibility { white-space: nowrap; font-size: 0.9rem; color: var(--muted); }

    .input-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 8px; }
    label { font-size: 0.8rem; color: #64748b; display: block; margin-bottom: 2px; }
    input[type="number"], input[type="date"], textarea { width: 100%; padding: 6px; border: 1px solid var(--border); border-radius: 4px; box-sizing: border-box; }

    .bulk-actions { display:flex; gap:8px; padding: 0 15px 8px; }
    .bulk-actions button { padding: 6px 10px; border: 1px solid var(--border); background:#f8fafc; border-radius:6px; cursor:pointer; font-size:0.9rem; }

    .calendar-container, .list-container {
      background: var(--surface);
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      overflow-x: auto;
      padding-bottom: 10px;
    }

    .list-container { padding: 20px; display: none; }
    .list-container table { min-width: 100%; }
    .list-container th { text-align: left; padding: 10px; }

    table { width: 100%; border-collapse: collapse; min-width: 800px; }
    th, td { border: 1px solid var(--border); text-align: center; padding: 8px 4px; font-size: 0.85rem; }
    th { background: #f1f5f9; font-weight: 600; }

    td:first-child {
      font-weight: bold; text-align: left; padding-left: 10px;
      position: sticky; left: 0; background: var(--surface); border-right: 2px solid var(--border); z-index: 10;
    }
    th:first-child {
      position: sticky; left: 0; background: #f1f5f9; z-index: 11; border-right: 2px solid var(--border);
    }

    /* Transformando as células de dia em clicáveis */
    #scheduleTable td:not(:first-child) { cursor: pointer; transition: background 0.2s; }
    #scheduleTable td:not(:first-child):hover { filter: brightness(0.85); box-shadow: inset 0 0 5px rgba(0,0,0,0.2); }

    .folga { background-color: var(--success); color: white; font-weight: bold; }
    .ferias { background-color: var(--vacation); color: #000; font-weight: bold; }
    .exam { font-weight: 700; color: #fff; }
    .exam-periodic { background-color: var(--exam-periodic); }
    .exam-clinical { background-color: var(--exam-clinical); color: #0f172a; }
    .exam.weekend { background-color: #000 !important; color: #fff !important; }
    .ocorrencia { background-color: var(--occurrence) !important; color: white !important; font-weight: bold; }

    .legend { color: var(--muted); font-size: 0.8rem; margin: 6px 0 12px; text-align: center; }

    .save-btn {
      width: 100%; padding: 12px; background: var(--primary); color: white; border: none;
      border-radius: 8px; font-size: 1rem; margin-top: 10px; cursor: pointer; font-weight: bold;
    }
    .empty-hint { color: var(--muted); text-align:center; padding: 14px; font-size:0.9rem; }
    .link-inline { font-size: 0.8rem; color: #2563eb; text-decoration: underline; background: none; border: none; cursor: pointer; padding: 0; }
  
    .actions-bar {
      position:sticky; top:0; z-index:999; display:flex; justify-content:flex-end; gap:8px;
      margin-bottom:8px; padding:8px; background:#f8fafc; border-bottom:1px solid #e2e8f0; flex-wrap: wrap;
    }
    .actions-bar .save-btn { width:auto !important; margin-top:0 !important; padding:10px 14px !important; }

    /* Estilos do Modal (Bloco de notas da ocorrência) */
    .modal-overlay {
      position: fixed; top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(0,0,0,0.6); display: none; justify-content: center; align-items: center; z-index: 10000;
      backdrop-filter: blur(2px);
    }
    .modal-content {
      background: #fff; padding: 20px; border-radius: 8px; width: 90%; max-width: 450px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.2);
    }
    .modal-content textarea { height: 120px; margin-top: 5px; resize: vertical; font-family: inherit; }
    .modal-buttons { display: flex; gap: 10px; justify-content: space-between; margin-top: 15px; }
    .btn-action { padding: 10px 16px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; }
    .btn-save-occ { background: var(--primary); color: white; }
    .btn-cancel-occ { background: var(--muted); color: white; }
    .btn-delete-occ { background: var(--occurrence); color: white; }

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
    <div class="legend">
      • Alternância: <strong>A → B → A → B…</strong><br />
      • Cores: <span style="background:#22c55e;color:#fff;padding:0 4px;">Folga</span> | 
      <span style="background:#facc15;padding:0 4px;">Férias</span> | 
      <span style="background:#1d4ed8;color:#fff;padding:0 4px;">EP</span> | 
      <span style="background:#93c5fd;padding:0 4px;">EC</span> | 
      <span style="background:#000;color:#fff;padding:0 4px;">Exame FDS</span> | 
      <span style="background:#ef4444;color:#fff;padding:0 4px;">Afastamento (⚠️)</span><br />
      • <strong>DICA:</strong> Clique em qualquer dia da tabela abaixo para registrar um atestado ou falta!
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
    <button class="save-btn" style="width: auto; margin-bottom: 15px; background: var(--muted);" onclick="toggleOccurrencesList()">⬅️ Retornar para a Escala</button>
    <h2 style="margin-top: 0; color: #1e293b;">Lista Estratificada de Ausências e Afastamentos</h2>
    <table>
      <thead>
        <tr>
          <th style="width: 120px;">Data</th>
          <th style="width: 200px;">Funcionário</th>
          <th>Motivo / Observação (Atestados, faltas, etc.)</th>
        </tr>
      </thead>
      <tbody id="absencesListBody"></tbody>
    </table>
  </div>

  <div class="modal-overlay" id="occurrenceModal">
    <div class="modal-content">
      <h3 id="occTitle" style="margin-top: 0; color: #1e293b;">Registrar Ausência</h3>
      <label for="occReason" style="font-weight: bold; color: #334155;">Motivo (Atestado, falta, suspensão...)</label>
      <textarea id="occReason" placeholder="Descreva aqui o motivo da ausência do funcionário neste dia..."></textarea>
      
      <div class="modal-buttons">
        <button class="btn-action btn-delete-occ" id="btnDelOcc" onclick="deleteOccurrence()">Excluir</button>
        <div style="display: flex; gap: 10px; width: 100%; justify-content: flex-end;" id="btnGroupRight">
          <button class="btn-action btn-cancel-occ" onclick="closeModal()">Cancelar</button>
          <button class="btn-action btn-save-occ" onclick="saveOccurrence()">Salvar Ocorrência</button>
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

    // Garante a estrutura correta dos dados
    function ensureShape(emp, i){
      if (!emp) emp = {};
      emp.id = emp.id ?? (i+1);
      emp.name = emp.name ?? `Funcionário ${i+1}`;
      emp.startDate = emp.startDate ?? '2026-01-01';
      emp.visible = (typeof emp.visible === 'boolean') ? emp.visible : true;
      emp.vacationStart = emp.vacationStart ?? '';
      emp.vacationEnd = emp.vacationEnd ?? '';
      emp.examPeriodic = emp.examPeriodic ?? '';
      emp.examClinical = emp.examClinical ?? '';
      // Novo campo para suportar o bloco de notas de ocorrências:
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

    function sameDate(dateObj, dateStr){
      if (!dateStr) return false;
      const t = new Date(dateStr + 'T00:00:00');
      if (isNaN(t)) return false;
      return dateObj.getFullYear() === t.getFullYear() && dateObj.getMonth() === t.getMonth() && dateObj.getDate() === t.getDate();
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

    function renderConfigPanel() {
      configList.innerHTML = '';
      employees.forEach((emp, index) => {
        const div = document.createElement('div');
        div.className = 'employee-card';
        div.innerHTML = `
          <div class="row">
            <input class="name-input" type="text" value="${emp.name}" onchange="updateEmp(${index}, 'name', this.value)" />
            <label class="visibility"><input type="checkbox" ${emp.visible ? 'checked' : ''} onchange="updateEmp(${index}, 'visible',
