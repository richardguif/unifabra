<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Calculadora de Rebates para Compras Parceladas</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1, h2 {
            color: #2c3e50;
            text-align: center;
        }
        .dashboard {
            display: flex;
            justify-content: space-between;
            margin-bottom: 20px;
            flex-wrap: wrap;
            gap: 10px;
        }
        .card {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 15px;
            flex: 1;
            min-width: 200px;
            text-align: center;
            border-left: 5px solid #3498db;
        }
        .card h3 {
            margin-top: 0;
            color: #7f8c8d;
            font-size: 16px;
        }
        .card p {
            font-size: 24px;
            font-weight: bold;
            margin: 10px 0;
            color: #2c3e50;
        }
        .tabs {
            margin-bottom: 20px;
        }
        .tab-button {
            background-color: #f8f9fa;
            border: none;
            outline: none;
            cursor: pointer;
            padding: 10px 20px;
            font-size: 16px;
            border-radius: 5px 5px 0 0;
            margin-right: 5px;
        }
        .tab-button.active {
            background-color: #3498db;
            color: white;
        }
        .tab-content {
            display: none;
            padding: 20px;
            border: 1px solid #ddd;
            border-radius: 0 5px 5px 5px;
            background-color: white;
        }
        .tab-content.active {
            display: block;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            padding: 10px;
            border: 1px solid #ddd;
            text-align: right;
        }
        th {
            background-color: #f2f2f2;
            text-align: center;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        tr:hover {
            background-color: #f1f1f1;
        }
        tr.total {
            font-weight: bold;
            background-color: #e8f4fc;
        }
        .chart-container {
            margin-top: 30px;
            height: 300px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Calculadora de Rebates (5%)</h1>
        <h2>Compras Parceladas em 30/60/90 dias</h2>

        <div class="dashboard">
            <div class="card">
                <h3>Total de Compras</h3>
                <p id="total-compras">R$ 0,00</p>
            </div>
            <div class="card">
                <h3>Total de Rebates</h3>
                <p id="total-rebates">R$ 0,00</p>
            </div>
            <div class="card">
                <h3>Média Mensal de Rebates</h3>
                <p id="media-rebates">R$ 0,00</p>
            </div>
        </div>

        <div class="tabs">
            <button class="tab-button active" onclick="openTab(event, 'tab-resumo')">Resumo de Rebates</button>
            <button class="tab-button" onclick="openTab(event, 'tab-detalhado')">Detalhamento por Cliente</button>
            <button class="tab-button" onclick="openTab(event, 'tab-parcelas')">Parcelas Mensais</button>
        </div>

        <div id="tab-resumo" class="tab-content active">
            <table id="tabela-resumo">
                <thead>
                    <tr>
                        <th>Mês</th>
                        <th>Compras</th>
                        <th>Rebate Total</th>
                        <th>1ª Parcela</th>
                        <th>2ª Parcela</th>
                        <th>3ª Parcela</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Será preenchido pelo JavaScript -->
                </tbody>
            </table>
            <div class="chart-container">
                <canvas id="chartRebates"></canvas>
            </div>
        </div>

        <div id="tab-detalhado" class="tab-content">
            <table id="tabela-detalhada">
                <thead>
                    <tr>
                        <th>Cliente</th>
                        <th>Out/24</th>
                        <th>Nov/24</th>
                        <th>Dez/24</th>
                        <th>Jan/25</th>
                        <th>Fev/25</th>
                        <th>Mar/25</th>
                        <th>Abr/25</th>
                        <th>Mai/25</th>
                        <th>Jun/25</th>
                        <th>Total</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Será preenchido pelo JavaScript -->
                </tbody>
            </table>
        </div>

        <div id="tab-parcelas" class="tab-content">
            <table id="tabela-parcelas">
                <thead>
                    <tr>
                        <th>Mês de Compra</th>
                        <th>Valor Total</th>
                        <th>1ª Parcela (Mês+1)</th>
                        <th>2ª Parcela (Mês+2)</th>
                        <th>3ª Parcela (Mês+3)</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- Será preenchido pelo JavaScript -->
                </tbody>
            </table>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.0/papaparse.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
    <script>
        // Dados da planilha CSV
        const csvData = `R¢tulos de Linha;out/24;nov/24;dez/24;jan/25;fev/25;mar/25
DROGALIRA; R$ 5.876,59 ; R$ 4.304,38 ; R$ 6.040,25 ; R$ 6.530,60 ; R$ 5.545,58 ; R$ 6.624,39 
DROGARIA COUTINHO; R$ 28.994,32 ; R$ 26.831,23 ; R$ 16.930,48 ; R$ 10.575,84 ; R$ 6.725,09 ; R$ 15.741,39 
DROGARIA DEDICAR;;;;; R$ 38,07 ; R$ 454,92 
DROGARIA E FARMACIA MONICA; R$ 8.896,10 ; R$ 9.726,51 ; R$ 10.552,27 ; R$ 7.897,98 ; R$ 8.348,09 ; R$ 8.630,10 
DROGARIA FARMAMED; R$ 11.035,62 ; R$ 9.645,55 ; R$ 6.865,81 ; R$ 7.023,50 ; R$ 6.548,49 ; R$ 5.528,90 
DROGARIA RETIRO; R$ 4.853,20 ; R$ 4.246,94 ; R$ 6.164,92 ; R$ 9.469,20 ; R$ 7.879,50 ; R$ 8.040,53 
DROGARIAS CATEDRAL; R$ 13.635,25 ; R$ 12.332,56 ; R$ 11.767,51 ; R$ 13.397,49 ; R$ 10.597,92 ; R$ 13.710,33 
FARMACIA DOSE CERTA; R$ 2.741,11 ; R$ 2.726,28 ; R$ 2.134,58 ; R$ 2.348,83 ; R$ 1.739,10 ; R$ 1.256,52 
FARMACIA LUCENA; R$ 50.081,46 ; R$ 46.240,30 ; R$ 43.594,39 ; R$ 46.053,51 ; R$ 40.792,47 ; R$ 51.068,09 
FARMACIA NACIONAL; R$ 31.138,39 ; R$ 37.501,57 ; R$ 34.974,56 ; R$ 37.444,24 ; R$ 33.662,06 ; R$ 35.846,93 
FARMACIA NICOLA; R$ 7.433,35 ; R$ 8.267,62 ; R$ 6.070,19 ; R$ 4.362,27 ; R$ 2.257,04 ; R$ 3.652,85 
FARMACIAS RIO BRANCO; R$ 55.943,48 ; R$ 59.148,38 ; R$ 53.830,28 ; R$ 45.349,29 ; R$ 46.924,29 ; R$ 57.154,61 
FARMACIAS UNIPRECO; R$ 41.848,47 ; R$ 38.018,43 ; R$ 30.263,17 ; R$ 31.212,53 ; R$ 31.212,29 ; R$ 30.872,78 
MINHA FARMA; R$ 5.018,04 ; R$ 5.836,92 ; R$ 2.744,06 ; R$ 3.934,95 ; R$ 3.575,06 ; R$ 3.139,50 
NATUS FARMA; R$ 65.317,10 ; R$ 70.165,08 ; R$ 72.558,90 ; R$ 79.048,68 ; R$ 59.422,78 ; R$ 66.277,31 
RD FARMA; R$ 16.334,88 ; R$ 21.434,15 ; R$ 23.757,47 ; R$ 19.538,03 ; R$ 22.545,76 ; R$ 27.842,72 
REDE CENTRAL; R$ 9.299,80 ; R$ 9.608,84 ; R$ 8.473,97 ; R$ 6.975,85 ; R$ 8.761,05 ; R$ 7.904,94 
Total Geral; R$ 358.447,16 ; R$ 366.034,74 ; R$ 336.722,81 ; R$ 331.162,79 ; R$ 296.574,64 ; R$ 343.746,81`;

        // Função para converter string em formato brasileiro para número
        function parseBrazilianNumber(str) {
            if (!str || str.trim() === '') return 0;
            return parseFloat(str.replace('R$', '').replace(/\./g, '').replace(',', '.').trim());
        }

        // Função para formatar número para moeda brasileira
        function formatCurrency(value) {
            return value.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
        }

        // Parse do CSV
        function parseCSV(csv) {
            const results = Papa.parse(csv, {
                delimiter: ';',
                header: true,
                skipEmptyLines: true
            });
            
            return results.data;
        }

        // Processa os dados para cálculo de rebates
        function processData(data) {
            const monthNames = ["out/24", "nov/24", "dez/24", "jan/25", "fev/25", "mar/25"];
            const futureMonths = ["abr/25", "mai/25", "jun/25"];
            const allMonths = [...monthNames, ...futureMonths];
            
            // Conversão para formato processável
            const processedData = [];
            
            data.forEach(row => {
                if (row['R¢tulos de Linha'] && row['R¢tulos de Linha'] !== 'Total Geral') {
                    const clientRow = {
                        cliente: row['R¢tulos de Linha'],
                        compras: {}
                    };
                    
                    monthNames.forEach(month => {
                        clientRow.compras[month] = parseBrazilianNumber(row[month]);
                    });
                    
                    processedData.push(clientRow);
                }
            });

            // Cálculo do rebate mensal (considerando parcelamento 30/60/90)
            const rebateRate = 0.05; // 5% de rebate
            
            // Para cada cliente, calcular o rebate para cada mês
            const rebatesByClient = processedData.map(client => {
                const rebates = { cliente: client.cliente };
                let totalRebate = 0;
                
                allMonths.forEach((month, monthIdx) => {
                    rebates[month] = 0;
                    
                    // Para cada mês, somamos as parcelas aplicáveis
                    for (let i = 0; i < 3; i++) {
                        // Se o mês atual é maior ou igual a 0 e menor que o número de meses com dados
                        if (monthIdx - i >= 0 && monthIdx - i < monthNames.length) {
                            const purchaseMonth = monthNames[monthIdx - i];
                            const parcela = (client.compras[purchaseMonth] || 0) / 3 * rebateRate;
                            rebates[month] += parcela;
                        }
                    }
                    
                    totalRebate += rebates[month];
                });
                
                rebates.total = totalRebate;
                return rebates;
            });

            // Cálculo do sumário mensal
            const monthlySummary = monthNames.map((month, idx) => {
                const monthPurchases = processedData.reduce((sum, client) => sum + (client.compras[month] || 0), 0);
                
                // Dividindo em 3 parcelas iguais
                const parcelValue = monthPurchases / 3;
                
                // Rebate para cada parcela (5%)
                const rebatePerParcel = parcelValue * rebateRate;
                
                // Meses das parcelas
                const parcel1Month = allMonths[idx + 0] || null;
                const parcel2Month = allMonths[idx + 1] || null;
                const parcel3Month = allMonths[idx + 2] || null;
                
                return {
                    month,
                    totalPurchase: monthPurchases,
                    totalRebate: monthPurchases * rebateRate,
                    parcels: [
                        { month: parcel1Month, value: rebatePerParcel },
                        { month: parcel2Month, value: rebatePerParcel },
                        { month: parcel3Month, value: rebatePerParcel }
                    ]
                };
            });

            // Cálculo do rebate recebido por mês (somando todas as parcelas aplicáveis)
            const rebateByReceiveMonth = {};
            
            allMonths.forEach(month => {
                rebateByReceiveMonth[month] = 0;
            });
            
            monthNames.forEach((purchaseMonth, idx) => {
                const totalMonthPurchase = processedData.reduce((sum, client) => sum + (client.compras[purchaseMonth] || 0), 0);
                const rebatePerParcel = (totalMonthPurchase / 3) * rebateRate;
                
                for (let i = 0; i < 3; i++) {
                    if (idx + i < allMonths.length) {
                        const receiveMonth = allMonths[idx + i];
                        rebateByReceiveMonth[receiveMonth] += rebatePerParcel;
                    }
                }
            });

            return {
                clients: processedData,
                rebatesByClient,
                monthlySummary,
                rebateByReceiveMonth
            };
        }

        // Preenche a tabela resumo
        function fillSummaryTable(monthlySummary) {
            const tbody = document.querySelector('#tabela-resumo tbody');
            tbody.innerHTML = '';
            
            let totalPurchases = 0;
            let totalRebate = 0;
            
            monthlySummary.forEach(summary => {
                const row = document.createElement('tr');
                
                row.innerHTML = `
                    <td>${summary.month}</td>
                    <td>${formatCurrency(summary.totalPurchase)}</td>
                    <td>${formatCurrency(summary.totalRebate)}</td>
                    <td>${summary.parcels[0].month ? formatCurrency(summary.parcels[0].value) : '-'}</td>
                    <td>${summary.parcels[1].month ? formatCurrency(summary.parcels[1].value) : '-'}</td>
                    <td>${summary.parcels[2].month ? formatCurrency(summary.parcels[2].value) : '-'}</td>
                `;
                
                tbody.appendChild(row);
                
                totalPurchases += summary.totalPurchase;
                totalRebate += summary.totalRebate;
            });
            
            // Adicionar linha de total
            const totalRow = document.createElement('tr');
            totalRow.className = 'total';
            
            totalRow.innerHTML = `
                <td>Total</td>
                <td>${formatCurrency(totalPurchases)}</td>
                <td>${formatCurrency(totalRebate)}</td>
                <td colspan="3"></td>
            `;
            
            tbody.appendChild(totalRow);
            
            // Atualizar as informações do dashboard
            document.getElementById('total-compras').textContent = formatCurrency(totalPurchases);
            document.getElementById('total-rebates').textContent = formatCurrency(totalRebate);
            document.getElementById('media-rebates').textContent = formatCurrency(totalRebate / monthlySummary.length);
        }

        // Preenche a tabela detalhada
        function fillDetailedTable(rebatesByClient) {
            const tbody = document.querySelector('#tabela-detalhada tbody');
            tbody.innerHTML = '';
            
            const months = ["out/24", "nov/24", "dez/24", "jan/25", "fev/25", "mar/25", "abr/25", "mai/25", "jun/25"];
            
            rebatesByClient.forEach(client => {
                const row = document.createElement('tr');
                
                let rowHtml = `<td>${client.cliente}</td>`;
                
                months.forEach(month => {
                    rowHtml += `<td>${formatCurrency(client[month] || 0)}</td>`;
                });
                
                rowHtml += `<td>${formatCurrency(client.total)}</td>`;
                
                row.innerHTML = rowHtml;
                tbody.appendChild(row);
            });
            
            // Adicionar linha de total
            const totalRow = document.createElement('tr');
            totalRow.className = 'total';
            
            let totalRowHtml = `<td>Total</td>`;
            let grandTotal = 0;
            
            months.forEach(month => {
                const monthTotal = rebatesByClient.reduce((sum, client) => sum + (client[month] || 0), 0);
                totalRowHtml += `<td>${formatCurrency(monthTotal)}</td>`;
                grandTotal += monthTotal;
            });
            
            totalRowHtml += `<td>${formatCurrency(grandTotal)}</td>`;
            
            totalRow.innerHTML = totalRowHtml;
            tbody.appendChild(totalRow);
        }

        // Preenche a tabela de parcelas
        function fillParcelsTable(monthlySummary) {
            const tbody = document.querySelector('#tabela-parcelas tbody');
            tbody.innerHTML = '';
            
            monthlySummary.forEach(summary => {
                const row = document.createElement('tr');
                
                row.innerHTML = `
                    <td>${summary.month}</td>
                    <td>${formatCurrency(summary.totalPurchase)}</td>
                    <td>${summary.parcels[0].month ? formatCurrency(summary.parcels[0].value) : '-'}</td>
                    <td>${summary.parcels[1].month ? formatCurrency(summary.parcels[1].value) : '-'}</td>
                    <td>${summary.parcels[2].month ? formatCurrency(summary.parcels[2].value) : '-'}</td>
                `;
                
                tbody.appendChild(row);
            });
            
            // Adicionar linha de total
            const totalRow = document.createElement('tr');
            totalRow.className = 'total';
            
            const totalPurchase = monthlySummary.reduce((sum, item) => sum + item.totalPurchase, 0);
            const totalParcel1 = monthlySummary.reduce((sum, item) => sum + (item.parcels[0].month ? item.parcels[0].value : 0), 0);
            const totalParcel2 = monthlySummary.reduce((sum, item) => sum + (item.parcels[1].month ? item.parcels[1].value : 0), 0);
            const totalParcel3 = monthlySummary.reduce((sum, item) => sum + (item.parcels[2].month ? item.parcels[2].value : 0), 0);
            
            totalRow.innerHTML = `
                <td>Total</td>
                <td>${formatCurrency(totalPurchase)}</td>
                <td>${formatCurrency(totalParcel1)}</td>
                <td>${formatCurrency(totalParcel2)}</td>
                <td>${formatCurrency(totalParcel3)}</td>
            `;
            
            tbody.appendChild(totalRow);
        }

        // Criar o gráfico de rebates
        function createRebateChart(rebateByReceiveMonth) {
            const ctx = document.getElementById('chartRebates').getContext('2d');
            
            const months = Object.keys(rebateByReceiveMonth).filter(month => rebateByReceiveMonth[month] > 0);
            const values = months.map(month => rebateByReceiveMonth[month]);
            
            new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: months,
                    datasets: [{
                        label: 'Rebates a Receber (R$)',
                        data: values,
                        backgroundColor: '#3498db',
                        borderColor: '#2980b9',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                callback: function(value) {
                                    return 'R$ ' + value.toLocaleString('pt-BR');
                                }
                            }
                        }
                    },
                    plugins: {
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return formatCurrency(context.raw);
                                }
                            }
                        }
                    }
                }
            });
        }

        // Função para alternar entre abas
        function openTab(evt, tabId) {
            const tabContents = document.getElementsByClassName('tab-content');
            for (let i = 0; i < tabContents.length; i++) {
                tabContents[i].classList.remove('active');
            }
            
            const tabButtons = document.getElementsByClassName('tab-button');
            for (let i = 0; i < tabButtons.length; i++) {
                tabButtons[i].classList.remove('active');
            }
            
            document.getElementById(tabId).classList.add('active');
            evt.currentTarget.classList.add('active');
        }

        // Inicialização
        document.addEventListener('DOMContentLoaded', function() {
            // Parse e processa os dados
            const rawData = parseCSV(csvData);
            const processedData = processData(rawData);
            
            // Preenche as tabelas
            fillSummaryTable(processedData.monthlySummary);
            fillDetailedTable(processedData.rebatesByClient);
            fillParcelsTable(processedData.monthlySummary);
            
            // Cria o gráfico
            createRebateChart(processedData.rebateByReceiveMonth);
        });
    </script>
</body>
</html>
