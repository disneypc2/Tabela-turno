<html lang="pt-PT">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Gestor de Escalas 2026-2030</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg: #e0ffff; /* Cor Ciano Claro */
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

    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background-color: var(--bg);
      color: var(--text);
      margin: 0;
      padding: 10px;
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
    .row .name-input { flex: 1; font-weight: bold; border: none; border-bottom: 1px solid #ccc; padding: 6px; min-width: 150px; background: transparent; font-size: 1.05rem; }
    .row .visibility { white-space: nowrap; font-size: 0.9rem; color: var(--muted); font-weight: bold; }
    .input-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }
    label { font-size: 0.8rem; color: #475569; display: block; margin-bottom: 4px; font-weight: 600; }
    input[type="number"], input[type="date"], textarea, select.cargo-select { width: 100%; padding: 8px; border: 1px solid var(--border); border-radius: 4px; font-size: 1rem; }
    .bulk-actions { display: flex; gap: 8px; padding: 0 15px 8px; flex-wrap: wrap; }
    .bulk-actions button { padding: 8px 12px; border: 1px solid var(--border); background: #f8fafc; border-radius: 6px; cursor: pointer; font-size: 0.9rem; flex: 1; }
    .link-inline { font-size: 0.85rem; background: none; border: none; cursor: pointer; padding: 0; }

    /* =========================================
       ESTRUTURA BLINDADA (2 TABELAS)
       ========================================= */
    .calendar-wrapper {
      display: flex;
      width: 100%;
      background: var(--surface);
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      overflow: hidden; 
      margin-bottom: 10px;
    }

    /* PAINEL DOS NOMES (Totalmente Fixo) */
    .fixed-panel {
      width: 120px;
      flex-shrink: 0; 
      background-color: #ffffff;
      border-right: 2px solid #94a3b8;
      box-shadow: 3px 0px 5px rgba(0,0,0,0.08);
      z-index: 10;
    }

    /* PAINEL DOS DIAS (Apenas este rola horizontalmente) */
    .scroll-panel {
      flex-grow: 1;
      overflow-x: auto; /* Scroll horizontal apenas aqui */
      -webkit-overflow-scrolling: touch; 
      background-color: #ffffff;
    }

    /* Regras Comuns para as 2 Tabelas */
    .fixed-panel table, 
    .scroll-panel table {
      border-collapse: collapse; 
    }

    .fixed-panel table {
      width: 100%;
      table-layout: fixed;
    }

    .scroll-panel table {
      /* MÁGICA AQUI: OBRIGA A TABELA A CRESCER SEM ESPREMER */
      width: max-content; 
      min-width: 100%;
    }

    .fixed-panel th, .fixed-panel td,
    .scroll-panel th, .scroll-panel td {
      height: 48px; /* Tabela mais alta e confortável */
      border-bottom: 1px solid var(--border);
      padding: 0;
      text-align: center;
      vertical-align: middle;
      font-size: 0.9rem;
      box-sizing: border-box;
    }

    /* Cabeçalhos Comuns */
    .fixed-panel th, 
    .scroll-panel th {
      background-color: #f1f5f9;
      font-weight: 600;
      border-top: 1px solid var(--border);
    }

    /* Especificidades dos Nomes */
    .fixed-panel td {
      text-align: left;
      padding: 0 8px;
      font-weight: bold;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    /* Especificidades dos Dias */
    .scroll-panel th, 
    .scroll-panel td {
      border-right: 1px solid var(--border);
      width: 42px; /* Quadrados confortáveis */
      min-width: 42px;
      max-width: 42px;
      white-space: nowrap; /* Impede os números de saltarem de linha */
    }

    /* Comportamento de clique nas células de dias */
    .scroll-panel td { cursor: pointer; transition: filter 0.15s; }
    .scroll-panel td:active { filter: brightness(0.8); }

    /* ── ESTADOS (Cores) ── */
    .folga      { background-color: var(--success); color: white; font-weight: bold; }
    .ferias     { background-color: var(--vacation); color: white; font-weight: bold; }
    .ocorrencia { background-color: var(--occurrence); color: white; font-weight: bold; }
    .cargo1-bg  { background-color: var(--cargo1); }
    .cargo2-bg  { background-color: var(--cargo2); }
    .cargo3-bg  { background-color: var(--cargo3); }

    .legend { color: var(--muted); font-size: 0.85rem; margin: 6px 0 12px; text-align: center; line-height: 1.6; }
    .save-btn { padding: 10px 14px; background: var(--primary); color: white; border: none; border-radius: 8px; font-size: 0.95rem; cursor: pointer; font-weight: bold; }
    .apply-btn { padding: 8px 12px; background: #3b82f6; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 0.9rem; }
    .empty-hint { color: var(--muted); text-align: center; padding: 14px; font-size: 0.9rem; }
    .actions-bar { position: sticky; top: 0; z-index: 999; display: flex; justify-content: flex-end; gap: 8px; margin-bottom: 12px; padding: 8px; background: #f8fafc; border-bottom: 1px solid #e2e8f0; }

    .list-container { padding: 20px; display: none; background: var(--surface); border-radius: 12px; }
    .list-container table { min-width: 100%; border-collapse: collapse; }
    .list-container th { text-align: left; padding: 10px; border-bottom: 2px solid var(--border); }

    .modal-overlay { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.6); display: none; justify-content: center; align-items: center; z-index: 10000; backdrop-filter: blur(2px); }
    .modal-content { background: #fff; padding: 20px; border-radius: 12px; width: 90%; max-width: 450px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
    .modal-content textarea { height: 100px; margin-top: 5px; resize: vertical; font-family: inherit; width: 100%; }
    .modal-buttons { display: flex; gap: 10px; justify-content: space-between; margin-top: 15px; flex-wrap: wrap; }
    .btn-action { padding: 12px 16px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; flex: 1; text-align: center; font-size: 0.95rem; }
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
  <h1>📅 Escala de Folgas</h1>

  <div class="actions-bar">
    <button class="save-btn" style="background:#f59e0b;" onclick="toggleOccurrencesList()">📋 Relatório de Ausências</button>
    <button class="save-btn" onclick="saveData()">💾 Salvar Tudo</button>
    <button class="save-btn" onclick="toggleConfig()">⚙️ Configurações</button>
  </div>

  <div class="controls" id="mainControls">
    <select id="selMonth" onchange="renderCalendar()"></select>
    <select id="selYear"  onchange="renderCalendar()"></select>
  </div>

  <details id="configDetails">
    <summary>Configurar Escalas (Toque aqui)</summary>
    <div class="bulk-actions">
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
  </details>

  <div class="calendar-wrapper" id="calendarContainer">
    <div class="fixed-panel">
      <table id="nameTable">
        <thead>
          <tr><th>Func.</th></tr>
        </thead>
        <tbody id="nameRows"></tbody>
      </table>
    </div>
    
    <div class="scroll-panel" id="scrollPanel">
      <table id="daysTable">
        <thead>
          <tr id="daysHeaderRow"></tr>
        </thead>
        <tbody id="daysRows"></tbody>
      </table>
    </div>
  </div>
  
  <div id="emptyHint" class="empty-hint" style="display:none;">Nenhum funcionário selecionado.</div>

  <div class="list-container" id="listContainer">
    <button class="save-btn" style="width:100%;margin-bottom:15px;background:var(--muted);" onclick="toggleOccurrencesList()">⬅️ Retornar para a Escala</button>
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
    const startYear = 2026, endYear = 2030, defaultEmployees = 10;
    const STORAGE_KEY_V2 = 'escalaData_v2', LEGACY_KEY_V1 = 'escalaData_v1';
    let employees = [], activeEmpIndex = -1, activeDateStr = '';

    function toInt(n, fb = 0){ const v = parseInt(n, 10); return isNaN(v) ? fb : v; }

    function ensureShape(emp, i){
      if (!emp) emp = {};
      emp.id          = emp.id          ?? (i + 1);
      emp.name        = emp.name        ?? `Funcionário ${i + 1}`;
      emp.startDate   = emp.startDate   ?? '2026-01-01';
      emp.visible     = typeof emp.visible === 'boolean' ? emp.visible : true;
      emp.vacationStart = emp.vacationStart ?? '';
      emp.vacationEnd
