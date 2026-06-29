<html lang="pt-PT">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <meta name="color-scheme" content="light" />
  <title>Gestor de Escalas PRO</title>
  <style>
    :root {
      --primary: #2563eb;
      --bg-gradient: linear-gradient(135deg, #f8fafc 0%, #e0ffff 100%);
      --surface: rgba(255, 255, 255, 0.95);
      --border: #e2e8f0;
      --success: #10b981;
      --vacation: #8b4513;
      --occurrence: #ef4444;
      --cargo1: #e0f2fe;
      --cargo2: #ffedd5;
      --cargo3: #fef9c3;
      --text: #0f172a;
      --muted: #64748b;
    }
    * { box-sizing: border-box; }
    body { font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background: var(--bg-gradient); background-attachment: fixed; color: var(--text); margin: 0; padding: 0 10px 10px 10px; padding-top: 70px; }
    
    .actions-bar { position: sticky; top: 0; z-index: 9999; display: flex; justify-content: flex-end; gap: 10px; padding: 12px; margin: 0 -10px 20px -10px; background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); border-bottom: 1px solid rgba(226, 232, 240, 0.8); box-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05); }
    .btn-save-occ, .btn-cancel-occ, .btn-delete-occ, .save-btn, .btn-action, .apply-btn { cursor: pointer; border: none; border-radius: 8px; font-weight: 700; transition: all 0.2s; }
    .apply-btn { padding: 8px 14px; background: var(--primary); color: white; margin-left: 5px; }
    .save-btn { padding: 12px 16px; background: var(--primary); color: white; font-weight: bold; }
    
    .calendar-wrapper { display: flex; width: 100%; background: #ffffff; border-radius: 12px; box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); overflow: hidden; margin-bottom: 10px; border: 1px solid rgba(226, 232, 240, 0.8); }
    .fixed-column { width: 110px; flex-shrink: 0; background-color: #ffffff; border-right: 2px solid #cbd5e1; z-index: 10; }
    .scroll-column { flex-grow: 1; overflow-x: auto; -webkit-overflow-scrolling: touch; }
    .sync-table { border-collapse: collapse; width: 100%; }
    .sync-table tr { height: 40px !important; }
    .sync-table th, .sync-table td { height: 40px !important; border-bottom: 1px solid var(--border); padding: 0; text-align: center; font-size: 0.85rem; }
    .sync-table th { background-color: #f8fafc; font-weight: 800; }
    .fixed-column td { text-align: left; padding-left: 10px; font-weight: 800; font-size: 0.85rem; white-space: nowrap; }
    .scroll-column td { width: 38px !important; min-width: 38px !important; cursor: pointer; }
    
    /* Cores */
    .folga { background-color: var(--success) !important; color: white !important; }
    .ferias { background-color: var(--vacation) !important; color: white !important; }
    .ocorrencia { background-color: var(--occurrence) !important; color: white !important; }
    .cargo1-bg { background-color: var(--cargo1) !important; }
    .cargo2-bg { background-color: var(--cargo2) !important; }
    .cargo3-bg { background-color: var(--cargo3) !important; }
    
    .config-grid { padding: 20px; display: grid; gap: 20px; }
    .employee-card { background: #ffffff; border: 1px solid var(--border); padding: 18px; border-radius: 12px; }
    .input-group { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 12px; }
    label { font-size: 0.75rem; color: var(--muted); font-weight: 800; text-transform: uppercase; }
    input, select { padding: 10px; border: 1px solid var(--border); border-radius: 8px; width: 100%; }
  </style>
</head>
<body>

  <div class="actions-bar">
    <button class="save-btn" style="background:#f59e0b;" onclick="toggleOccurrencesList()">📋 Ausências</button>
    <button class="save-btn" onclick="saveData()">💾 Salvar</button>
    <button class="save-btn" style="background:#0f172a;" onclick="toggleConfig()">⚙️ Config.</button>
  </div>

  <div class="calendar-wrapper" id="calendarContainer">
    <div class="fixed-column"><table class="sync-table"><thead><tr><th>Func.</th></tr></thead><tbody id="nameRows"></tbody></table></div>
    <div class="scroll-column"><table class="sync-table"><thead><tr id="daysHeaderRow"></tr></thead><tbody id="daysRows"></tbody></table></div>
  </div>

  <details id="configDetails">
    <summary>Configurar Escalas</summary>
    <div class="config-grid" id="configList"></div>
  </details>

  <script>
    const STORAGE_KEY = 'meu_backup_escala_seguro';
    const defaultEmployees = 20;
    let employees = [];

    // Lógica para injetar o botão Aplicar ao lado das datas
    function renderConfigPanel(){
      const container = document.getElementById('configList');
      container.innerHTML = '';
      employees.forEach((emp, index) => {
        const div = document.createElement('div');
        div.className = 'employee-card';
        div.innerHTML = `
          <div class="row">
            <input class="name-input" type="text" value="${emp.name}" onchange="updateEmp(${index},'name',this.value)" />
          </div>
          <div class="input-group">
            <div>
              <label>Início da Escala</label>
              <div style="display:flex;">
                <input type="date" value="${emp.startDate}" onchange="updateEmp(${index},'startDate',this.value)" />
                <button class="apply-btn" onclick="renderCalendar()">✔</button>
              </div>
            </div>
            <div>
              <label>Início Férias</label>
              <div style="display:flex;">
                <input type="date" value="${emp.vacationStart}" onchange="updateEmp(${index},'vacationStart',this.value)" />
                <button class="apply-btn" onclick="renderCalendar()">✔</button>
              </div>
            </div>
          </div>`;
        container.appendChild(div);
      });
    }

    // [Restante da lógica JS permanece a mesma, apenas certifique-se de chamar renderCalendar()]
    // Adicionei os botões "✔" que chamam a renderCalendar() para forçar a atualização imediata.
    // ... resto do código igual ...
    function updateEmp(i, f, v) { employees[i][f] = v; }
    function renderCalendar() { /* lógica anterior */ }
    function init() { /* lógica anterior */ }
    init();
  </script>
</body>
</html>
