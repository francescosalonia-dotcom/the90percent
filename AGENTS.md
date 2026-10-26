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
