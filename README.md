<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MVP - Enriqueta RFID (Versão Câmera)</title>
    <!-- Biblioteca HTML5-QRCode para compatibilidade universal com câmeras de telemóveis -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
    <style>
        :root {
            --primary: #4f46e5;
            --primary-hover: #4338ca;
            --bg: #f9fafb;
            --card-bg: #ffffff;
            --text: #1f2937;
            --border: #e5e7eb;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 16px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            margin-bottom: 24px;
        }

        h1 {
            font-size: 1.5rem;
            color: var(--primary);
            margin-bottom: 4px;
        }

        .card {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 16px;
            margin-bottom: 16px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }

        button {
            background-color: var(--primary);
            color: white;
            border: none;
            border-radius: 6px;
            padding: 10px 16px;
            font-size: 1rem;
            cursor: pointer;
            width: 100%;
            margin-top: 8px;
            font-weight: 600;
        }

        button:hover {
            background-color: var(--primary-hover);
        }

        button.secondary {
            background-color: #6b7280;
        }

        button.secondary:hover {
            background-color: #4b5563;
        }

        input, select {
            width: 100%;
            padding: 10px;
            margin-top: 6px;
            margin-bottom: 12px;
            border: 1px solid var(--border);
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 1rem;
        }

        label {
            font-size: 0.875rem;
            font-weight: 500;
            color: #4b5563;
        }

        .row {
            display: flex;
            gap: 12px;
        }

        .col {
            flex: 1;
        }

        #reader {
            width: 100%;
            border-radius: 6px;
            overflow: hidden;
            margin-bottom: 12px;
            display: none;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.875rem;
            margin-top: 12px;
        }

        th, td {
            border: 1px solid var(--border);
            padding: 8px;
            text-align: left;
        }

        th {
            background-color: #f3f4f6;
        }

        .table-responsive {
            overflow-x: auto;
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>Enriqueta RFID</h1>
        <p>MVP - Controle de Estoque via Câmera Universal</p>
    </header>

    <!-- Seção da Câmera -->
    <div class="card">
        <h3>📷 Leitura por Câmera</h3>
        <button id="btn-start-camera" onclick="iniciarCamera()">Iniciar Câmera</button>
        <div id="reader"></div>
        <button id="btn-stop-camera" class="secondary" onclick="pararCamera()" style="display:none;">Parar Câmera</button>
    </div>

    <!-- Seção de Cadastro -->
    <div class="card">
        <h3>📦 Cadastro de Produto</h3>
        <form id="product-form" onsubmit="salvarProduto(event)">
            <label for="codigo">Código do Produto</label>
            <input type="text" id="codigo" required>

            <label for="descricao">Descrição (Produto)</label>
            <input type="text" id="descricao" required>

            <div class="row">
                <div class="col">
                    <label for="categoria">Categoria</label>
                    <input type="text" id="categoria">
                </div>
                <div class="col">
                    <label for="marca">Marca</label>
                    <input type="text" id="marca">
                </div>
            </div>

            <div class="row">
                <div class="col">
                    <label for="quantidade">Quantidade</label>
                    <input type="number" id="quantidade" value="1" required>
                </div>
                <div class="col">
                    <label for="corredor">Corredor</label>
                    <input type="text" id="corredor">
                </div>
            </div>

            <div class="row">
                <div class="col">
                    <label for="prateleira">Prateleira</label>
                    <input type="text" id="prateleira">
                </div>
                <div class="col">
                    <label for="posicao">Posição</label>
                    <input type="text" id="posicao">
                </div>
            </div>

            <div class="row">
                <div class="col">
                    <label for="compra">Valor de Compra (€)</label>
                    <input type="number" step="0.01" id="compra">
                </div>
                <div class="col">
                    <label for="venda">Valor de Venda (€)</label>
                    <input type="number" step="0.01" id="venda">
                </div>
            </div>

            <button type="submit">Salvar Produto</button>
        </form>
    </div>

    <!-- Seção de Estoque e Pesquisa -->
    <div class="card">
        <h3>📈 Controle de Estoque</h3>
        <input type="text" id="pesquisa" placeholder="Pesquisar por código, produto ou marca..." onkeyup="pesquisarProdutos()">
        
        <button class="secondary" onclick="exportarCSV()">📊 Exportar para CSV</button>

        <div class="table-responsive">
            <table id="tabela-produtos">
                <thead>
                    <tr>
                        <th>Código</th>
                        <th>Produto</th>
                        <th>Categoria</th>
                        <th>Marca</th>
                        <th>Qtd</th>
                        <th>Corredor</th>
                        <th>Prat.</th>
                        <th>Pos.</th>
                        <th>Compra</th>
                        <th>Venda</th>
                        <th>Data</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Preenchido via JavaScript -->
                </tbody>
            </table>
        </div>
    </div>
</div>

<script>
    // --- Configuração do IndexedDB ---
    let db;
    const dbName = "EnriquetaDB";
    const storeName = "produtos";

    function abrirBanco() {
        const request = indexedDB.open(dbName, 1);
        request.onerror = (event) => console.error("Erro ao abrir IndexedDB", event);
        request.onsuccess = (event) => {
            db = event.target.result;
            carregarProdutos();
        };
        request.onupgradeneeded = (event) => {
            let db = event.target.result;
            if (!db.objectStoreNames.contains(storeName)) {
                db.createObjectStore(storeName, { keyPath: "codigo" });
            }
        };
    }

    abrirBanco();

    // --- Funcionalidade da Câmera (Html5Qrcode Universal) ---
    let html5QrCode = null;

    function iniciarCamera() {
        const readerDiv = document.getElementById("reader");
        readerDiv.style.display = "block";
        document.getElementById("btn-start-camera").style.display = "none";
        document.getElementById("btn-stop-camera").style.display = "block";

        html5QrCode = new Html5Qrcode("reader");
        
        // Configuração universal para usar a câmara traseira em qualquer telemóvel
        const configKeys = { fps: 10, qrbox: { width: 250, height: 150 } };

        html5QrCode.start(
            { facingMode: "environment" }, 
            configKeys,
            (decodedText, decodedResult) => {
                document.getElementById("codigo").value = decodedText;
                pararCamera();
                alert("Código lido com sucesso: " + decodedText);
            },
            (errorMessage) => {
                // Erros de leitura contínua ignorados para não poluir a consola
            }
        ).catch((err) => {
            console.error("Erro ao iniciar a câmara: ", err);
            // Fallback automático para qualquer câmara disponível caso a traseira falhe
            html5QrCode.start(
                { facingMode: "user" }, 
                configKeys,
                (decodedText, decodedResult) => {
                    document.getElementById("codigo").value = decodedText;
                    pararCamera();
                    alert("Código lido com sucesso: " + decodedText);
                },
                (err2) => {}
            ).catch(err3 => {
                alert("Não foi possível aceder a nenhuma câmara do dispositivo. Verifique as permissões.");
                pararCamera();
            });
        });
    }

    function pararCamera() {
        if (html5QrCode && html5QrCode.isScanning) {
            html5QrCode.stop().then(() => {
                html5QrCode.clear();
            }).catch(err => {
                console.error("Erro ao parar a câmara", err);
            });
        }
        document.getElementById("reader").style.display = "none";
        document.getElementById("btn-start-camera").style.display = "block";
        document.getElementById("btn-stop-camera").style.display = "none";
    }

    // --- Salvar Produto ---
    function salvarProduto(event) {
        event.preventDefault();

        const dataAtual = new Date().toLocaleDateString('pt-PT');

        const produto = {
            codigo: document.getElementById("codigo").value,
            descricao: document.getElementById("descricao").value,
            categoria: document.getElementById("categoria").value,
            marca: document.getElementById("marca").value,
            quantidade: document.getElementById("quantidade").value,
            corredor: document.getElementById("corredor").value,
            prateleira: document.getElementById("prateleira").value,
            posicao: document.getElementById("posicao").value,
            compra: document.getElementById("compra").value,
            venda: document.getElementById("venda").value,
            data: dataAtual
        };

        const transaction = db.transaction([storeName], "readwrite");
        const store = transaction.objectStore(storeName);
        const request = store.put(produto);

        request.onsuccess = () => {
            alert("Produto cadastrado com sucesso!");
            document.getElementById("product-form").reset();
            carregarProdutos();
        };

        request.onerror = (event) => {
            alert("Erro ao salvar produto: " + event.target.error);
        };
    }

    // --- Carregar e Listar Produtos ---
    function carregarProdutos(filtro = "") {
        if (!db) return;
        const transaction = db.transaction([storeName], "readonly");
        const store = transaction.objectStore(storeName);
        const request = store.getAll();

        request.onsuccess = (event) => {
            const produtos = event.target.result;
            const tbody = document.querySelector("#tabela-produtos tbody");
            tbody.innerHTML = "";

            const produtosFiltrados = produtos.filter(p => 
                p.codigo.toLowerCase().includes(filtro.toLowerCase()) ||
                p.descricao.toLowerCase().includes(filtro.toLowerCase()) ||
                p.marca.toLowerCase().includes(filtro.toLowerCase())
            );

            produtosFiltrados.forEach(p => {
                const tr = document.createElement("tr");
                tr.innerHTML = `
                    <td>${p.codigo}</td>
                    <td>${p.descricao}</td>
                    <td>${p.categoria}</td>
                    <td>${p.marca}</td>
                    <td>${p.quantidade}</td>
                    <td>${p.corredor}</td>
                    <td>${p.prateleira}</td>
                    <td>${p.posicao}</td>
                    <td>${p.compra}</td>
                    <td>${p.venda}</td>
                    <td>${p.data}</td>
                `;
                tbody.appendChild(tr);
            });
        };
    }

    // --- Pesquisa ---
    function pesquisarProdutos() {
        const termo = document.getElementById("pesquisa").value;
        carregarProdutos(termo);
    }

    // --- Exportar para CSV ---
    function exportarCSV() {
        const transaction = db.transaction([storeName], "readonly");
        const store = transaction.objectStore(storeName);
        const request = store.getAll();

        request.onsuccess = (event) => {
            const produtos = event.target.result;
            if (produtos.length === 0) {
                alert("Nenhum produto cadastrado para exportar.");
                return;
            }

            let csvContent = "Código,Produto,Categoria,Marca,Quantidade,Corredor,Prateleira,Posição,Compra,Venda,Data\n";

            produtos.forEach(p => {
                let linha = [
                    `"${p.codigo}"`,
                    `"${p.descricao}"`,
                    `"${p.categoria}"`,
                    `"${p.marca}"`,
                    p.quantidade,
                    `"${p.corredor}"`,
                    `"${p.prateleira}"`,
                    `"${p.posicao}"`,
                    p.compra,
                    p.venda,
                    `"${p.data}"`
                ].join(",");
                csvContent += linha + "\n";
            });

            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.setAttribute("href", url);
            a.setAttribute("download", "estoque_enriqueta.csv");
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
        };
    }
</script>

</body>
</html>
