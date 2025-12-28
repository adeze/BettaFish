# Third-Party JavaScript Libraries

This directory contains third-party JavaScript libraries required for HTML report rendering. These libraries are inlined into the generated HTML files for use in offline environments.

## Included Libraries

1. **chart.js** (204KB) - Used for chart rendering
   - Version: 4.5.1
   - Source: https://cdn.jsdelivr.net/npm/chart.js

2. **chartjs-chart-sankey.js** (10KB) - Sankey chart plugin
   - Version: 0.12.0
   - Source: https://unpkg.com/chartjs-chart-sankey@0.12.0/dist/chartjs-chart-sankey.min.js

3. **html2canvas.min.js** (194KB) - HTML to Canvas conversion tool
   - Version: 1.4.1
   - Source: https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js

4. **jspdf.umd.min.js** (356KB) - PDF export library
   - Version: 2.5.1
   - Source: https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js

5. **mathjax.js** (1.1MB) - Mathematical formula rendering engine
   - Version: 3.2.2
   - Source: https://cdn.jsdelivr.net/npm/mathjax@3.2.2/es5/tex-mml-chtml.js

## Functionality

The HTML renderer (`html_renderer.py`) automatically loads these library files from this directory and inlines them into the generated HTML. This approach has the following advantages:

- ✅ Offline availability - Reports display correctly without network connection
- ✅ Fast loading - Independent of external CDNs
- ✅ High stability - Unaffected by CDN service disruptions
- ✅ Fixed versions - Ensures feature consistency

## Fallback Mechanism

If library file loading fails (e.g., file doesn't exist or read error), the renderer automatically falls back to using CDN links, ensuring normal operation under any circumstances.

## Updating Library Files

To update library files:

1. Download the latest version from the respective CDN
2. Replace the corresponding file in this directory
3. Update version information in this README file

## Notes

- Total size approximately 1.86MB, which increases the generated HTML file size
- These libraries are still included even for simple reports that don't require charts and mathematical formulas
- If file size reduction is needed, consider using lighter alternatives
