<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>T-REGISTER - Sistema de Cadastro</title>
  <style>
    :root {
      --primary: #4f46e5;
      --primary-hover: #4338ca;
      --bg: #0f172a;
      --card-bg: #1e293b;
      --text: #f8fafc;
      --text-muted: #94a3b8;
      --border: #334155;
      --danger: #ef4444;
      --success: #10b981;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background-color: var(--bg);
      color: var(--text);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 2rem 1rem;
    }

    header {
      margin-bottom: 2rem;
      text-align: center;
    }

    header h1 {
      font-size: 2.5rem;
      color: var(--primary);
      letter-spacing: 2px;
      margin-bottom: 0.5rem;
    }

    header p {
      color: var(--text-muted);
    }

    .container {
      width: 100%;
      max-width: 900px;
      display: grid;
      grid-template-columns: 1fr;
      gap: 2rem;
    }

    @media (min-width: 768px) {
      .container {
        grid-template-columns: 1fr 1fr;
      }
    }

    .card {
      background-color: var(--card-bg);
      padding: 1.5rem;
      border-radius: 12px;
      border: 1px solid var(--border);
      box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.3);
    }

    .card h2 {
      font-size: 1.25rem;
      margin-bottom: 1.25rem;
      border-bottom: 1px solid var(--border);
      padding-bottom: 0.5rem;
    }

    .form-group {
      margin-bottom: 1.25rem;
    }

    label {
      display: block;
      font-size: 0.875rem;
      margin-bottom: 0.5rem;
      color: var(--text-muted);
    }

    input[type="text"],
    input[type="tel"] {
      width: 100%;
      padding: 0.75rem;
      border-radius: 6px;
      border: 1px solid var(--border);
      background-color: #0f172a;
      color: var(--text);
      font-size: 1rem;
      outline: none;
      transition: border-color 0.2s;
    }

    input:focus {
      border-color: var(--primary);
    }

    .checkbox-group {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      margin-bottom: 1rem;
      cursor: pointer;
    }

    .checkbox-group input {
      accent-color: var(--primary);
      width: 16px;
      height: 16px;
      cursor: pointer;
    }

    .cpf-container {
      display: none;
    }

    .cpf-container.active {
      display: block;
    }

    button {
      width: 100%;
      padding: 0.75rem;
      background-color: var(--primary);
      color: white;
      border: none;
      border-radius: 6px;
      font-size: 1rem;
      font-weight: 600;
      cursor: pointer;
      transition: background-color 0.2s;
    }

    button:hover {
      background-color: var(--primary-hover);
    }

    .table-responsive {
      overflow-x: auto;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      text-align: left;
      font-size: 0.875rem;
    }

    th, td {
      padding: 0.75rem;
      border-bottom: 1px solid var(--border);
    }

    th {
      color: var(--text-muted);
      font-weight: 600;
    }

    .btn-delete {
      background-color: transparent;
      color: var(--danger);
      border: 1px solid var(--danger);
      padding: 0.25rem 0.5rem;
      font-size: 0.75rem;
      border-radius: 4px;
      width: auto;
    }

    .btn-delete:hover {
      background-color: var(--danger);
      color: white;
    }

    .empty-msg {
      color: var(--text-muted);
      text-align: center;
      padding: 1rem 0;
    }
  </style>
</head>
<body>

  <header>
    <h1>T-REGISTER</h1>
    <p>Painel de Cadastro de Clientes</p>
  </header>

  <div class="container">
    <!-- Formulario -->
    <div class="card">
      <h2>Novo Cadastro</h2>
      <form id="cadForm">
        <div class="form-group">
          <label for="nome">Nome Completo *</label>
          <input type="text" id="nome" required placeholder="Digite o nome completo">
        </div>

        <div class="form-group">
          <label for="telefone">Número de Telefone *</label>
          <input type="tel" id="telefone" required placeholder="(11) 99999-9999" maxlength="15">
        </div>

        <div class="checkbox-group">
          <input type="checkbox" id="toggleCpf">
          <label for="toggleCpf" style="margin:0; cursor:pointer;">Deseja cadastrar o CPF?</label>
        </div>

        <div class="form-group cpf-container" id="cpfContainer">
          <label for="cpf">CPF</label>
          <input type="text" id="cpf" placeholder="000.000.000-00" maxlength="14">
        </div>

        <button type="submit">Salvar Cadastro</button>
      </form>
    </div>

    <!-- Lista -->
    <div class="card">
      <h2>Clientes Cadastrados</h2>
      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th>Nome</th>
              <th>Telefone</th>
              <th>CPF</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody id="tabelaCorpo">
            <!-- Dados inseridos via JS -->
          </tbody>
        </table>
        <div id="mensagemVazia" class="empty-msg">Nenhum cadastro encontrado.</div>
      </div>
    </div>
  </div>

  <script>
    // Elementos do DOM
    const form = document.getElementById('cadForm');
    const nomeInput = document.getElementById('nome');
    const telefoneInput = document.getElementById('telefone');
    const toggleCpf = document.getElementById('toggleCpf');
    const cpfContainer = document.getElementById('cpfContainer');
    const cpfInput = document.getElementById('cpf');
    const tabelaCorpo = document.getElementById('tabelaCorpo');
    const mensagemVazia = document.getElementById('mensagemVazia');

    // Chave para guardar no LocalStorage
    const STORAGE_KEY = 't_register_clientes';

    // Alternar visibilidade do CPF
    toggleCpf.addEventListener('change', () => {
      if (toggleCpf.checked) {
        cpfContainer.classList.add('active');
      } else {
        cpfContainer.classList.remove('active');
        cpfInput.value = '';
      }
    });

    // Mascara de Telefone
    telefoneInput.addEventListener('input', (e) => {
      let v = e.target.value.replace(/\D/g, '');
      v = v.replace(/^(\d{2})(\d)/g, '($1) $2');
      v = v.replace(/(\d)(\d{4})$/, '$1-$2');
      e.target.value = v;
    });

    // Mascara de CPF
    cpfInput.addEventListener('input', (e) => {
      let v = e.target.value.replace(/\D/g, '');
      v = v.replace(/(\d{3})(\d)/, '$1.$2');
      v = v.replace(/(\d{3})(\d)/, '$1.$2');
      v = v.replace(/(\d{3})(\d{1,2})$/, '$1-$2');
      e.target.value = v;
    });

    // Carregar clientes salvos
    function obterClientes() {
      const dados = localStorage.getItem(STORAGE_KEY);
      return dados ? JSON.parse(dados) : [];
    }

    // Salvar lista atualizada
    function salvarClientes(clientes) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(clientes));
    }

    // Renderizar tabela na tela
    function renderizarTabela() {
      const clientes = obterClientes();
      tabelaCorpo.innerHTML = '';

      if (clientes.length === 0) {
        mensagemVazia.style.display = 'block';
        return;
      }

      mensagemVazia.style.display = 'none';

      clientes.forEach((cliente, index) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${escapeHtml(cliente.nome)}</td>
          <td>${escapeHtml(cliente.telefone)}</td>
          <td>${cliente.cpf ? escapeHtml(cliente.cpf) : '<span style="color:var(--text-muted)">Não informado</span>'}</td>
          <td>
            <button class="btn-delete" onclick="removerCliente(${index})">Excluir</button>
          </td>
        `;
        tabelaCorpo.appendChild(tr);
      });
    }

    // Adicionar novo cliente
    form.addEventListener('submit', (e) => {
      e.preventDefault();

      const novoCliente = {
        nome: nomeInput.value.trim(),
        telefone: telefoneInput.value.trim(),
        cpf: toggleCpf.checked ? cpfInput.value.trim() : null
      };

      const clientes = obterClientes();
      clientes.push(novoCliente);
      salvarClientes(clientes);

      // Limpar formulário
      form.reset();
      cpfContainer.classList.remove('active');
      renderizarTabela();
    });

    // Remover cliente
    window.removerCliente = function(index) {
      const clientes = obterClientes();
      clientes.splice(index, 1);
      salvarClientes(clientes);
      renderizarTabela();
    };

    // Função utilitária para evitar XSS
    function escapeHtml(texto) {
      const div = document.createElement('div');
      div.innerText = texto;
      return div.innerHTML;
    }

    // Inicializar visualização
    renderizarTabela();
  </script>
</body>
</html>
