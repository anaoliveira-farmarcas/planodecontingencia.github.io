<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard de Projetos</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@3.7.1/dist/chart.min.js"></script>
    <!-- Chart.js Annotation Plugin -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chartjs-plugin-annotation/1.4.0/chartjs-plugin-annotation.min.js"></script>
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <!-- Google Font: Rubik (com ExtraBold 800) -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700;800&display=swap" rel="stylesheet">
    
    <style>
        /* Estilos personalizados para o Dashboard */
        body {
            font-family: 'Rubik', sans-serif; /* Alterado para Rubik */
            background-color: #f3f4f6; /* Fundo cinza claro */
        }

        /* Classes de Status */
        .status-select {
            padding: 4px 8px;
            border-radius: 6px;
            border: 1px solid #d1d5db;
            font-weight: 500;
            -webkit-appearance: none;
            -moz-appearance: none;
            appearance: none;
            background-position: right 8px center;
            background-repeat: no-repeat;
            background-size: 14px;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 20 20' fill='none' stroke='%236b7280' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpath d='M6 9l4 4 4-4'/%3e%3c/svg%3e");
        }
        .status-default {
            background-color: #f3f4f6;
            color: #4b5563;
        }
        .status-pendente {
            background-color: #fee2e2; /* Fundo vermelho claro */
            color: #b91c1c; /* Texto vermelho escuro */
            border-color: #fca5a5; /* Borda vermelha */
        }
        .status-andamento {
            background-color: #dbeafe; /* Fundo azul claro */
            color: #1d4ed8; /* Texto azul escuro */
            border-color: #93c5fd; /* Borda azul */
        }
        .status-pendente-associa {
            background-color: #fef9c3; /* Fundo amarelo claro */
            color: #a16207; /* Texto amarelo escuro */
            border-color: #fde047; /* Borda amarela */
        }
        .status-finalizado {
            background-color: #d1fae5; /* Fundo verde claro */
            color: #047857; /* Texto verde escuro */
            border-color: #6ee7b7; /* Borda verde */
        }
        
        /* Inputs de indicador na tabela */
        .indicator-input-table {
            width: 70px;
            padding: 4px 8px;
            border-radius: 6px;
            border: 1px solid #d1d5db;
            text-align: center;
            background-color: #f9fafb;
            transition: all 0.2s ease-in-out;
        }
        .indicator-input-table:focus {
            outline: none;
            border-color: #FF5510;
            background-color: #fff;
            box-shadow: 0 0 0 2px rgba(255, 85, 16, 0.2);
        }
        .indicator-input-table::placeholder {
            color: #9ca3af;
        }

        /* Tabela "colada" */
        .sticky-header th {
            position: sticky;
            top: 0;
            background-color: #f9fafb;
            z-index: 10;
        }
        
        /* Scrollbar customizado (opcional) */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        ::-webkit-scrollbar-thumb {
            background: #c1c1c1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #a1a1a1;
        }

        /* Ocultar setas de input number */
        input[type=number]::-webkit-inner-spin-button,
        input[type=number]::-webkit-outer-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type=number] {
            -moz-appearance: textfield;
        }

        /* Animação do Toast */
        #toast {
            transition: transform 0.3s ease-in-out, opacity 0.3s ease-in-out;
        }
        
        /* Ícone de observação preenchido */
        .obs-filled {
            color: #3b82f6; /* Azul */
        }
        .obs-filled:hover {
            color: #1d4ed8; /* Azul escuro */
        }

    </style>
</head>
<body class="bg-gray-100">

    <!-- Sidebar de Filtros (Inicialmente oculta em telas pequenas) -->
    <aside id="filter-sidebar" class="fixed top-0 left-0 z-30 w-72 h-screen bg-white shadow-lg p-6 transform -translate-x-full md:translate-x-0 transition-transform duration-300 ease-in-out">
        <h3 class="text-xl font-extrabold text-gray-800 mb-6">Filtros</h3>
        
        <div class="space-y-4 overflow-y-auto h-[calc(100vh-160px)] pr-2">
            <div>
                <label for="filter-analista" class="block text-sm font-medium text-gray-700">Analista</label>
                <select id="filter-analista" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <div>
                <label for="filter-ge" class="block text-sm font-medium text-gray-700">GE</label>
                <select id="filter-ge" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <div>
                <label for="filter-associado" class="block text-sm font-medium text-gray-700">Associado</label>
                <select id="filter-associado" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <div>
                <label for="filter-uf" class="block text-sm font-medium text-gray-700">UF</label>
                <select id="filter-uf" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <div>
                <label for="filter-anjo" class="block text-sm font-medium text-gray-700">Anjo</label>
                <select id="filter-anjo" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <div>
                <label for="filter-bandeira" class="block text-sm font-medium text-gray-700">Bandeira</label>
                <select id="filter-bandeira" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <!-- FILTRO DE PROJETO ADICIONADO AQUI -->
            <div>
                <label for="filter-projeto" class="block text-sm font-medium text-gray-700">Projeto</label>
                <select id="filter-projeto" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></select>
            </div>
            <div>
                <label for="filter-cnpj" class="block text-sm font-medium text-gray-700">CNPJ</label>
                <input type="text" id="filter-cnpj" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50" placeholder="Filtrar por CNPJ">
            </div>
            <div>
                <label for="filter-loja" class="block text-sm font-medium text-gray-700">Loja</label>
                <input type="text" id="filter-loja" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50" placeholder="Filtrar por nome da loja">
            </div>
        </div>

        <div class="absolute bottom-6 left-6 right-6 space-y-3">
            <button id="apply-filters-btn" class="w-full bg-[#FF5510] text-white py-2 px-4 rounded-lg font-semibold hover:bg-[#E04A0E] transition-colors shadow">
                Aplicar Filtros
            </button>
            <button id="clear-filters-btn" class="w-full bg-gray-200 text-gray-700 py-2 px-4 rounded-lg font-medium hover:bg-gray-300 transition-colors">
                Limpar Filtros
            </button>
        </div>
    </aside>

    <!-- Conteúdo Principal -->
    <main id="main-content" class="md:ml-72 transition-all duration-300 ease-in-out p-6">
        
        <!-- Header (KPIs e Ações) -->
        <header class="mb-6">
            <!-- Barra de Navegação Superior -->
            <nav class="bg-white rounded-lg shadow-sm p-4 flex justify-between items-center mb-6">
                <div class="flex items-center">
                    <!-- Botão de Menu (Mobile) -->
                    <button id="toggle-filter-btn" class="text-gray-600 md:hidden mr-4 p-2 rounded-md hover:bg-gray-100">
                        <i class="fa-solid fa-bars fa-lg"></i>
                    </button>
                    <h1 class="text-3xl font-extrabold text-[#FF5510]">Dashboard de Projetos</h1> <!-- Alterado para font-extrabold -->
                </div>
                <button id="open-modal-btn" class="bg-[#FF5510] text-white py-2 px-5 rounded-lg font-semibold hover:bg-[#E04A0E] transition-colors shadow flex items-center space-x-2">
                    <i class="fa-solid fa-plus"></i>
                    <span>Inserir Loja</span>
                </button>
            </nav>

            <!-- KPIs -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-md border-l-4 border-black">
                    <h4 class="text-sm font-medium text-gray-500 mb-1 uppercase">Total de Lojas</h4>
                    <p id="kpi-total-lojas" class="text-3xl font-extrabold text-gray-900">0</p> <!-- Alterado para font-extrabold -->
                </div>
                <div class="bg-white p-5 rounded-xl shadow-md border-l-4 border-red-500">
                    <h4 class="text-sm font-medium text-gray-500 mb-1 uppercase">Pendentes</h4>
                    <p id="kpi-projetos-pendentes" class="text-3xl font-extrabold text-gray-900">0</p> <!-- Alterado para font-extrabold -->
                </div>
                <div class="bg-white p-5 rounded-xl shadow-md border-l-4 border-yellow-400">
                    <h4 class="text-sm font-medium text-gray-500 mb-1 uppercase">Pendentes Associado</h4>
                    <p id="kpi-projetos-pendentes-associa" class="text-3xl font-extrabold text-gray-900">0</p> <!-- Alterado para font-extrabold -->
                </div>
                <div class="bg-white p-5 rounded-xl shadow-md border-l-4 border-blue-500">
                    <h4 class="text-sm font-medium text-gray-500 mb-1 uppercase">Em Andamento</h4>
                    <p id="kpi-em-andamento" class="text-3xl font-extrabold text-gray-900">0</p> <!-- Alterado para font-extrabold -->
                </div>
                <div class="bg-white p-5 rounded-xl shadow-md border-l-4 border-green-500">
                    <h4 class="text-sm font-medium text-gray-500 mb-1 uppercase">Projetos Finalizados</h4>
                    <p id="kpi-projetos-finalizados" class="text-3xl font-extrabold text-gray-900">0</p> <!-- Alterado para font-extrabold -->
                </div>
            </div>
        </header>

        <!-- =================================== -->
        <!-- ===== NOVO: Gráfico Consolidado ===== -->
        <!-- =================================== -->
        <section class="mb-6 bg-white rounded-lg shadow-sm p-6">
            <h2 class="text-2xl font-extrabold text-gray-800 mb-4">Gráfico Consolidado por Projeto</h2>
            
            <!-- Filtros do Gráfico -->
            <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-4 items-end">
                <div>
                    <label for="chart-filter-project" class="block text-sm font-medium text-gray-700">Projeto</label>
                    <select id="chart-filter-project" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        <option value="" disabled selected>Selecione um projeto</option>
                    </select>
                </div>
                <div>
                    <label for="chart-filter-ge" class="block text-sm font-medium text-gray-700">GE</label>
                    <select id="chart-filter-ge" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        <option value="">Todos</option>
                    </select>
                </div>
                <div>
                    <label for="chart-filter-cnpj-loja" class="block text-sm font-medium text-gray-700">CNPJ ou Loja</label>
                    <input type="text" id="chart-filter-cnpj-loja" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50" placeholder="Filtrar por CNPJ ou Loja...">
                </div>
                <button id="generate-chart-btn" class="w-full md:w-auto bg-[#FF5510] text-white py-2 px-5 rounded-lg font-semibold hover:bg-[#E04A0E] transition-colors shadow h-10">
                    Gerar Gráfico
                </button>
            </div>
            
            <!-- Canvas do Gráfico -->
            <div class="relative h-96">
                <canvas id="consolidated-chart"></canvas>
            </div>
        </section>
        <!-- =================================== -->
        <!-- === FIM: Gráfico Consolidado === -->
        <!-- =================================== -->


        <!-- Tabela de Projetos -->
        <div class="bg-white rounded-lg shadow-sm overflow-hidden">
            <!-- Barra de Pesquisa da Tabela -->
            <div class="p-4 border-b">
                <div class="relative">
                    <span class="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400">
                        <i class="fa-solid fa-search"></i>
                    </span>
                    <input type="text" id="search-input" placeholder="Pesquisar por Loja ou CNPJ..." class="w-full pl-10 pr-4 py-2 rounded-lg border border-gray-300 focus:outline-none focus:ring-2 focus:ring-[#FF5510] focus:border-transparent">
                </div>
            </div>

            <!-- Container da Tabela com Scroll -->
            <div class="overflow-x-auto max-h-[calc(100vh-320px)]">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50 sticky-header">
                        <tr>
                            <!-- Dados da Loja -->
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">Loja</th>
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">CNPJ</th>
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">Analista</th>
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">GE</th>
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">Associado</th>
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">Bandeira</th>
                            
                            <!-- Dados do Projeto -->
                            <th scope="col" class="px-4 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider">Projeto</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Status</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Meta</th>
                            
                            <!-- Indicadores (Novo Período) -->
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Ago/25</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Set/25</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Out/25</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Nov/25</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Dez/25</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Jan/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Fev/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Mar/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Abr/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Mai/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Jun/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Jul/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Ago/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Set/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Out/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Nov/26</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Dez/26</th>
                            
                            <!-- Ações -->
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Obs</th>
                            <th scope="col" class="px-4 py-3 text-center text-xs font-bold text-gray-600 uppercase tracking-wider">Ações</th>
                        </tr>
                    </thead>
                    <tbody id="project-table-body" class="bg-white divide-y divide-gray-200">
                        <!-- Linhas serão injetadas pelo JavaScript -->
                        <tr>
                            <td colspan="29" class="text-center p-8 text-gray-500">
                                Carregando dados...
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </main>

    <!-- Toast Notification -->
    <div id="toast" class="fixed top-6 right-6 p-4 rounded-lg text-white shadow-lg transform translate-x-full opacity-0 z-50">
        Mensagem
    </div>

    <!-- Modal: Inserir/Editar Loja -->
    <div id="store-modal" class="fixed inset-0 z-40 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-3xl max-h-[90vh] overflow-hidden flex flex-col">
            <header class="flex justify-between items-center p-5 border-b">
                <h2 id="store-modal-title" class="text-xl font-extrabold text-gray-800">Adicionar Nova Loja ao Projeto</h2>
                <button id="close-modal-btn" class="text-gray-500 hover:text-gray-800 p-1">
                    <i class="fa-solid fa-times fa-lg"></i>
                </button>
            </header>
            
            <form id="store-form" class="p-6 overflow-y-auto">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Coluna 1 -->
                    <div class="space-y-4">
                        <div>
                            <label for="nome-loja" class="block text-sm font-medium text-gray-700">Nome da Loja</label>
                            <input type="text" id="nome-loja" name="nome-loja" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                        <div>
                            <label for="cnpj" class="block text-sm font-medium text-gray-700">CNPJ (ID)</label>
                            <input type="text" id="cnpj" name="cnpj" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50 read-only:bg-gray-100 read-only:text-gray-500">
                        </div>
                        <div>
                            <label for="analista" class="block text-sm font-medium text-gray-700">Analista</label>
                            <input type="text" id="analista" name="analista" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                        <div>
                            <label for="ge" class="block text-sm font-medium text-gray-700">GE</label>
                            <input type="text" id="ge" name="ge" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                        <div>
                            <label for="associado" class="block text-sm font-medium text-gray-700">Associado</label>
                            <input type="text" id="associado" name="associado" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                    </div>
                    
                    <!-- Coluna 2 -->
                    <div class="space-y-4">
                        <div>
                            <label for="bandeira" class="block text-sm font-medium text-gray-700">Bandeira</label>
                            <input type="text" id="bandeira" name="bandeira" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                         <div>
                            <label for="uf" class="block text-sm font-medium text-gray-700">UF</label>
                            <input type="text" id="uf" name="uf" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                        <div>
                            <label for="anjo" class="block text-sm font-medium text-gray-700">Anjo</label>
                            <input type="text" id="anjo" name="anjo" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                        </div>
                        
                        <!-- Campos do Projeto (visíveis no modo 'Criar') -->
                        <div id="form-project-group">
                            <label for="nome-projeto" class="block text-sm font-medium text-gray-700">Projeto</label>
                            <select id="nome-projeto" name="nome-projeto" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50">
                                <option value="" disabled selected>Selecione um projeto</option>
                            </select>
                        </div>
                        <div id="form-meta-group">
                            <label for="meta" class="block text-sm font-medium text-gray-700">Meta (Opcional)</label>
                            <input type="number" id="meta" name="meta" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50" placeholder="Ex: 5000">
                        </div>
                        <div id="form-observation-group">
                            <label for="observation" class="block text-sm font-medium text-gray-700">Observação (Opcional)</label>
                            <textarea id="observation" name="observation" rows="3" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50"></textarea>
                        </div>
                    </div>
                </div>
            </form>
            
            <footer class="flex justify-end items-center p-5 border-t bg-gray-50">
                <button id="cancel-modal-btn" type="button" class="bg-gray-200 text-gray-700 py-2 px-5 rounded-lg font-medium hover:bg-gray-300 transition-colors mr-3">
                    Cancelar
                </button>
                <button id="save-store-btn" type="submit" form="store-form" class="bg-[#FF5510] text-white py-2 px-5 rounded-lg font-semibold hover:bg-[#E04A0E] transition-colors shadow">
                    Salvar
                </button>
            </footer>
        </div>
    </div>
    
    <!-- Modal: Gráfico (Individual) -->
    <div id="chart-modal" class="fixed inset-0 z-40 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-4xl">
            <header class="flex justify-between items-center p-5 border-b">
                <h2 id="chart-modal-title" class="text-xl font-extrabold text-gray-800">Evolução do Indicador</h2>
                <button id="close-chart-modal-btn" class="text-gray-500 hover:text-gray-800 p-1">
                    <i class="fa-solid fa-times fa-lg"></i>
                </button>
            </header>
            <div class="p-6">
                <!-- Controles de Formato -->
                <div class="mb-4 flex justify-center space-x-6">
                    <label class="inline-flex items-center">
                        <input type="radio" name="chart-format" value="number" class="form-radio text-[#FF5510]" checked>
                        <span class="ml-2">Número</span>
                    </label>
                    <label class="inline-flex items-center">
                        <input type="radio" name="chart-format" value="currency" class="form-radio text-[#FF5510]">
                        <span class="ml-2">Moeda (R$)</span>
                    </label>
                    <label class="inline-flex items-center">
                        <input type="radio" name="chart-format" value="percent" class="form-radio text-[#FF5510]">
                        <span class="ml-2">Percentual (%)</span>
                    </label>
                </div>
                <!-- Canvas do Gráfico -->
                <canvas id="indicator-chart"></canvas>
            </div>
        </div>
    </div>
    
    <!-- Modal: Confirmação de Exclusão -->
    <div id="delete-confirm-modal" class="fixed inset-0 z-40 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-md">
            <header class="flex justify-between items-center p-5 border-b">
                <h2 class="text-xl font-extrabold text-gray-800">Confirmar Exclusão</h2>
            </header>
            <div class="p-6">
                <p id="delete-modal-text" class="text-gray-700 mb-6">Tem certeza que deseja excluir este projeto?</p>
            </div>
            <footer class="flex justify-end items-center p-5 border-t bg-gray-50">
                <button id="cancel-delete-btn" class="bg-gray-200 text-gray-700 py-2 px-5 rounded-lg font-medium hover:bg-gray-300 transition-colors mr-3">
                    Cancelar
                </button>
                <button id="confirm-delete-btn" class="bg-red-600 text-white py-2 px-5 rounded-lg font-semibold hover:bg-red-700 transition-colors shadow">
                    Excluir
                </button>
            </footer>
        </div>
    </div>

    <!-- Modal: Observação -->
    <div id="obs-modal" class="fixed inset-0 z-40 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-lg">
            <header class="flex justify-between items-center p-5 border-b">
                <h2 id="obs-modal-title" class="text-xl font-extrabold text-gray-800">Observações</h2>
                <button id="close-obs-modal-btn" class="text-gray-500 hover:text-gray-800 p-1">
                    <i class="fa-solid fa-times fa-lg"></i>
                </button>
            </header>
            <div class="p-6">
                <textarea id="obs-modal-textarea" rows="8" class="w-full rounded-md border-gray-300 shadow-sm focus:border-[#FF5510] focus:ring focus:ring-[#FF5510] focus:ring-opacity-50" placeholder="Digite as observações aqui..."></textarea>
            </div>
            <footer class="flex justify-end items-center p-5 border-t bg-gray-50">
                <button id="cancel-obs-btn" class="bg-gray-200 text-gray-700 py-2 px-5 rounded-lg font-medium hover:bg-gray-300 transition-colors mr-3">
                    Cancelar
                </button>
                <button id="save-obs-btn" class="bg-[#FF5510] text-white py-2 px-5 rounded-lg font-semibold hover:bg-[#E04A0E] transition-colors shadow">
                    Salvar Observação
                </button>
            </footer>
        </div>
    </div>


    <script type="module">
        // --- VARIÁVEIS GLOBAIS E CONFIGURAÇÃO ---
        
        // Chave para salvar no "baú" do navegador (localStorage)
        // **NÃO É FIREBASE**: Isso salva os dados apenas no seu computador.
        const LOCAL_STORAGE_KEY = 'meusProjetosDashboard';

        const projectList = [
            "MIX DE PRODUTOS", "PRECIFICAÇÃO", "PBM", "PEC", 
            "SETORIZAÇÃO (ESTACIONAMENTO)", "MARCAS PROPRIAS", 
            "GC (FARMACINHA)", "DELIVERY (E-DELIVERY)", "INSPIRE +", 
            "DERMACLUB", "SERVIÇOS FARMACEUTICOS", "CAMPANHA IDOSO", 
            "MEDICAMENTOS ESPECIAIS", "FIDELIZA MAIS"
        ];
        
        const statusList = [
            { value: 'pendente', text: 'Pendente', class: 'status-pendente' },
            { value: 'andamento', text: 'Em Andamento', class: 'status-andamento' },
            { value: 'pendente-associa', text: 'Pendente Associado', class: 'status-pendente-associa' },
            { value: 'finalizado', text: 'Finalizado', class: 'status-finalizado' }
        ];
        
        // --- PERÍODO DE INDICADORES ATUALIZADO (AGO/25 - DEZ/26) ---
        const indicatorMonths = [
            { id: 'aug2025', label: 'Ago/2025' }, { id: 'sep2025', label: 'Set/2025' },
            { id: 'oct2025', label: 'Out/2025' }, { id: 'nov2025', label: 'Nov/2025' },
            { id: 'dec2025', label: 'Dez/2025' }, { id: 'jan2026', label: 'Jan/2026' },
            { id: 'feb2026', label: 'Fev/2026' }, { id: 'mar2026', label: 'Mar/2026' },
            { id: 'apr2026', label: 'Abr/2026' }, { id: 'may2026', label: 'Mai/2026' },
            { id: 'jun2026', label: 'Jun/2026' }, { id: 'jul2026', label: 'Jul/2026' },
            { id: 'aug2026', label: 'Ago/2026' }, { id: 'sep2026', label: 'Set/2026' },
            { id: 'oct2026', label: 'Out/2026' }, { id: 'nov2026', label: 'Nov/2026' },
            { id: 'dec2026', label: 'Dez/2026' }
        ];
        const chartLabels = [
            'Ago/25', 'Set/25', 'Out/25', 'Nov/25', 'Dez/25', 
            'Jan/26', 'Fev/26', 'Mar/26', 'Abr/26', 'Mai/26', 'Jun/26', 'Jul/26', 
            'Ago/26', 'Set/26', 'Out/26', 'Nov/26', 'Dez/26'
        ];

        // Variáveis Globais
        let allFlattenedData = []; // Cache de dados
        
        // Gráfico
        let currentChartInstance = null; 
        let consolidatedChartInstance = null; 
        let currentChartRowData = null; 

        // --- ELEMENTOS DO DOM ---
        // (principais)
        const openModalBtn = document.getElementById('open-modal-btn');
        const storeModal = document.getElementById('store-modal');
        const storeForm = document.getElementById('store-form');
        const projectSelect = document.getElementById('nome-projeto');
        const toast = document.getElementById('toast');
        const projectTableBody = document.getElementById('project-table-body');
        
        // (filtros)
        const searchInput = document.getElementById('search-input');
        const sidebar = document.getElementById('filter-sidebar');
        const mainContent = document.getElementById('main-content');
        const toggleFilterBtn = document.getElementById('toggle-filter-btn');
        const applyFiltersBtn = document.getElementById('apply-filters-btn');
        const clearFiltersBtn = document.getElementById('clear-filters-btn');
        const filterAnalista = document.getElementById('filter-analista');
        const filterGe = document.getElementById('filter-ge');
        const filterAssociado = document.getElementById('filter-associado');
        const filterUf = document.getElementById('filter-uf');
        const filterAnjo = document.getElementById('filter-anjo');
        const filterBandeira = document.getElementById('filter-bandeira');
        const filterProjeto = document.getElementById('filter-projeto'); // NOVO
        const filterCnpj = document.getElementById('filter-cnpj');
        const filterLoja = document.getElementById('filter-loja');

        // (KPIs)
        const kpiTotalLojas = document.getElementById('kpi-total-lojas'); 
        const kpiProjetosPendentes = document.getElementById('kpi-projetos-pendentes');
        const kpiProjetosPendentesAssocia = document.getElementById('kpi-projetos-pendentes-associa');
        const kpiProjetosFinalizados = document.getElementById('kpi-projetos-finalizados');
        const kpiEmAndamento = document.getElementById('kpi-em-andamento');
        
        // (Modal Gráfico Individual)
        const chartModal = document.getElementById('chart-modal');
        const closeChartModalBtn = document.getElementById('close-chart-modal-btn');
        const chartModalTitle = document.getElementById('chart-modal-title');
        const chartCanvas = document.getElementById('indicator-chart').getContext('2d');

        // (Modal Gráfico Consolidado) 
        const consolidatedChartCanvas = document.getElementById('consolidated-chart').getContext('2d');
        const chartFilterProject = document.getElementById('chart-filter-project');
        const chartFilterGe = document.getElementById('chart-filter-ge');
        const chartFilterCnpjLoja = document.getElementById('chart-filter-cnpj-loja');
        const generateChartBtn = document.getElementById('generate-chart-btn');

        // (Modal Exclusão)
        const deleteConfirmModal = document.getElementById('delete-confirm-modal');
        const cancelDeleteBtn = document.getElementById('cancel-delete-btn');
        const confirmDeleteBtn = document.getElementById('confirm-delete-btn');
        
        // (Modal Inserir)
        const closeModalBtn = document.getElementById('close-modal-btn');
        const cancelModalBtn = document.getElementById('cancel-modal-btn');
        
        // (Modal Observação)
        const obsModal = document.getElementById('obs-modal');
        const obsModalTitle = document.getElementById('obs-modal-title');
        const obsModalTextarea = document.getElementById('obs-modal-textarea');
        const closeObsModalBtn = document.getElementById('close-obs-modal-btn');
        const cancelObsBtn = document.getElementById('cancel-obs-btn');
        const saveObsBtn = document.getElementById('save-obs-btn');

        
        // --- FUNÇÕES AUXILIARES (Modal, Toast, Status, Sidebar) ---

        function getRandomColor() {
            const letters = '0123456789ABCDEF';
            let color = '#';
            for (let i = 0; i < 6; i++) {
                color += letters[Math.floor(Math.random() * 16)];
            }
            return color;
        }

        function populateProjectSelect() { 
            projectSelect.innerHTML = '<option value="" disabled selected>Selecione um projeto</option>';
            projectList.forEach(project => {
                const option = document.createElement('option');
                option.value = project;
                option.textContent = project;
                projectSelect.appendChild(option);
            });
        }

        // Popula os filtros de projeto (consolidado e sidebar)
        function populateFilterProjectSelects() {
            // Filtro da Sidebar
            filterProjeto.innerHTML = '<option value="">Todos os projetos</option>';
            projectList.forEach(project => {
                const option = document.createElement('option');
                option.value = project;
                option.textContent = project;
                filterProjeto.appendChild(option);
            });

            // Filtro do Gráfico Consolidado
            chartFilterProject.innerHTML = '<option value="" disabled selected>Selecione um projeto</option>';
            projectList.forEach(project => {
                const option = document.createElement('option');
                option.value = project;
                option.textContent = project;
                chartFilterProject.appendChild(option.cloneNode(true)); // Clona para o outro select
            });
        }

        function showModal(modalElement) { 
            modalElement.classList.remove('hidden');
        }
        function hideModal(modalElement) { 
            modalElement.classList.add('hidden');
        }
        function showToast(message, isError = false) { 
            toast.textContent = message;
            toast.classList.remove('bg-green-600', 'bg-red-600', 'translate-x-full', 'opacity-0');
            toast.classList.add(isError ? 'bg-red-600' : 'bg-green-600', 'translate-x-0', 'opacity-100');
            setTimeout(() => {
                toast.classList.remove('translate-x-0', 'opacity-100');
                toast.classList.add('translate-x-full', 'opacity-0');
            }, 3000);
        }
        function updateStatusColor(selectElement) { 
            statusList.forEach(s => selectElement.classList.remove(s.class));
            selectElement.classList.remove('status-default');
            const selectedStatus = statusList.find(s => s.value === selectElement.value);
            if (selectedStatus) {
                selectElement.classList.add(selectedStatus.class);
            } else {
                selectElement.classList.add('status-default');
            }
        }
        function toggleSidebar() { 
            sidebar.classList.toggle('-translate-x-full');
            if (sidebar.classList.contains('-translate-x-full')) {
                mainContent.classList.remove('md:ml-72');
            } else {
                mainContent.classList.add('md:ml-72');
            }
        }
        
        // --- FUNÇÕES DE FILTRO (localStorage) ---
        
        function saveFilters() {
            const filters = {
                analista: filterAnalista.value,
                ge: filterGe.value,
                associado: filterAssociado.value,
                uf: filterUf.value, 
                anjo: filterAnjo.value, 
                bandeira: filterBandeira.value, 
                projeto: filterProjeto.value, // NOVO
                cnpj: filterCnpj.value,
                loja: filterLoja.value,
                search: searchInput.value
            };
            localStorage.setItem('projectDashboardFilters', JSON.stringify(filters));
            console.log("Filtros salvos:", filters);
        }
        
        function loadSavedFilters() {
            const savedFilters = JSON.parse(localStorage.getItem('projectDashboardFilters'));
            if (savedFilters) {
                console.log("Carregando filtros salvos:", savedFilters);
                filterAnalista.value = savedFilters.analista || '';
                filterGe.value = savedFilters.ge || '';
                filterAssociado.value = savedFilters.associado || '';
                filterUf.value = savedFilters.uf || ''; 
                filterAnjo.value = savedFilters.anjo || ''; 
                filterBandeira.value = savedFilters.bandeira || '';
                filterProjeto.value = savedFilters.projeto || ''; // NOVO
                filterCnpj.value = savedFilters.cnpj || '';
                filterLoja.value = savedFilters.loja || '';
                searchInput.value = savedFilters.search || '';
            }
        }
        
        function clearSavedFilters() {
            localStorage.removeItem('projectDashboardFilters');
            console.log("Filtros limpos.");
            
            filterAnalista.value = '';
            filterGe.value = '';
            filterAssociado.value = '';
            filterUf.value = ''; 
            filterAnjo.value = ''; 
            filterBandeira.value = '';
            filterProjeto.value = ''; // NOVO
            filterCnpj.value = '';
            filterLoja.value = '';
            searchInput.value = '';
            
            applyAllFilters();
        }
        
        function applyAllFilters() {
            const search = searchInput.value.toLowerCase();
            const analista = filterAnalista.value;
            const ge = filterGe.value;
            const associado = filterAssociado.value;
            const uf = filterUf.value; 
            const anjo = filterAnjo.value; 
            const bandeira = filterBandeira.value;
            const projeto = filterProjeto.value; // NOVO
            const cnpj = filterCnpj.value.toLowerCase();
            const loja = filterLoja.value.toLowerCase();

            const filteredData = allFlattenedData.filter(row => {
                const searchMatch = search === '' || 
                                    row.nomeLoja.toLowerCase().includes(search) || 
                                    row.id.toLowerCase().includes(search);
                const analistaMatch = analista === '' || row.analista === analista;
                const geMatch = ge === '' || row.ge === ge;
                const associadoMatch = associado === '' || row.associado === associado;
                const ufMatch = uf === '' || row.uf === uf; 
                const anjoMatch = anjo === '' || row.anjo === anjo; 
                const bandeiraMatch = bandeira === '' || row.bandeira === bandeira;
                const projetoMatch = projeto === '' || row.projectName === projeto; // NOVO
                const cnpjMatch = cnpj === '' || row.id.toLowerCase().includes(cnpj);
                const lojaMatch = loja === '' || row.nomeLoja.toLowerCase().includes(loja);

                return searchMatch && analistaMatch && geMatch && associadoMatch && ufMatch && anjoMatch && bandeiraMatch && projetoMatch && cnpjMatch && lojaMatch;
            });

            renderTable(filteredData);
        }
        
        // --- FUNÇÕES DE RENDERIZAÇÃO ---

        /**
         * Renderiza as linhas da tabela com base nos dados fornecidos
         */
        function renderTable(dataToRender) {
            projectTableBody.innerHTML = '';
            
            if (dataToRender.length === 0) {
                 projectTableBody.innerHTML = `
                        <tr>
                             <td colspan="29" class="text-center p-8 text-gray-500">
                                 Nenhum projeto encontrado para os filtros selecionados.
                             </td>
                        </tr>`;
                return;
            }
            
            dataToRender.forEach(row => {
                const tr = document.createElement('tr');
                tr.className = 'bg-white border-b hover:bg-gray-50';

                const statusOptions = statusList.map(s => 
                    `<option value="${s.value}" ${s.value === row.status ? 'selected' : ''}>${s.text}</option>`
                ).join('');
                const statusSelectClass = statusList.find(s => s.value === row.status)?.class || 'status-default';

                // Input de indicadores com o novo período
                const indicatorInputs = indicatorMonths.map(month => `
                    <td class="px-4 py-2 text-center">
                        <input type="number" 
                               class="indicator-input-table"
                               value="${row[month.id] || ''}"
                               placeholder="-"
                               data-store-id="${row.id}"
                               data-project-name="${row.projectName}"
                               data-month="${month.id}">
                    </td>
                `).join('');

                const obsBtnClass = (row.observation && row.observation.trim() !== '') 
                                    ? 'text-blue-500 hover:text-blue-700 obs-filled' 
                                    : 'text-gray-400 hover:text-gray-600';

                tr.innerHTML = `
                    <td class="px-4 py-3 font-medium text-gray-900 whitespace-nowrap">${row.nomeLoja}</td>
                    <td class="px-4 py-3 text-gray-600">${row.id}</td>
                    <td class="px-4 py-3 text-gray-600">${row.analista}</td>
                    <td class="px-4 py-3 text-gray-600">${row.ge}</td>
                    <td class="px-4 py-3 text-gray-600">${row.associado}</td>
                    <td class="px-4 py-3 text-gray-600">${row.bandeira}</td>
                    
                    <td class="px-4 py-3 font-medium text-gray-800">${row.projectName}</td>
                    <td class="px-4 py-3 text-center">
                        <select class="status-select ${statusSelectClass}" 
                                data-store-id="${row.id}" 
                                data-project-name="${row.projectName}">
                            ${statusOptions}
                        </select>
                    </td>
                    <td class="px-4 py-2 text-center">
                        <input type="number" 
                               class="indicator-input-table meta-input"
                               value="${row.goal || ''}"
                               placeholder="-"
                               data-store-id="${row.id}"
                               data-project-name="${row.projectName}"
                               data-field="goal">
                    </td>
                    ${indicatorInputs}
                    <td class="px-4 py-3 text-center">
                        <button class="view-obs-btn ${obsBtnClass}" 
                                title="Ver/Editar Observação"
                                data-store-id="${row.id}" 
                                data-project-name="${row.projectName}">
                            <i class="fa-solid fa-note-sticky fa-lg"></i>
                        </button>
                    </td>
                    <td class="px-4 py-3 text-center">
                        <!-- AJUSTE: Container flex com gap para os ícones não sobreporem -->
                        <div class="flex items-center justify-center gap-x-3 min-w-[120px]">
                            <button class="edit-store-btn text-blue-500 hover:text-blue-700"
                                    title="Editar Dados da Loja"
                                    data-store-id="${row.id}">
                                <i class="fa-solid fa-pencil fa-lg"></i>
                            </button>
                            <button class="view-chart-btn text-[#FF5510] hover:text-orange-700" 
                                    title="Ver Gráfico"
                                    data-store-id="${row.id}" 
                                    data-project-name="${row.projectName}">
                                <i class="fa-solid fa-chart-line fa-lg"></i>
                            </button>
                            <button class="delete-project-btn text-red-500 hover:text-red-700" 
                                    title="Excluir Projeto"
                                    data-store-id="${row.id}" 
                                    data-project-name="${row.projectName}">
                                <i class="fa-solid fa-trash fa-lg"></i>
                            </button>
                        </div>
                    </td>
                `; 
                
                projectTableBody.appendChild(tr); 
            });
            
            // Adiciona os listeners DEPOIS que o loop terminou
            projectTableBody.querySelectorAll('.status-select').forEach(select => {
                select.addEventListener('change', handleStatusChange);
            });
            projectTableBody.querySelectorAll('.indicator-input-table').forEach(input => {
                input.addEventListener('blur', handleIndicatorChange);
            });
            projectTableBody.querySelectorAll('.meta-input').forEach(input => {
                input.addEventListener('blur', handleProjectFieldChange);
            });
            projectTableBody.querySelectorAll('.view-chart-btn').forEach(button => {
                button.addEventListener('click', handleViewChart);
            });
            projectTableBody.querySelectorAll('.delete-project-btn').forEach(button => {
                button.addEventListener('click', handleDeleteProject);
            });
            projectTableBody.querySelectorAll('.edit-store-btn').forEach(button => {
                button.addEventListener('click', handleEditStoreClick);
            });
            projectTableBody.querySelectorAll('.view-obs-btn').forEach(button => {
                button.addEventListener('click', handleViewObservation);
            });
        }
        
        function updateKpiCards(kpi) { 
            kpiTotalLojas.textContent = kpi.totalLojas; 
            kpiProjetosPendentes.textContent = kpi.totalPendentes;
            kpiProjetosPendentesAssocia.textContent = kpi.totalPendentesAssocia; 
            kpiProjetosFinalizados.textContent = kpi.totalFinalizados;
            kpiEmAndamento.textContent = kpi.totalEmAndamento;
        }
        
        /**
         * Recalcula os KPIs com base no cache de dados 'allFlattenedData'
         */
        function recalculateKPIs() {
            const kpi = { 
                totalLojas: 0, 
                totalEmAndamento: 0, 
                totalFinalizados: 0, 
                totalPendentes: 0,
                totalPendentesAssocia: 0 
            };
            const uniqueLojas = new Set(); 
            
            allFlattenedData.forEach(row => {
                uniqueLojas.add(row.id); // Conta lojas únicas
                
                if(row.status === 'finalizado') kpi.totalFinalizados++;
                if(row.status === 'pendente') kpi.totalPendentes++; // Separado
                if(row.status === 'pendente-associa') kpi.totalPendentesAssocia++; // Separado
                if(row.status === 'andamento') kpi.totalEmAndamento++; // Adicionado
            });

            kpi.totalLojas = uniqueLojas.size; // Define o total de lojas
            updateKpiCards(kpi); // Chama a função de atualização do DOM
        }

        function populateFilterSelects(options) { 
            const createOptions = (selectElement, optionsSet) => {
                const currentValue = selectElement.value;
                selectElement.innerHTML = '<option value="">Todos</option>';
                [...optionsSet].sort().forEach(opt => {
                    if (opt) { // Ignora valores vazios
                        const option = document.createElement('option');
                        option.value = opt;
                        option.textContent = opt;
                        selectElement.appendChild(option);
                    }
                });
                selectElement.value = currentValue; // Mantém o filtro selecionado
            };
            
            createOptions(filterAnalista, options.analistas);
            createOptions(filterGe, options.ges);
            createOptions(filterAssociado, options.associados);
            createOptions(filterUf, options.ufs); 
            createOptions(filterAnjo, options.anjos); 
            createOptions(filterBandeira, options.bandeiras); 

            // Popula também o filtro GE do gráfico consolidado
            createOptions(chartFilterGe, options.ges);
        }
        
        function renderChart(rowData, format = 'number') { 
            if (!rowData) return;
            
            // Puxa os dados com base no novo período
            const data = indicatorMonths.map(month => Number(rowData[month.id]) || 0);
            const goal = Number(rowData.goal) || 0;
            
            chartModalTitle.textContent = `Evolução: ${rowData.nomeLoja} - ${rowData.projectName}`;
            
            if (currentChartInstance) {
                currentChartInstance.destroy();
            }

            currentChartInstance = new Chart(chartCanvas, {
                type: 'line',
                data: {
                    labels: chartLabels, // Labels atualizados (17 meses)
                    datasets: [{
                        label: 'Indicador',
                        data: data, // Dados atualizados (17 meses)
                        borderColor: '#FF5510',
                        backgroundColor: 'rgba(255, 85, 16, 0.1)',
                        fill: true,
                        tension: 0.1
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                callback: function(value) {
                                    if (format === 'currency') {
                                        return 'R$ ' + value.toLocaleString('pt-BR');
                                    }
                                    if (format === 'percent') {
                                        return value + ' %';
                                    }
                                    return value.toLocaleString('pt-BR');
                                }
                            }
                        }
                    },
                    plugins: {
                        legend: { display: false },
                        annotation: {
                            annotations: {
                                goalLine: {
                                    type: 'line',
                                    yMin: goal,
                                    yMax: goal,
                                    borderColor: 'rgb(255, 99, 132)',
                                    borderWidth: 2,
                                    borderDash: [6, 6],
                                    label: {
                                        content: 'Meta: ' + (format === 'currency' ? 'R$ ' : '') + goal.toLocaleString('pt-BR') + (format === 'percent' ? ' %' : ''),
                                        enabled: true,
                                        position: 'start',
                                        backgroundColor: 'rgba(255, 99, 132, 0.8)'
                                    }
                                }
                            }
                        }
                    }
                }
            });
            
            // Atualiza a label da meta com a formatação correta
            if (currentChartInstance && currentChartInstance.options.plugins.annotation && currentChartInstance.options.plugins.annotation.annotations.goalLine) {
                let goalLabel = 'Meta: ';
                if (format === 'currency') goalLabel += 'R$ ' + goal.toLocaleString('pt-BR');
                else if (format === 'percent') goalLabel += goal.toLocaleString('pt-BR') + ' %';
                else goalLabel += goal.toLocaleString('pt-BR');
                currentChartInstance.options.plugins.annotation.annotations.goalLine.label.content = goalLabel;
                currentChartInstance.update();
            }
        }

        // --- NOVO: Plugin para mostrar labels no gráfico ---
        const customDatalabelsPlugin = {
            id: 'customDatalabels',
            afterDraw: (chart) => {
                const ctx = chart.ctx;
                ctx.save();
                ctx.font = '800 10px Rubik, sans-serif'; // 800 para ExtraBold
                ctx.textAlign = 'center';
                ctx.textBaseline = 'bottom';
                
                chart.data.datasets.forEach((dataset, i) => {
                    const meta = chart.getDatasetMeta(i);
                    ctx.fillStyle = dataset.borderColor || '#333'; // Usa a cor da linha

                    if (!meta.hidden) {
                        meta.data.forEach((element, index) => {
                            const value = dataset.data[index];
                            if (value) { // Só mostra se houver valor (diferente de 0 ou null)
                                const { x, y } = element.getProps(['x', 'y']);
                                // Formata o número para o padrão PT-BR
                                const formattedValue = value.toLocaleString('pt-BR'); 
                                ctx.fillText(formattedValue, x, y - 5); // 5px acima do ponto
                            }
                        });
                    }
                });
                ctx.restore();
            }
        };

        /**
         * Renderiza o gráfico consolidado com múltiplas lojas
         */
        function renderConsolidatedChart() {
            const selectedProject = chartFilterProject.value;
            const selectedGe = chartFilterGe.value;
            const filterCnpjLoja = chartFilterCnpjLoja.value.toLowerCase();

            if (!selectedProject) {
                showToast("Por favor, selecione um projeto para gerar o gráfico.", true);
                return;
            }
            
            // 1. Filtrar os dados
            const filteredData = allFlattenedData.filter(row => {
                const projectMatch = row.projectName === selectedProject;
                const geMatch = selectedGe === '' || row.ge === selectedGe;
                const cnpjLojaMatch = filterCnpjLoja === '' || 
                                      row.id.toLowerCase().includes(filterCnpjLoja) || 
                                      row.nomeLoja.toLowerCase().includes(filterCnpjLoja);
                
                return projectMatch && geMatch && cnpjLojaMatch;
            });

            if (filteredData.length === 0) {
                showToast("Nenhuma loja encontrada para este projeto e filtros.", true);
                if (consolidatedChartInstance) {
                    consolidatedChartInstance.destroy();
                    consolidatedChartInstance = null;
                }
                return;
            }

            // 2. Transformar dados em datasets
            const datasets = filteredData.map(row => {
                const data = indicatorMonths.map(month => Number(row[month.id]) || 0);
                const color = getRandomColor();
                return {
                    label: `${row.nomeLoja} (${row.id})`, // Distingue as lojas
                    data: data,
                    borderColor: color,
                    backgroundColor: 'transparent', // Linha pura
                    tension: 0.1,
                    fill: false
                };
            });

            // 3. Renderizar o gráfico
            if (consolidatedChartInstance) {
                consolidatedChartInstance.destroy();
            }

            consolidatedChartInstance = new Chart(consolidatedChartCanvas, {
                type: 'line',
                data: {
                    labels: chartLabels, // Labels globais
                    datasets: datasets
                },
                plugins: [customDatalabelsPlugin], // Adiciona o plugin
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'top', // Legenda no topo para ver as lojas
                            labels: {
                                boxWidth: 20
                            }
                        },
                        title: {
                            display: true,
                            text: `Evolução do Projeto: ${selectedProject}`
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true
                        }
                    },
                    interaction: {
                        intersect: false,
                        mode: 'index',
                    }
                }
            });
        }
        
        // --- LÓGICA DO LOCALSTORAGE ---
        
        /**
         * Busca os dados salvos no localStorage e os converte de JSON.
         * Retorna um array de lojas, ou um array vazio se não houver nada.
         */
        function getDadosSalvos() {
            try {
                const dadosSalvos = localStorage.getItem(LOCAL_STORAGE_KEY);
                return dadosSalvos ? JSON.parse(dadosSalvos) : [];
            } catch (error) {
                console.error("Erro ao ler dados do localStorage:", error);
                return [];
            }
        }

        /**
         * Salva o array de lojas no localStorage
         */
        function salvarDados(stores) {
            try {
                localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(stores));
            } catch (error) {
                console.error("Erro ao salvar dados no localStorage:", error);
                showToast("Erro ao salvar dados localmente.", true);
            }
        }

        /**
         * Carrega os dados do localStorage, processa (para KPIs, filtros)
         * e atualiza a tela.
         */
        function carregarEProcessarDados() {
            console.log("Carregando dados do localStorage...");
            const stores = getDadosSalvos();

            allFlattenedData = []; 
            const filterOptions = { 
                analistas: new Set(), ges: new Set(), associados: new Set(), 
                anjos: new Set(), ufs: new Set(), bandeiras: new Set() 
            };
            
            stores.forEach((store) => {
                filterOptions.analistas.add(store.analista);
                filterOptions.ges.add(store.ge);
                filterOptions.associados.add(store.associado);
                filterOptions.anjos.add(store.anjo); 
                filterOptions.ufs.add(store.uf); 
                filterOptions.bandeiras.add(store.bandeira); 
                
                if (store.projects) {
                    Object.keys(store.projects).forEach(projectName => {
                        const project = store.projects[projectName];
                        const row = {
                            ...store, // analista, ge, associado, bandeira, etc.
                            projectName: projectName,
                            status: project.status,
                            goal: project.goal || '',
                            observation: project.observation || '',
                            ...project.indicators 
                        };
                        delete row.projects; 
                        allFlattenedData.push(row);
                    });
                }
            });
            
            allFlattenedData.sort((a, b) => a.nomeLoja.localeCompare(b.nomeLoja));

            recalculateKPIs(); // KPIs são calculados aqui
            populateFilterSelects(filterOptions); // Popula filtros da sidebar e do gráfico
            loadSavedFilters(); // Carrega filtros salvos ANTES de aplicar
            applyAllFilters();
        }


        // --- HANDLERS DE EVENTOS (Modificados para localStorage) ---
        
        function handleViewChart(e) { 
            const { storeId, projectName } = e.currentTarget.closest('button').dataset;
            currentChartRowData = allFlattenedData.find(r => r.id === storeId && r.projectName === projectName);
            
            if (!currentChartRowData) {
                showToast("Erro: Não foi possível carregar os dados do gráfico.", true);
                return;
            }
            
            document.querySelector('input[name="chart-format"][value="number"]').checked = true;
            renderChart(currentChartRowData, 'number');
            showModal(chartModal);
        }
        
        function handleDeleteProject(e) { 
            const { storeId, projectName } = e.currentTarget.closest('button').dataset;
            const rowData = allFlattenedData.find(r => r.id === storeId && r.projectName === projectName);
            
            if (!rowData) {
                showToast("Erro: Não foi possível encontrar o projeto para excluir.", true);
                return;
            }
            
            document.getElementById('delete-modal-text').textContent = `Tem certeza que deseja excluir o projeto "${projectName}" da loja "${rowData.nomeLoja}"? Esta ação não pode ser desfeita.`;
            confirmDeleteBtn.dataset.storeId = storeId;
            confirmDeleteBtn.dataset.projectName = projectName;
            
            showModal(deleteConfirmModal);
        }
        
        function confirmProjectDelete() { 
            const { storeId, projectName } = confirmDeleteBtn.dataset;
            if (!storeId || !projectName) {
                showToast("Erro ao excluir: IDs não encontrados.", true);
                return;
            }

            confirmDeleteBtn.disabled = true;
            confirmDeleteBtn.textContent = 'Excluindo...';

            try {
                let stores = getDadosSalvos();
                const storeIndex = stores.findIndex(s => s.id === storeId);
                
                if (storeIndex > -1 && stores[storeIndex].projects && stores[storeIndex].projects[projectName]) {
                    // Remove o projeto de dentro da loja
                    delete stores[storeIndex].projects[projectName];
                    salvarDados(stores); // Salva o array modificado
                    carregarEProcessarDados(); // Recarrega e redesenha a tela
                    
                    showToast("Projeto excluído com sucesso!");
                    hideModal(deleteConfirmModal);
                } else {
                    showToast("Erro: Projeto ou loja não encontrado nos dados locais.", true);
                }
            } catch (error) {
                console.error("Erro ao excluir projeto local:", error);
                showToast(`Erro ao excluir: ${error.message}`, true);
            } finally {
                confirmDeleteBtn.disabled = false;
                confirmDeleteBtn.textContent = 'Excluir';
                delete confirmDeleteBtn.dataset.storeId;
                delete confirmDeleteBtn.dataset.projectName;
            }
        }

        function handleViewObservation(e) { 
            const { storeId, projectName } = e.currentTarget.closest('button').dataset;
            const rowData = allFlattenedData.find(r => r.id === storeId && r.projectName === projectName);
            
            if (!rowData) {
                showToast("Erro: Não foi possível carregar os dados.", true);
                return;
            }
            
            obsModalTitle.textContent = `Observações: ${rowData.nomeLoja} - ${projectName}`;
            obsModalTextarea.value = rowData.observation || '';
            
            saveObsBtn.dataset.storeId = storeId;
            saveObsBtn.dataset.projectName = projectName;
            
            showModal(obsModal);
        }
        
        function handleSaveObservation() {
            const { storeId, projectName } = saveObsBtn.dataset;
            const newObservation = obsModalTextarea.value;
            
            if (!storeId || !projectName) {
                showToast("Erro: Não foi possível salvar. IDs não encontrados.", true);
                return;
            }

            saveObsBtn.disabled = true;
            saveObsBtn.textContent = 'Salvando...';

            try {
                let stores = getDadosSalvos();
                const storeIndex = stores.findIndex(s => s.id === storeId);

                if (storeIndex > -1 && stores[storeIndex].projects && stores[storeIndex].projects[projectName]) {
                    // Atualiza a observação
                    stores[storeIndex].projects[projectName].observation = newObservation;
                    
                    salvarDados(stores);
                    carregarEProcessarDados(); // Recarrega os dados e atualiza a UI
                    
                    showToast("Observação salva com sucesso!");
                    hideModal(obsModal);
                } else {
                     showToast("Erro: Projeto ou loja não encontrado.", true);
                }

            } catch (error) {
                console.error("Erro ao salvar observação:", error);
                showToast(`Erro ao salvar: ${error.message}`, true);
            } finally {
                saveObsBtn.disabled = false;
                saveObsBtn.textContent = 'Salvar Observação';
                delete saveObsBtn.dataset.storeId;
                delete saveObsBtn.dataset.projectName;
            }
        }

        function handleFormSubmit(e) {
            e.preventDefault();
            const saveButton = document.getElementById('save-store-btn');
            saveButton.disabled = true;
            saveButton.textContent = 'Salvando...';

            const formData = new FormData(storeForm);
            const storeId = formData.get('cnpj'); // CNPJ
            
            const isEditMode = document.getElementById('cnpj').readOnly;

            const storeData = {
                analista: formData.get('analista'),
                anjo: formData.get('anjo'),
                uf: formData.get('uf'),
                bandeira: formData.get('bandeira'),
                ge: formData.get('ge'),
                associado: formData.get('associado'),
                nomeLoja: formData.get('nome-loja'),
            };
            
            // Pega todos os dados atuais
            let stores = getDadosSalvos();

            try {
                if (isEditMode) {
                    // --- LÓGICA DE ATUALIZAÇÃO DA LOJA ---
                    const storeIndex = stores.findIndex(s => s.id === storeId);
                    if (storeIndex > -1) {
                        // Atualiza os dados da loja, mantendo os projetos existentes
                        stores[storeIndex] = { ...stores[storeIndex], ...storeData };
                        
                        salvarDados(stores);
                        carregarEProcessarDados();
                        showToast("Dados da loja atualizados com sucesso!");
                        hideModal(storeModal);
                    } else {
                        showToast("Erro: Loja para editar não encontrada.", true);
                    }
                    
                } else {
                    // --- LÓGICA DE CRIAÇÃO DE PROJETO ---
                    const projectName = formData.get('nome-projeto');
                    const meta = formData.get('meta');
                    const observation = formData.get('observation');
                    
                    if (!storeId || !projectName) {
                        showToast("CNPJ e Projeto são obrigatórios!", true);
                        saveButton.disabled = false;
                        saveButton.textContent = 'Salvar';
                        return;
                    }

                    // Cria o objeto de indicadores com todos os meses
                    const indicators = {};
                    indicatorMonths.forEach(month => {
                        indicators[month.id] = '';
                    });
                    
                    const newProjectData = {
                        status: 'pendente', 
                        indicators: indicators,
                        goal: meta || '',
                        observation: observation || ''
                    };
                    
                    const storeIndex = stores.findIndex(s => s.id === storeId);

                    if (storeIndex > -1) {
                        // Loja existe, atualiza os dados dela e adiciona o novo projeto
                        stores[storeIndex] = { ...stores[storeIndex], ...storeData };
                        // Adiciona o novo projeto
                        if (!stores[storeIndex].projects) stores[storeIndex].projects = {};
                        stores[storeIndex].projects[projectName] = newProjectData;
                        
                        showToast("Projeto adicionado à loja existente!");
                    } else {
                        // Loja não existe, cria uma nova
                        const novaLoja = {
                            ...storeData,
                            id: storeId, // Adiciona o ID (CNPJ)
                            projects: { [projectName]: newProjectData }
                        };
                        stores.push(novaLoja);
                        showToast("Nova loja cadastrada com sucesso!");
                    }

                    salvarDados(stores);
                    carregarEProcessarDados();
                    hideModal(storeModal);
                    storeForm.reset();
                }
            } catch (error) {
                console.error("Erro ao salvar no localStorage:", error);
                showToast(`Erro ao salvar: ${error.message}`, true);
            } finally {
                saveButton.disabled = false;
                saveButton.textContent = 'Salvar';
            }
        }
        
        function handleStatusChange(e) { 
            const selectElement = e.target;
            const newStatus = selectElement.value;
            const { storeId, projectName } = selectElement.dataset;
            if (!storeId || !projectName) return;
            
            updateStatusColor(selectElement);
            
            try {
                let stores = getDadosSalvos();
                const storeIndex = stores.findIndex(s => s.id === storeId);
                if (storeIndex > -1 && stores[storeIndex].projects[projectName]) {
                    stores[storeIndex].projects[projectName].status = newStatus;
                    salvarDados(stores);
                    // Não precisa recarregar tudo, apenas atualizamos o cache local
                    const rowData = allFlattenedData.find(r => r.id === storeId && r.projectName === projectName);
                    if (rowData) rowData.status = newStatus;
                    
                    // Recalcula os KPIs
                    recalculateKPIs();
                }
            } catch (error) {
                console.error("Erro ao atualizar status:", error);
                showToast("Erro ao salvar novo status.", true);
            }
        }
        
        function handleIndicatorChange(e) { 
            const inputElement = e.target;
            const newValue = inputElement.value;
            const { storeId, projectName, month } = inputElement.dataset;
            if (!storeId || !projectName || !month) return;
            
            try {
                let stores = getDadosSalvos();
                const storeIndex = stores.findIndex(s => s.id === storeId);
                if (storeIndex > -1 && stores[storeIndex].projects[projectName]) {
                    // Cria 'indicators' se não existir
                    if (!stores[storeIndex].projects[projectName].indicators) {
                        stores[storeIndex].projects[projectName].indicators = {};
                    }
                    stores[storeIndex].projects[projectName].indicators[month] = newValue;
                    salvarDados(stores);
                    
                    // Atualiza cache local
                    const rowData = allFlattenedData.find(r => r.id === storeId && r.projectName === projectName);
                    if (rowData) rowData[month] = newValue;

                    inputElement.classList.add('bg-green-100', 'border-green-500');
                    setTimeout(() => inputElement.classList.remove('bg-green-100', 'border-green-500'), 1500);
                }
            } catch (error) {
                console.error("Erro ao atualizar indicador:", error);
                showToast("Erro ao salvar indicador.", true);
                 inputElement.classList.add('bg-red-100', 'border-red-500');
                 setTimeout(() => inputElement.classList.remove('bg-red-100', 'border-red-500'), 1500);
            }
        }
        
        function handleProjectFieldChange(e) { 
            const inputElement = e.target;
            const newValue = inputElement.value;
            const { storeId, projectName, field } = inputElement.dataset; 
            if (!storeId || !projectName || !field) return;
            
            try {
                let stores = getDadosSalvos();
                const storeIndex = stores.findIndex(s => s.id === storeId);
                if (storeIndex > -1 && stores[storeIndex].projects[projectName]) {
                    
                    stores[storeIndex].projects[projectName][field] = newValue;
                    salvarDados(stores);
                    
                    // Atualiza cache local
                    const rowData = allFlattenedData.find(r => r.id === storeId && r.projectName === projectName);
                    if (rowData) rowData[field] = newValue;

                    inputElement.classList.add('bg-green-100', 'border-green-500');
                    setTimeout(() => inputElement.classList.remove('bg-green-100', 'border-green-500'), 1500);
                }
            } catch (error)
            {
                console.error(`Erro ao atualizar campo ${field}:`, error);
                showToast(`Erro ao salvar ${field}.`, true);
                 inputElement.classList.add('bg-red-100', 'border-red-500');
                 setTimeout(() => inputElement.classList.remove('bg-red-100', 'border-red-500'), 1500);
            }
        }

        function resetModalToCreateMode() {
            storeForm.reset();
            document.getElementById('cnpj').readOnly = false;
            
            document.getElementById('store-modal-title').textContent = "Adicionar Nova Loja ao Projeto";
            document.getElementById('save-store-btn').textContent = "Salvar";
            
            document.getElementById('form-project-group').style.display = 'block';
            document.getElementById('form-meta-group').style.display = 'block';
            document.getElementById('form-observation-group').style.display = 'block';
            
            document.getElementById('nome-projeto').required = true;
        }

        function handleEditStoreClick(e) {
            const storeId = e.currentTarget.closest('button').dataset.storeId;
            const rowData = allFlattenedData.find(r => r.id === storeId);
            
            if (!rowData) {
                showToast("Erro: Não foi possível carregar os dados da loja.", true);
                return;
            }
            
            // Preenche o formulário
            document.getElementById('analista').value = rowData.analista || '';
            document.getElementById('anjo').value = rowData.anjo || '';
            document.getElementById('uf').value = rowData.uf || '';
            document.getElementById('bandeira').value = rowData.bandeira || '';
            document.getElementById('cnpj').value = rowData.id;
            document.getElementById('ge').value = rowData.ge || '';
            document.getElementById('associado').value = rowData.associado || '';
            document.getElementById('nome-loja').value = rowData.nomeLoja || '';
            
            // Configura o modal para "Modo Edição"
            document.getElementById('cnpj').readOnly = true;
            document.getElementById('store-modal-title').textContent = "Editar Dados da Loja";
            document.getElementById('save-store-btn').textContent = "Atualizar Loja";
            
            document.getElementById('form-project-group').style.display = 'none';
            document.getElementById('form-meta-group').style.display = 'none';
            document.getElementById('form-observation-group').style.display = 'none';
            
            document.getElementById('nome-projeto').required = false;

            showModal(storeModal);
        }


        // --- INICIALIZAÇÃO ---
        
        // Modais
        openModalBtn.addEventListener('click', () => {
            resetModalToCreateMode(); 
            showModal(storeModal);
        });
        closeModalBtn.addEventListener('click', () => hideModal(storeModal));
        cancelModalBtn.addEventListener('click', () => hideModal(storeModal));
        
        closeChartModalBtn.addEventListener('click', () => hideModal(chartModal));
        
        // Modal de Exclusão
        cancelDeleteBtn.addEventListener('click', () => hideModal(deleteConfirmModal));
        confirmDeleteBtn.addEventListener('click', confirmProjectDelete);
        
        // Modal de Observação
        closeObsModalBtn.addEventListener('click', () => hideModal(obsModal));
        cancelObsBtn.addEventListener('click', () => hideModal(obsModal));
        saveObsBtn.addEventListener('click', handleSaveObservation);
        
        // Modal do Gráfico (Rádio buttons)
        document.querySelectorAll('input[name="chart-format"]').forEach(radio => {
            radio.addEventListener('change', (e) => {
                if (currentChartRowData) {
                    renderChart(currentChartRowData, e.target.value);
                }
            });
        });
        
        // Sidebar
        toggleFilterBtn.addEventListener('click', toggleSidebar);
        
        // Filtros e Pesquisa
        applyFiltersBtn.addEventListener('click', () => {
            saveFilters();
            applyAllFilters();
        });
        clearFiltersBtn.addEventListener('click', clearSavedFilters);
        searchInput.addEventListener('input', () => {
            saveFilters(); // Salva em tempo real
            applyAllFilters();
        });

        // Gráfico Consolidado
        generateChartBtn.addEventListener('click', renderConsolidatedChart);
        
        // Formulário
        storeForm.addEventListener('submit', handleFormSubmit);

        // Preenche o dropdown de projetos no modal
        populateProjectSelect();
        populateFilterProjectSelects(); // NOVO
        
        // Inicia a lógica de autenticação e carregamento de dados
        carregarEProcessarDados(); 

    </script>
</body>
</html>
