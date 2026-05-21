<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Remedial Schedule Finder</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 40px auto; max-width: 900px; padding: 0 20px; color: #333; }
        h1 { color: #2c3e50; }
        input { padding: 12px; width: 100%; max-width: 400px; margin-bottom: 20px; border: 2px solid #bdc3c7; border-radius: 6px; font-size: 16px; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        th { background-color: #34495e; color: white; position: sticky; top: 0; }
        tr:nth-child(even) { background-color: #f9f9f9; }
        tr:hover { background-color: #f1f1f1; }
        .loading { font-style: italic; color: #7f8c8d; }
    </style>
</head>
<body>

    <h1>📚 Remedial Schedule Finder</h1>
    <p>Type your name, student ID, teacher, or subject to find your slot.</p>
    
    <input type="text" id="searchInput" onkeyup="filterTable()" placeholder="Search...">

    <div id="loading" class="loading">Loading schedule...</div>
    <table id="scheduleTable" style="display:none;">
        <thead id="tableHeader"></thead>
        <tbody id="tableBody"></tbody>
    </table>

    <script>
        // ⚠️ CHANGE THIS to match the exact name of your CSV file!
        const csvFileName = "remedial_schedule.csv"; 

        // Fetch and parse the CSV file
        Papa.parse(csvFileName, {
            download: true,
            header: true,
            skipEmptyLines: true,
            complete: function(results) {
                document.getElementById('loading').style.display = 'none';
                document.getElementById('scheduleTable').style.display = 'table';
                setupTable(results.data);
            },
            error: function(err) {
                document.getElementById('loading').innerText = "Error loading the schedule file. Ensure the CSV name matches.";
            }
        });

        function setupTable(data) {
            if (data.length === 0) return;
            
            // Build Headers dynamically based on your CSV columns
            const headers = Object.keys(data[0]);
            const headerRow = document.getElementById('tableHeader');
            let headerHtml = '<tr>';
            headers.forEach(header => { headerHtml += `<th>${header}</th>`; });
            headerHtml += '</tr>';
            headerRow.innerHTML = headerHtml;

            // Build Rows
            const tableBody = document.getElementById('tableBody');
            let bodyHtml = '';
            data.forEach(row => {
                bodyHtml += '<tr>';
                headers.forEach(header => {
                    bodyHtml += `<td>${row[header] || ''}</td>`;
                });
                bodyHtml += '</tr>';
            });
            tableBody.innerHTML = bodyHtml;
        }

        // Search Filter function
        function filterTable() {
            let input = document.getElementById("searchInput").value.toUpperCase();
            let table = document.getElementById("tableBody");
            let tr = table.getElementsByTagName("tr");

            for (let i = 0; i < tr.length; i++) {
                let rowText = tr[i].textContent || tr[i].innerText;
                if (rowText.toUpperCase().indexOf(input) > -1) {
                    tr[i].style.display = "";
                } else {
                    tr[i].style.display = "none";
                }
            }
        }
    </script>
</body>
</html>
