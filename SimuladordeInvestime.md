# [title:Simulador de Investimentos]#html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Simulador de Investimentos</title>

  <!-- CSS -->
  <link rel="stylesheet" href="css/style.css">

  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>

<body>
  <div class="container">
    <h1>Simulador de Investimentos</h1>

    <div class="grid">
      <div>
        <label>Valor inicial (R$)</label>
        <input type="number" id="inicial" value="10000">
      </div>

      <div>
        <label>Aporte mensal (R$)</label>
        <input type="number" id="aporte" value="500">
      </div>

      <div>
        <label>Tempo (meses)</label>
        <input type="number" id="meses" value="24">
      </div>

      <div>
        <label>Inflação (IPCA anual %)</label>
        <input type="number" id="ipca" value="4.5">
      </div>
    </div>

    <button onclick="simular()">Simular</button>

    <div id="resultado" class="resultado"></div>

    <canvas id="grafico"></canvas>
  </div>

  <!-- JS -->
  <script src="js/script.js"></script>
</body>
</html>
#css
body {
  font-family: Arial, sans-serif;
  background: #f2f4f7;
  margin: 0;
  padding: 20px;
}

.container {
  max-width: 900px;
  margin: auto;
  background: #ffffff;
  padding: 20px;
  border-radius: 8px;
}

h1 {
  text-align: center;
}

.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
  margin-bottom: 20px;
}

label {
  font-weight: bold;
}

input {
  width: 100%;
  padding: 8px;
  margin-top: 5px;
}

button {
  width: 100%;
  padding: 10px;
  background: #2563eb;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background: #1e4fd6;
}

.resultado {
  background: #eef2f5;
  padding: 15px;
  border-radius: 6px;
  margin-top: 20px;
}

canvas {
  margin-top: 30px;
}
#javascript
let grafico;

function calcularIR(tipo, meses, lucro) {
  if (tipo === "CDB" || tipo === "Tesouro") {
    if (meses <= 6) return lucro * 0.225;
    if (meses <= 12) return lucro * 0.20;
    if (meses <= 24) return lucro * 0.175;
    return lucro * 0.15;
  }
  return lucro * 0.15;
}

function simular() {
  const inicial = Number(document.getElementById("inicial").value);
  const aporte = Number(document.getElementById("aporte").value);
  const meses = Number(document.getElementById("meses").value);
  const ipca = Number(document.getElementById("ipca").value) / 100;

  const investimentos = [
    { nome: "CDB (110% CDI)", taxa: 0.009 },
    { nome: "Tesouro IPCA+", taxa: 0.008 },
    { nome: "Fundos", taxa: 0.010 },
    { nome: "Ações", taxa: 0.012 }
  ];

  let resultadoHTML = "";
  let labels = [];
  let datasets = [];

  investimentos.forEach(inv => {
    let saldo = inicial;
    let historico = [];

    for (let i = 1; i <= meses; i++) {
      saldo += aporte;
      saldo *= (1 + inv.taxa);
      historico.push(saldo.toFixed(2));
    }

    let totalInvestido = inicial + (aporte * meses);
    let lucro = saldo - totalInvestido;
    let imposto = calcularIR(inv.nome, meses, lucro);
    let saldoLiquido = saldo - imposto;

    let inflacaoMensal = Math.pow(1 + ipca, 1 / 12) - 1;
    let fatorInflacao = Math.pow(1 + inflacaoMensal, meses);
    let saldoReal = saldoLiquido / fatorInflacao;

    resultadoHTML += `
      <strong>${inv.nome}</strong><br>
      Total investido: R$ ${totalInvestido.toFixed(2)}<br>
      Saldo líquido: R$ ${saldoLiquido.toFixed(2)}<br>
      Saldo real (IPCA): R$ ${saldoReal.toFixed(2)}<br><br>
    `;

    datasets.push({
      label: inv.nome,
      data: historico,
      tension: 0.3
    });
  });

  document.getElementById("resultado").innerHTML = resultadoHTML;

  labels = Array.from({ length: meses }, (_, i) => `Mês ${i + 1}`);

  if (grafico) grafico.destroy();

  grafico = new Chart(document.getElementById("grafico"), {
    type: "line",
    data: { labels, datasets },
    options: {
      responsive: true,
      plugins: {
        title: {
          display: true,
          text: "Comparativo de Crescimento dos Investimentos"
        }
      }
    }
  });
}
#md
# Simulador de Investimentos

Simulador web para comparação de investimentos (CDB, Tesouro, Fundos e Ações),
com cálculo de imposto de renda e ajuste pela inflação (IPCA).

## Funcionalidades
- Aportes mensais
- Rentabilidade composta
- Imposto de Renda
- Ganho real (inflação)
- Gráfico comparativo (Chart.js)

⚠️ Este projeto tem finalidade educacional e não constitui recomendação de investimento.

#pgsql
simulador-investimentos/
├── index.html
├── css/
│   └── style.css
├── js/
│   └── script.js
├── LICENSE
└── README.md

