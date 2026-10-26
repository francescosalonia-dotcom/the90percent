# AGENTS.md — Schiaffo / Punti di Fuga (Codex rules)

Applicare queste istruzioni a ogni pagina, componente, grafico, test ed esportazione.

## Identità non negoziabile
- Un solo brand: **Punti di Fuga / Schiaffo**. Visivamente audace, editorialmente accurato, interattivamente leggibile.
- Font: `Archivo Black` solo per display/wordmarks/titoli grandi; `Space Grotesk` per testo e UI; `DM Mono` per valori, assi, indici, filtri e fonti.
- Colori e scale: usare i token `--sf-...` quando esistono nel progetto; non introdurre nuovi colori senza motivazione. In questa consegna non è incluso un file token autonomo: usa `design-system/SCHIAFFO_PREVIEW.html` come riferimento visivo e non presumere che contenga un sistema di token implementabile.
- Layout squadrato, bordi netti, carta calda, acid come superficie di enfasi, blu per la selezione, arancio come annotazione, NON come connotazione politica o morale.
- Evitare: gradienti decorativi, neumorfismo, blur, card con radius grandi, ombre soffici, animazioni continue, dashboard generiche in stile SaaS.

## Regole di costruzione
- Prima di codificare, specificare domanda giornalistica, popolazione, unità, anno, metrica, sorgente, chart type e insight.
- Ogni chart DEVE avere titolo informativo, periodo, unità, dati visibili in tabella, sorgente reale o etichetta chiara «DATI SINTETICI», eventuali limiti della serie e possibilità di esportazione CSV.
- Chart accessibili: tastiera, focus visibile, label semantici, colore mai unico canale di significato, supporto `prefers-reduced-motion`.
- Semantica: `<button>` per azioni, `<a>` per navigare, mai click handler solo su `<div>`; tab conforme ARIA; toggle con `aria-pressed` o checkbox corretto.
- URL shareable: filtri importanti su query string, gestire stato e back/forward; usare `it-IT` per valori e formati, ISO per storage.
- Se dati sintetici: banner sempre visibile **DATI SINTETICI — ESEMPIO NON RIFERITO ALL'ITALIA**. Mai numeri simulati presentati come fatti veri.
- Mobile: senza hover, tappabilità >=44x44px, tabella scorrevole, niente colonne che impediscano la lettura.
- Un'interazione primaria per grafico; progressive disclosure per secondarie. Niente autoplay essenziale e niente gating della storia dietro gesti complessi.

## Gerarchia tecnica
- Prima di sviluppare interfacce o data visualization, consulta questa guida, `design-system/SCHIAFFO_DATA_VIZ.md` e la preview `design-system/SCHIAFFO_PREVIEW.html` (con `design-system/SCHIAFFO_ANTEPRIMA.png` come riferimento statico). La preview è un riferimento, non una libreria di componenti pronta all'uso. Questa consegna non include token/componenti autonomi, `INTERACTIONS.md`, `DATA_ETHICS.md` o un archivio ZIP: non presumere che esistano. Se il repository introduce un sistema di token o componenti, consulta e riusa quello prima di aggiungere nuove definizioni.
- Se i vincoli divergono, preferire correttezza statistica e accessibilità rispetto all'impatto estetico; documentare ogni eccezione con commento `SCHIAFFO-EXCEPTION:`.

## Output richiesto a Codex
A ogni nuova vista: fornire (1) screenshot desktop/mobile o resoconto dei limiti del test, (2) test dei casi vuoto/errore/dati mancanti, (3) controllo del contrasto, (4) conferma label/periodo/fonte, (5) modifiche ai token se introdotte, (6) test tastiera e tabella equivalente.

## Checklist minima prima del merge
- [ ] Zero colori hardcoded eccetto quelli dichiarati nel file token e nel generatore
- [ ] Nessun grafico senza unità, anno, fonte, popolazione
- [ ] Tastiera, screenreader e reduced motion testati
- [ ] Contrasto testo normale >= 4.5:1 e elementi grafici informativi >= 3:1 quando applicabile
- [ ] Mobile 360px senza overflow non intenzionale
- [ ] Tabella e CSV coerenti con i numeri del grafico
- [ ] Stati caricamento/vuoto/errore e origine dei dati espliciti

## Canonical project workspace
- The source of truth for this project is https://github.com/francescosalonia-dotcom/the90percent. Perform project code and design work in this repository, not in the local `The 90%` artifact folder.
- Before implementation, inspect this repository's current branch and application structure. Do not treat generated local output as source code.

## Logo ufficiale — The 90 Percent

### Asset canonico

L'unico logo autorizzato per The 90 Percent è **[the-90-percent-logo.svg](design-system/assets/the-90-percent-logo.svg)**. Questo SVG è la componente ufficiale del progetto: riutilizzarlo direttamente ogni volta che serve il logo.

Il lockup ha il wordmark a sinistra e il segno geometrico a destra: nove quadrati blu oltremare in una matrice 3×3 e un quadrato giallo acido separato, allineato alla riga inferiore. Le dieci forme sono parte integrante del segno.

### Regole d'uso

- Usare sempre il file SVG canonico, senza ridisegnarlo o sostituirlo con altre proposte o versioni precedenti.
- Non cambiare ordine, posizione, distanza o proporzioni tra wordmark e matrice; non isolare la matrice né il wordmark come logo autonomo.
- Non alterare colori, caratteri, spaziatura, forme, orientamento o rapporto d'aspetto. Non applicare filtri, maschere, ombre o ritagli.
- Ridimensionare proporzionalmente e caricare i font SCHIAFFO previsti (Archivo Black e DM Mono). Preferire superfici carta o chiare, che mantengono il contrasto originale.
- Nelle interfacce usare l'SVG come asset condiviso, per esempio: `<img src="/design-system/assets/the-90-percent-logo.svg" alt="The 90 Percent">`.
- Se il lockup non entra nello spazio disponibile, omettere il marchio: non creare una variante compatta o alternativa.

Qualunque uso del logo del progetto deve quindi puntare a questo asset e a nessun'altra variante.
