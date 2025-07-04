<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>What2Buy product comparison tool</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; }
    table { border-collapse: collapse; width: 100%; margin-top: 20px; }
    th, td { border: 1px solid #ccc; padding: 8px; text-align: center; }
    .highlight { background: #cfffcf; font-weight: bold; }
    input[type="text"], input[type="number"], select { width: 100px; }
    #generateBtn { margin-top: 10px; }
  </style>
</head>
<body>
  <h2>What2Buy product comparison tool</h2>
  <p>Step 1: Enter products and features, assign importance weights, score direction, and input raw values.</p>

  <label>Number of Products: <input type="number" id="numProducts" value="3" min="1" max="10"></label>
  <label>Number of Features: <input type="number" id="numFeatures" value="4" min="1" max="10"></label>
  <button id="generateBtn" onclick="generateTable()">Generate Table</button>

  <div id="tableContainer"></div>

  <script>
    function generateTable() {
      const numProducts = parseInt(document.getElementById('numProducts').value);
      const numFeatures = parseInt(document.getElementById('numFeatures').value);
      const container = document.getElementById('tableContainer');
      container.innerHTML = '';

      const table = document.createElement('table');
      const thead = table.createTHead();
      const headerRow = thead.insertRow();
      headerRow.insertCell().outerHTML = '<th>Feature ↓ / Product →</th>';
      headerRow.insertCell().outerHTML = '<th>Weight</th>';
      headerRow.insertCell().outerHTML = '<th>Direction</th>';

      for (let j = 0; j < numProducts; j++) {
        const cell = headerRow.insertCell();
        cell.innerHTML = `<input type="text" id="product_${j}" placeholder="Product ${j+1}">`;
      }

      const tbody = table.createTBody();
      for (let i = 0; i < numFeatures; i++) {
        const row = tbody.insertRow();
        row.insertCell().innerHTML = `<input type="text" id="feature_${i}" placeholder="Feature ${i+1}">`;
        row.insertCell().innerHTML = `<input type="text" id="w${i}" placeholder="Weight">`;
        row.insertCell().innerHTML = `
          <select id="dir${i}">
            <option value="high">Higher is Better</option>
            <option value="low">Lower is Better</option>
          </select>`;
        for (let j = 0; j < numProducts; j++) {
          row.insertCell().innerHTML = `<input type="text" id="s${i}_${j}" placeholder="Raw Value">`;
        }
      }

      const totalRow = tbody.insertRow();
      totalRow.insertCell().outerHTML = '<td><b>Total Score</b></td>';
      totalRow.insertCell();
      totalRow.insertCell();
      for (let j = 0; j < numProducts; j++) {
        const td = document.createElement('td');
        td.id = `t${j}`;
        td.textContent = '0';
        totalRow.appendChild(td);
      }

      const button = document.createElement('button');
      button.textContent = 'Calculate Scores';
      button.onclick = () => calculate(numFeatures, numProducts);
      container.appendChild(table);
      container.appendChild(button);
    }

    function calculate(features, products) {
      let totals = Array(products).fill(0);
      for (let i = 0; i < features; i++) {
        const w = parseFloat(document.getElementById(`w${i}`).value);
        const dir = document.getElementById(`dir${i}`).value;
        if (isNaN(w)) continue;

        let values = [];
        for (let j = 0; j < products; j++) {
          const val = parseFloat(document.getElementById(`s${i}_${j}`).value);
          values.push(isNaN(val) ? null : val);
        }

        const validValues = values.filter(v => v !== null);
        const min = Math.min(...validValues);
        const max = Math.max(...validValues);

        for (let j = 0; j < products; j++) {
          const val = values[j];
          if (val === null || max === min) continue;
          let normalized;
          if (dir === 'high') {
            normalized = (val - min) / (max - min) * 10;
          } else {
            normalized = (max - val) / (max - min) * 10;
          }
          totals[j] += normalized * w;
        }
      }
      const maxTotal = Math.max(...totals);
      for (let j = 0; j < products; j++) {
        const cell = document.getElementById(`t${j}`);
        cell.textContent = totals[j].toFixed(1);
        cell.className = totals[j] === maxTotal ? 'highlight' : '';
      }
    }

    // Auto-generate default on load
    window.onload = generateTable;
  </script>
</body>
</html>
# What2buy
