# MD2PDF

Sdílený nástroj **Markdown → profesionálně vysázené PDF**: vlastní řádkový MD parser v PHP + dva renderery — **mPDF** (default, čistě PHP) nebo **headless Chrome + GhostScript** (vektorový mermaid). Junction na `c:\work\MD2PDF`; používá se napříč projekty. **Sdílené znalosti jsou ve vaultu `c:\work\_knowledge`** — čti relevantní noty před prací a nové poznatky zapisuj tam (viz globální `~/.claude/CLAUDE.md`).

## Společné (vault)
- `playbooks/md2pdf.md` — pravidla psaní MD pro hezké PDF (žádné ASCII-art wireframy → mermaid; nativní MD tabulky; omezení emoji; `mermaid height=`; renderer `chrome` vs `mpdf`).
- `infra/stack.md` — dev stanice (Windows, PowerShell 7, PHP `php -l`, Node v24, GhostScript/Poppler/Chromium cesty).
- `infra/windows-gotchas.md` — Windows/PowerShell pasti (kódování, here-stringy, cesty).
- `playbooks/git-deployment-rules.md` — pravidla pro Git/deploy (force-push zakázán, `@` v commit message).

## Specifika projektu
- **Stack:** PHP 8.0+ engine `md2pdf.php` (Composer vendoruje `mpdf/mpdf ^8.2`). Renderer `chrome` přes Node (`render-pdf.mjs`, `puppeteer` + `@mermaid-js/mermaid-cli`) a GhostScript. Bez DB.
- **Engine vs config:** engine je projektově nezávislý a **nemění se** — vše specifické (cesty, výběr souborů, identita, překlad, logo, výběr rendereru) žije v `md2pdf.config.php` (vzor `md2pdf.config.sample.php`). Každý projekt má jen svůj `md2pdf.config.php` + `export-pdf.ps1`/`export-pdf.sh`, engine se odkazuje (env `MD2PDF_HOME`).
- **Spuštění:** `export-pdf.ps1` (`-Preview` → náhledové PNG v `pdf/_preview`, vizuálně zkontroluj; `-Only <basename>` pro jeden dokument). Výstup do `pdf/`.
- **Fonty:** embedované volné fonty (Source Sans 3 + Cascadia Mono + DejaVu) ve `fonts/`; `cleanup-mpdf-fonts.php` běží jako Composer post-install/update hook (ořez balíku mPDF fontů).
- **Renderery:** `mpdf` (default, menší soubory, mermaid jako PNG přes mermaid-cli) vs `chrome` (vektorové SVG mermaid, downsample obrázků; když Chrome chybí, fallback na `mpdf`).
- **Source of truth:** `README.md` v repu (CZ + EN) — kompletní popis vlastností, requirements, configu a combine režimu.
