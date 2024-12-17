# Pdf.file
Unificador de PDFs: Uma aplicação web simples e eficiente que permite unificar múltiplos arquivos PDF em um único documento. Utiliza a biblioteca PDF-Lib para manipulação dos arquivos no navegador, sem necessidade de back-end. Interface intuitiva para upload e download direto dos PDFs unificados.
# Unificador de PDFs 📄

Este é um projeto que permite unir múltiplos arquivos PDF diretamente no navegador utilizando HTML, CSS e a biblioteca **PDF-Lib**.

## 🚀 Código do Projeto

```
<!-- Unificador de PDFs -->
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Unificador de PDFs</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f9f9f9;
            text-align: center;
            margin: 0;
            padding: 0;
        }
        h1 {
            margin: 20px 0;
            color: #333;
        }
        input[type="file"] {
            margin: 20px;
            padding: 10px;
            border: 1px solid #ccc;
        }
        button {
            padding: 10px 20px;
            margin-top: 20px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <h1>PDF.File</h1>
    <input type="file" id="pdfFiles" multiple accept="application/pdf">
    <br>
    <button id="mergeBtn">Unificar PDFs</button>
    <p id="status"></p>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf-lib/1.17.1/pdf-lib.min.js"></script>
    <script>
        document.getElementById('mergeBtn').addEventListener('click', async () => {
            const input = document.getElementById('pdfFiles');
            const files = input.files;

            if (files.length < 2) {
                alert("Selecione pelo menos dois arquivos PDF para unificar.");
                return;
            }

            const mergedPdf = await PDFLib.PDFDocument.create();
            const status = document.getElementById('status');
            status.textContent = "Processando...";

            try {
                for (let file of files) {
                    const reader = new FileReader();
                    const loaded = await new Promise(resolve => {
                        reader.onload = () => resolve(reader.result);
                        reader.readAsArrayBuffer(file);
                    });

                    const pdfBytes = loaded;
                    const pdfDoc = await PDFLib.PDFDocument.load(pdfBytes);
                    const copiedPages = await mergedPdf.copyPages(pdfDoc, pdfDoc.getPageIndices());

                    copiedPages.forEach((page) => mergedPdf.addPage(page));
                }

                const mergedPdfBytes = await mergedPdf.save();
                downloadPdf(mergedPdfBytes, "pdf-unificado.pdf");
                status.textContent = "PDFs unificados com sucesso!";
            } catch (error) {
                console.error(error);
                status.textContent = "Erro ao unificar PDFs.";
            }
        });

        function downloadPdf(pdfBytes, fileName) {
            const blob = new Blob([pdfBytes], { type: "application/pdf" });
            const link = document.createElement("a");
            link.href = URL.createObjectURL(blob);
            link.download = fileName;
            link.click();
        }
    </script>
</body>
</html>
