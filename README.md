# Sportovni-den
<!DOCTYPE html>
<html lang="cs">
<head>
  <meta charset="UTF-8">
  <title>Sportovní výsledky</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      max-width: 900px;
      margin: auto;
    }
    input, button, select {
      margin: 5px;
      padding: 5px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: center;
    }
    th {
      background-color: #f2f2f2;
    }
  </style>
</head>
<body>

  <h1>Sportovní výsledky</h1>

  <h2>Přidat sportovce</h2>
  <input type="text" id="athleteName" placeholder="Jméno sportovce">
  <button onclick="addAthlete()">Přidat</button>

  <h2>Přidat disciplínu</h2>
  <input type="text" id="disciplineName" placeholder="Název disciplíny">
  <button onclick="addDiscipline()">Přidat</button>

  <h2>Zadání výsledků</h2>
  <div id="resultsForm"></div>

  <h2>Celkové výsledky</h2>
  <table id="resultsTable">
    <thead>
      <tr id="tableHeader">
        <th>Sportovec</th>
        <!-- Dynamicky přidané disciplíny -->
        <th>Součet umístění</th>
      </tr>
    </thead>
    <tbody id="tableBody"></tbody>
  </table>

  <script>
    let athletes = [];
    let disciplines = [];
    let results = {}; // { "Jméno": { "Disciplína": umístění, ... }, ... }

    function addAthlete() {
      const name = document.getElementById("athleteName").value.trim();
      if (name && !athletes.includes(name)) {
        athletes.push(name);
        results[name] = {};
        document.getElementById("athleteName").value = "";
        updateResultsForm();
        updateTable();
      }
    }

    function addDiscipline() {
      const name = document.getElementById("disciplineName").value.trim();
      if (name && !disciplines.includes(name)) {
        disciplines.push(name);
        document.getElementById("disciplineName").value = "";
        updateResultsForm();
        updateTable();
      }
    }

    function updateResultsForm() {
      const container = document.getElementById("resultsForm");
      container.innerHTML = "";

      disciplines.forEach(discipline => {
        const div = document.createElement("div");
        div.innerHTML = `<h4>${discipline}</h4>`;
        athletes.forEach(athlete => {
          const inputId = `result-${athlete}-${discipline}`;
          div.innerHTML += `
            ${athlete}: 
            <input type="number" id="${inputId}" value="${results[athlete][discipline] ?? ''}" 
                   min="1" style="width: 60px" 
                   onchange="saveResult('${athlete}', '${discipline}', this.value)">
            <br>
          `;
        });
        container.appendChild(div);
      });
    }

    function saveResult(athlete, discipline, value) {
      const num = parseInt(value);
      if (!isNaN(num) && num > 0) {
        results[athlete][discipline] = num;
      } else {
        delete results[athlete][discipline];
      }
      updateTable();
    }

    function updateTable() {
      const headerRow = document.getElementById("tableHeader");
      const body = document.getElementById("tableBody");

      // Vyčistíme tabulku
      headerRow.innerHTML = "<th>Sportovec</th>";
      disciplines.forEach(d => {
        const th = document.createElement("th");
        th.textContent = d;
        headerRow.appendChild(th);
      });
      headerRow.innerHTML += "<th>Součet</th>";

      body.innerHTML = "";

      athletes.forEach(athlete => {
        const row = document.createElement("tr");
        let sum = 0;

        const nameCell = document.createElement("td");
        nameCell.textContent = athlete;
        row.appendChild(nameCell);

        disciplines.forEach(discipline => {
          const cell = document.createElement("td");
          const val = results[athlete][discipline];
          if (val) {
            cell.textContent = val;
            sum += val;
          } else {
            cell.textContent = "-";
          }
          row.appendChild(cell);
        });

        const sumCell = document.createElement("td");
        sumCell.textContent = sum;
        row.appendChild(sumCell);

        body.appendChild(row);
      });
    }
  </script>

</body>
</html>
