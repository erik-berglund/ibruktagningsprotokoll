# Ibruktagningsprotokoll

A mobile-first web form for the commissioning inspection record (ibruktagningsprotokoll /
käyttöönottotarkastuspöytäkirja) of electrical installations, for students at Axxell.

The structure follows Tukes' example record, and the Swedish wording follows ST 51.21.08. The form covers
basic details, sensory inspection, protective-conductor continuity, insulation resistance, automatic
disconnection, RCDs, phase sequence and function tests, EMC, operating and maintenance instructions, the standards
applied, and the inspector.

## How it works

- **One file, no server needed.** `index.html` holds everything (HTML, CSS and JS). There's no build step and no
  backend. The only outside dependency is the QR code library, loaded from cdnjs.
- **Stateless.** Everything you type is saved in the browser (localStorage) and, at the same time, in the page
  address after `#`. The `#` part is never sent to the web server, so no data leaves the device unless you
  share the link yourself.
- **Share with a QR code.** **Dela / QR-kod** shows a QR code and a link. Opening the link on another device
  fills in the whole protocol. The data is packed into a compact binary format, compressed with deflate and
  encoded as base64url. A fully filled-in protocol is about 750 characters.
- **Print.** **Skriv ut** prints a compact A4 layout modelled on the Tukes form. A typical protocol fits on one
  page.
- Fields marked **\*** and sections marked **Krävs** are required content, based on our reading of government
  decree 1434/2016 § 4 and SFS 6000-6.

## Run locally

```bash
python3 -m http.server 8780 --bind 127.0.0.1
```

Then open http://localhost:8780. Opening `index.html` directly as a file also works, but share links then point to a
file on your computer.

## Hosting

Any static host works, for example GitHub Pages. Serve it over HTTPS, so the browser's compression API and the
clipboard work.

## Changing the form

The form is defined by `SECTIONS` in `index.html`, and the link format is derived from the order of the fields.
Adding, removing or reordering fields therefore breaks existing links. When you do that, bump `FORMAT_VERSION`.
Links made with the old version then show an error instead of loading wrong data.
