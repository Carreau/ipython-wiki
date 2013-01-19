Officially, IPython Notebook works with the following browsers:

- Safari ≥ 5
- Firefox ≥ 6
- Chrome / Chromium ≥ 13

General unsupported browsers:

- Safari < 5
- Firefox < 6
- Chrome < 13
- Opera (any): CSS issues, but execution might work
- Internet Explorer (any): websockets may work in IE10, but there will be CSS issues

The following specific combinations are known **NOT** to work:

- Safari, IPython 0.12, tornado ≥ 2.2.0
- Safari with HTTPS connection to notebook and an untrusted certificate (websockets will fail)

