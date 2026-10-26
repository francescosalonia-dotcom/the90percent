# SCHIAFFO / Data Visualization System

## 1. Selezionare il grafico a partire dalla domanda

Ispirazione metodologica: FT Visual Vocabulary (scelta per relazione nei dati), ONS (titoli, semantica e coerenza di colori), Our World in Data (vista grafico/tabella/download), Datawrapper (accessibilità). Fonti in `BENCHMARKS.md`.

Il chart chooser è **normativo**: prima chiedersi quale relazione serve mostrare.

| Domanda giornalistica | Chart preferito | Alternative | Anti-pattern |
|---|---|---|---|
| Chi possiede quale quota? | barre ordinate / dot plot | Lorenz se concentrato su distribuzione | torta con 10 fette |
| Quanto è concentrata la ricchezza? | Lorenz + benchmark 45° | top shares e mini-bars | area senza definizioni |
| Come sono variate le quote dal 1990? | line chart, small multiples | slope con soli due anni | animazione senza endpoint |
| Quanto differiscono due generazioni? | slope / dumbbell | barra divergente | due torte |
| Dove arrivano i figli di ogni quintile? | heatmap 5×5 | 100 dots, Sankey per la narrazione | chord non leggibile |
| Quali passaggi sono più frequenti? | matrice condizionata, stacked bars | alluvial con pochi nodi | flussi senza conservazione dei totali |
| Reddito e patrimonio sono correlati? | scatter + trend appropriato | hexbin se dense | line con categorie |
| Come cambiano le opportunità per provincia? | choropleth con legenda quantitativa | dot plot ordinato | colori categoriali per gradiente |
| Quanto contano regione, istruzione, coorte? | small multiples | filter/select group | tutti i gruppi sovrapposti |
| Come cambia rispetto alla mediana? | diverging bar | dot plot con linea media | scala asimmetrica nascosta |
| Qual è la distribuzione delle famiglie? | histogram / ridgeline se comparazioni | box plot + dati | media singola |
| Come si compone la spesa pubblica? | stacked bars o treemap se gerarchia | bar ordinato | 3D / exploded pie |

## 2. Anatomia normativa `SfChartFrame`

Ordine visivo:

```
[INDEX 03 / RELAZIONE: TRANSIZIONE]       [CONFIGURAZIONI SALVABILI]
Titolo che esplicita il messaggio o la domanda
Contesto: popolazione · anno/coorte · misura · unità
[FilterBar: dimensione / confronto / periodo] [RESET]
[Grafico]  + [annotazione primaria, descrizione del risultato selezionato]
[LEGENDA ETICHETTATA + dove applicabile SCALA/ASSE]
[Grafico | Tabella]  [Scarica CSV] [Copia link] [Metodo]
Fonte: ente · dataset · periodo di riferimento · anno aggiornamento · link
Limiti: campione, definizioni, confidence intervals, discontinuità
```

**Contract TS indipendente dal framework:**

```ts
type ChartMeta = {
  id: string;
  question: string;
  takeaway: string; // Esito principale, comprensibile senza hover
  population: string;
  geography: string;
  period: string;
  measure: string;
  unit: string;
  denominator?: string;
  source: { org: string; dataset: string; url?: string; updatedAt?: string };
  methodology?: string;
  limitations?: string[];
  synthetic: boolean;
};
type VizState = {
  view: 'chart' | 'table';
  filters: Record<string, string | string[]>;
  selectedKey?: string | null;
  comparisonKeys?: string[];
};
```

Il chart ha un `<figure>` e `<figcaption>`, `aria-label` concise e tabella HTML alternativa. Per chart complessi è preferibile un sommario testuale aggiornato (aria-live polite) a centinaia di SVG focusables.

## 3. Regole universali di grafica

- Asse parte da 0 per barre e aree salvo motivazione analitica esplicita; linee possono usare range non zero con annotazione chiara.
- Valori direttamente etichettati quando max 6–8, altrimenti assi + tooltip e tabella.
- Legenda accanto al grafico; test su mobile senza hover.
- Palette quantitativa monotona e contrastata, con legenda visibile e valori dei cutoffs.
- Evitare arcobaleni, 3D, pie charts con più di 3 sezioni, encoding multipli non essenziali.
- Ordinamento predefinito per valore o semantica geografica; quintili sempre I→V in entrambe le dimensioni.
- Annotare 1–2 fatti per chart, con una linea guida/bordo, senza coprire punti.
- Se ci sono filtri: i dati reali filtrati, numeri visibili, annunci per lettori screenreader e CSV **devono concordare**.
- Il dato evidenziato va differenziato anche per peso/outline/posizione, non solo tinta.
- Le interazioni devono avere anche un modello statico leggibile su stampa e social.
- Se la serie contiene dato mancante: non interpolare senza marcatura e consenso metodologico.

## 4. I 16 template riutilizzabili

Ogni template descrive in input, default, azione primaria, alternativa e cautele.

### V01 — RankBar / quote per decile

**Quando:** quota di reddito/patrimonio per 10 decili o categorie comparabili. **Input:** `category`, `value`, `unit`, `period`, `population`. **Default:** decili ordinati I→X, scala assoluta zero, etichette dirette. **Interazione:** click/tap/focus riga → dettaglio persistente; switch redditi/patrimoni tramite segmented; optional compare periodi. **Mobile:** barre orizzontali. **Fallback:** tabella ordinata. **Cautela:** le quote devono sommare 100% solo per variabili additive e categorie esaustive; gli importi medi NON devono essere ripartiti come quote.

### V02 — DivergingBars / differenze dalla media

**Quando:** Δ punti percentuali tra regioni/coorti e media nazionale. **Input:** label + signed `delta_pp` + base comparator. **Default:** linea 0 centrale; negativi a sinistra, positivi a destra. **Interazione:** selettore benchmark (Italia/Europa) con legenda aggiornata. **Fallback:** tabella `differenza pp`. **Cautela:** mai etichettare Δpp come `%`.

### V03 — TrendLine / serie storiche

**Quando:** trend di Gini, quota top 10%, mobilità tra coorti. **Input:** `year`, `series`, `value`, `unit`, `breaks`. **Default:** 1 linea enfatizzata, max 3 restanti neutre; diretto anno finale. **Interazione:** periodo selezionabile, focus sul punto, confronto. **Fallback:** tabella di annualità. **Cautela:** fonte omogenea; segnalare cambi di definizione e anni assenti; anno reale ≠ annata di pubblicazione.

### V04 — SlopeDumbbell / confronto due periodi

**Quando:** Italia 1990 vs 2025 o gruppo A vs B. **Input:** `group`, `start`, `end`, `startPeriod`, `endPeriod`. **Default:** asse comune; estremi e differenza con segno. **Interazione:** sort delta/valore finale; evidenziazione gruppo. **Mobile:** dumbbell orizzontali se >5 gruppi. **Cautela:** scale e composizioni coerenti.

### V05 — LorenzCurve / concentrazione

**Quando:** mostrare quanto la quota cumulata si discosti dall'eguaglianza. **Input:** quote cumulative popolazione e risorsa, ordinate con pesos/ponderazioni corrette. **Default:** diagonale 45°, Lorenz empirica, legenda; Gini SOLO se calcolato correttamente. **Interazione:** highlight percentile X con coppia (quota popolazione, quota risorsa). **Fallback:** decili cumulativi in tabella. **Cautela:** Lorenz negativa/non-monotona possibile con patrimoni netti negativi; definire convenzioni e non disegnare curva standard in tali casi senza trattamento esplicito.

### V06 — TransitionMatrix / mobilità intergenerazionale

**Quando:** P(quintile figli = j | quintile genitori = i). **Input:** matrice 5×5, etichette di origine/destinazione, coorti, pesi. **Default:** righe origine I→V, colonne destinazione I→V, riga su 100, etichette percentuali nelle celle. **Interazione:** click cell → persona su 100 e descrizione; click riga → 100-dot strip coordinata; highlight diagonale opzionale con spiegazione. **Mobile:** 5×5 tappabile 44px; alternativa tabella. **Cautela:** ogni riga ≈100 (tolleranza arrotondamento) se probabilità condizionate; non sommare colonne per dedurre distribuzione nazionale se pesi d'origine non uguali.

### V07 — AlluvialFlow / traiettorie tra stati

**Quando:** racconto dei passaggi tra cinque origini e cinque destinazioni, senza perdere peso delle masse. **Input:** sorgente/destinazione/volume coerenti con denominatori e margini. **Default:** nodi verticali con totali e pochi flussi dominanti. **Interazione:** selezione origine o destinazione, altre correnti in secondo piano; tooltip e descrizione statici. **Fallback:** matrice 5×5. **Cautela:** non visualizzare percentuali di riga come spessori globali senza moltiplicarle per massa di origine; etichettare se dati campionari/condizionali.

### V08 — PercentileDots / 100 persone

**Quando:** frazioni semplici, es. 6 su 100. **Input:** numeratore, denominatore, unità e outcome. **Default:** griglia 10×10, ordine deterministico, selezionati fortemente marcati e rappresentazione numerica `6/100`. **Interazione:** selezione categoria aggiorna dot chart e copy; dots solitamente non interattivi individualmente. **Fallback:** cifra e tabella. **Cautela:** se probabilità 6,4% non far credere che siano esattamente 6 punti senza dichiarare arrotondamento.

### V09 — BeeswarmDot / distribuzione di individui

**Quando:** mostrare dispersione e outlier senza nascondere persone. **Input:** valori individuali (o campione esplicitato), peso, gruppo. **Default:** posizione per valore, jitter deterministico; annotare campionamento. **Interazione:** highlight percentile/cluster, search only if supported. **Fallback:** histogram + quantili in tabella. **Cautela:** non usare dots non ponderati con indagini pesate come se rappresentassero tutta la popolazione.

### V10 — HistDensity / distribuzione

**Quando:** concentrazione dei redditi, gap tra mediane e code. **Input:** bin, count o density, population, weights. **Default:** bin dichiarati, mediana marcata, valori non troncati. **Interazione:** scelta bins documentata / toggle log solo esplicito. **Fallback:** tabella dei bin. **Cautela:** valori negativi, top coding, e right skew da gestire; il cambio bin non deve fingere nuovi dati.

### V11 — ScatterRelationship / reddito × patrimonio

**Quando:** correlazione non causale di due variabili. **Input:** x, y, optional weights/region. **Default:** assi con unità; trasparenza per densità; annotation of outlier. **Interazione:** selezione gruppo e focus; regressione solo se supportata e metodologicamente valida. **Fallback:** dati aggregate o tabella punti. **Cautela:** dispersione reale e privacy per microdati; non inferire causalità.

### V12 — RegionChoropleth / geografie italiane

**Quando:** indicatore disponibile per tutte le aree. **Input:** codici territoriali, valore, anno, unità, geometrie con licenza. **Default:** scala continua dichiarata e n.d. hatch. **Interazione:** hover e click→dettaglio + confronto Italia; keyboard access via lista alternative. **Fallback:** classifica in tabella. **Cautela:** valori assoluti su mappe colorate non sono confrontabili senza denominatori; mappe non sono classifiche demografiche.

### V13 — SmallMultiples / tanti gruppi stessa scala

**Quando:** generazioni × livelli istruzione × regioni, o serie storiche comparate. **Input:** serie normalizzate con range condiviso. **Default:** scale identiche; ordine informativo. **Interazione:** highlight comune su tutti i pannelli. **Fallback:** tabella multi-chiave. **Cautela:** mai riadattare ogni asse senza segnalazione.

### V14 — StackedShare / composizione

**Quando:** quote per gruppo, se la somma è 100%. **Input:** unità additive e categorie esclusive. **Default:** stessi colori tra anni; legenda ordinata. **Interazione:** selezione componente; dettaglio absolute+relative. **Fallback:** tabella di quote. **Cautela:** cattivo per piccole differenze; considerare small multiples o linee.

### V15 — UncertaintyBand / stime e incertezza

**Quando:** grafici da survey/studi empirici con intervalli. **Input:** estimate, lower, upper, confidence level e metodo. **Default:** punto + interval, non solo fascia colorata. **Interazione:** switch intervallo soltanto se dati supportano più soglie, spiegazione sulla precisione. **Fallback:** tabella estimate/CI. **Cautela:** CI ≠ range individuale ≠ previsione; differenza di significatività non derivabile dal semplice overlap di CI.

### V16 — StoryStepper / narrative reveal

**Quando:** costruire comprensione progressiva intorno allo stesso chart. **Input:** array step con heading, annotation, state del grafico, fallback statico. **Default:** step 1 presenta unità e baseline; step N costruiscono messaggio per accumulazione. **Interazione:** scroll + Next/Previous + pulsante Esplora liberamente; `prefers-reduced-motion` disabilita la transizione. **Fallback:** grafici statici per step. **Cautela:** mobile sticky limitato, contenuto mai nascosto dietro animazione, niente scroll-jacking.

## 5. UX di un'interazione chart

**Default:** una domanda chiara e un insight dimostrabile. **Focus/hover:** highlight e tooltip leggibile. **Click/tap:** selezione persistente con insight testuale aggiornato. **Compare:** aggiungere seconda serie senza eliminare il contesto. **Reset:** recuperare stato editoriale. **Table:** stessi dati/stesso filtro. **Export:** CSV filtrato + metadata, SVG/PNG opzionali.

Da NON fare: richiedere hover per conoscere la scala; cambiare colore di significato da un grafico all'altro; «tutte le serie sempre in movimento»; suggerire causalità attraverso connettori (frecce) che rappresentano correlazioni.

## 6. API proposta (non libreria già implementata)

```ts
interface SchiaffoChartProps<Row> {
  meta: ChartMeta;
  data: Row[];
  state: VizState;
  onStateChange: (next: VizState) => void;
  formatValue: (value: number) => string;
  getCsvRows: (state: VizState) => Array<Record<string, string | number>>;
}
```

Il renderer può essere SVG/HTML per chart piccoli; per grandi volumi usare canvas con layer accessibile o librerie mature. Tenere stato dati separato dalla view. Consigli architetturali per Codex in `CODEX_PLAYBOOK.md`.
