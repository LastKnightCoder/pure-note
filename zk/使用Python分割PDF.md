---
title: 使用Python分割PDF
created: 2023/2/12 14:22:10
updated: 2023/2/12 14:22:10
tags: [Python, PyPDF2, PDF, demo]
---

下载 `PyPDF2`

```bash
pip install PyPDF2
```

分割 PDF

```python
from PyPDF2 import PdfFileWriter, PdfFileReader

start = 67
end = 198

in_file = 'in.pdf'
out_file = 'out.pdf'

output = PdfFileWriter()
with open(in_file, 'rb') as in_pdf:
    pdf = PdfFileReader(in_pdf)
    for i in range(start, end):
        output.addPage(pdf.getPage(i))
        
    with open(out_file, 'wb') as out_pdf:
        output.write(out_pdf)
```

[[PDF Split.ipynb]]