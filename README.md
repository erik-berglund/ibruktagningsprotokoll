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
- **Share with a QR code.** **Dela / QR-kod** shows a QR code and a link. The QR code is an image with the link
  printed below it, so it can be copied (**Kopiera bild**, or long-press / right-click) and pasted elsewhere. Opening the link on another device
  fills in the whole protocol. The data is stored as JSON with a key for every value, compressed with deflate
  and encoded as base64url. A fully filled-in protocol is about 900 characters.
- **Print.** **Skriv ut** prints a compact A4 layout modelled on the Tukes form. A typical protocol fits on one
  page.
- **Print view link.** Add `?print` before the `#` (`…/ibruktagningsprotokoll/?print#J…`) to open a protocol
  read-only in its print layout, with the print dialog open, so it can be saved as PDF. This mode never saves
  anything in the browser, so it's safe for teachers opening students' links.
- **Help for students.** **Innan du börjar mäta** and the **Hjälp** buttons in sections 1–6 explain why each
  measurement is done, what is approved, and how to measure step by step with a Fluke 1662/1663/1664 FC or a
  Beha-Amprobe Telaris ProInstall-100 or -200.
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

The form is defined by `SECTIONS` in `index.html`. Links store every value under its field key, so fields and
table columns can be added, removed or reordered without breaking old links:

- **New fields** are simply empty when an older link is opened.
- **Values the form doesn't know** are shown in a notice above the form when a link is opened. That covers
  removed fields, fields whose type changed, and choice options that no longer exist. They're not loaded into
  the form, saved or printed, and the notice can be closed.
- **Never reuse a key for something else.** When you remove a field, add its key and a readable label to
  `RETIRED`, so the notice can name it.
