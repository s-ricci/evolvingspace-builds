# EvolvingSpace — Game Design Document

> Documento vivo: aggiornare qui il design man mano che evolve.
> Le decisioni prese e quelle aperte sono in [decisioni.md](decisioni.md), lo stato dei lavori in [stato.md](stato.md), le idee proposte e il loro destino in [idee.md](idee.md).
> I materiali originali (docx, lore, immagini di riferimento) sono in `old/`.

## Visione

Gioco mobile 2D in **pixel art**, orientamento **portrait**, prospettiva **top-down**. Genere idle/incremental spaziale con progressione di crafting e potenziamento della nave.

- Engine: Unity 6.4 (6000.4.10f1)
- Riferimenti visivi: `old/img1.jpeg` (intro a fumetto), `old/img2.jpeg` (stazione Aeterna), `old/img3.jpeg` (mockup gameplay)

## Lore

Il protagonista viveva sulla **Aeterna**, un'enorme stazione spaziale piena di persone, equipaggio e la sua famiglia — centinaia di stanze. Una flotta di pirati spaziali attacca la stazione con missili e laser: esplosioni, fiamme, morti. Il protagonista scappa nell'hangar posteriore, trova un piccolo caccia in riparazione ma pronto a partire, e fugge. I pirati lo inseguono e lo colpiscono di striscio un paio di volte; per un colpo di fortuna finisce in un buco nero e riemerge in un nuovo universo, da solo, con il caccia mezzo distrutto.

**Da qui inizia il suo viaggio di vendetta.**

## Loop di gioco iniziale

### Schermata 1 — Mining

- Il caccia è in basso al centro dello schermo, punta verso l'alto, con una mini torretta laser visibile sopra di esso.
- La schermata è **dinamica** (direttiva 13 del 23/07/2026): le stelle pulsano e scorrono verso il basso — l'effetto è che la nave stia avanzando. Gli **asteroidi compaiono random dalla cima della mappa** (uno ogni 3,5–6,5 s, max 5 in campo), scendono lenti e costanti (0,4 u/s) con una piccola deriva laterale e, se nessuno li distrugge, **scompaiono in fondo** — ferro perso.
- **Tap su un asteroide** → dalla torretta parte una **laserata continua** che aggancia il bersaglio e lo segue mentre scende; il danno arriva spalmato in 5 tocchi regolari (numeri, scintille e vibrazione per tutta la durata), col ronzio del laser che dura quanto il fascio.
- **Il laser ha una cadenza sola**, valida per il tap e per l'IA (decisione del 24/07 dopo la v0.9): fascio **1,00 s → 0,55 s** con i livelli di "velocità di fuoco", più 0,12 s di respiro. Il **tap ha la prelazione** — spara appena il laser è carico e, se il fascio è già acceso, lo **ridirige** sul nuovo bersaglio, che diventa anche il bersaglio dell'IA; l'**IA** invece attende **0,4 s di tempo di reazione** da quando il laser è pronto. Così il giocatore attivo è sempre avanti di un'incollatura, senza avere due orologi diversi.
- HP asteroide: **15**, danno base **5 per laserata** → servono 3 laserate per distruggerlo.
- Alla distruzione: piccola animazione esplosiva, l'asteroide scompare e droppa **3 minerali di ferro grezzo**, piccoli, che "scappano" dall'esplosione con una spinta subito smorzata (il raggio traente riesce a riprenderli).
- La **torretta automatica** aggancia solo asteroidi interamente dentro l'area di gioco, sotto un margine d'ingresso in alto, e dà priorità a quello più in basso (che sta per sfuggire); il tap manuale è libero.
- **Trascina col dito** i minerali verso il caccia: quando sono abbastanza vicini scompaiono e appare un "+1" che svanisce rapidamente (feedback di raccolta). Il grezzo finisce nel **deposito del raggio traente** (limite in peso): a deposito pieno non si raccoglie finché non si fonde.
- In basso: pulsante per passare alla schermata dell'interno nave.

> ✅ **Deciso il 22/07/2026** (vedi [decisioni.md](decisioni.md)): meccanica tap + trascina (opzione 2a); le azioni manuali (tap, trascina) sono pensate per diventare upgrade automatici (torretta automatica, raggio traente — entrambi implementati). Le ondate statiche dell'opzione 1a sono state **superate il 23/07/2026** dalla schermata dinamica (direttiva 13): resta da aggiungere, per la 1b completa, la barra velocità legata al viaggio.

### Schermata 2 — Interno nave (a lista di moduli, direttiva 14 del 23/07/2026)

L'interno è una **lista dei moduli costruiti** (overlay opaco: il mining continua a girare sotto — segnalazione 15). Tap su un modulo → il suo pannello di dettaglio/upgrade. In fondo alla lista il tasto **"COSTRUISCI MODULO"**, che apre l'elenco dei moduli nuovi; **ogni modulo ha un suo tempo di costruzione** e mentre è in cantiere la sua riga mostra il conto alla rovescia.

| Modulo | Di default? | Cosa fa / upgrade |
|---|---|---|
| Sala comandi | sì | Il ponte. Nessun upgrade, per ora |
| Magazzino | sì | Stiva i lingotti (senza limite) e il **minerale grezzo** (limite in unità di carico, 10 livelli — il deposito non è più del raggio traente) |
| Motore a impulso | sì | 10 livelli: più velocità di avanzamento ⇒ **più asteroidi entrano in mappa** (e rotte più rapide) |
| Laser minerario | sì | Upgrade velocità e danno (10 livelli l'uno) + **IA a livelli per minerale** (Lv 1 ferro, Lv 2 rame) |
| Fonderia | costruibile (**30 ferro grezzo** · 30 s) | 3 grezzo → 1 lingotto. **I livelli sono i minerali fondibili**: Lv 1 ferro (la costruzione stessa), Lv 2 rame (**60 rame grezzo** · 60 s), e un livello per ogni metallo futuro pagato nel suo grezzo. Tap sulla riga → schermata crafting |
| Raggio traente | costruibile (4 lingotti · 20 s) | Traina i minerali a bordo (velocità = forza / massa); upgrade forza di trazione (10 livelli); **Mk II: secondo fascio**. In rotta si spegne |
| Sala mappe | costruibile (8 lingotti · 30 s), sbloccata dalla prima visita alla stazione | Rivela la composizione dei campi non visitati e i nomi dei sistemi mai raggiunti |
| Sala comunicazioni | costruibile (10 lingotti · 60 s), sbloccata dalla prima visita alla stazione | Missioni in corso e avanzamento; **consegna a distanza** (senza, si consegna solo ad Argo) e incarichi via radio dai mercantili. Livelli: **slot missioni** 2 → 5 e **ricompense +3% per livello** |

### Schermata 3 — Crafting (fonderia)

Click sulla fonderia → schermata di crafting. Una ricetta per riga, disposta in orizzontale:

```
3 × [minerale ferro grezzo]  →  1 × [lingotto di ferro]     [CRAFT]
3 × [minerale rame grezzo]   →  1 × [lingotto di rame]      [serve fonderia Lv 2]
```

- Freccia rossa come indicatore di conversione, pulsante rosso "Craft" a destra; sotto, "PRODUCI TUTTI" converte in un colpo tutto il grezzo fondibile.
- Premendo "Craft" si consumano 3 minerali grezzi e si produce 1 lingotto.
- **Appena un minerale viene scoperto la sua ricetta compare nella lista**, anche se la fonderia non è ancora al livello che serve: la riga resta spenta e dice quale livello serve. Il prossimo obiettivo è visibile, non nascosto.

## Progressione e potenziamenti

Catena decisa il 22/07/2026 e rivista il 23/07/2026 sera con la **direttiva 12** — via il sistema energetico, dentro il deposito a peso (dettagli e motivazioni in [decisioni.md](decisioni.md)):

```
tap manuale (gratis, sempre)
  → "IA" del laser minerario (5 lingotti): spara da solo, con la
    STESSA CADENZA del tap (fascio + 0,12 s) più 0,4 s di reazione
    — il tap ha la prelazione e le indica il bersaglio             [implementata]
  → raggio traente (modulo: 4 lingotti · 20 s): aggancia 1
    minerale alla volta; forza di trazione Lv 1–10 = 0,8→2,8 u/s;
    in futuro i materiali pesanti richiederanno più forza        [implementato]
  → magazzino: il grezzo occupa unità di carico, la capacità
    è l'upgrade del modulo (1000→3500)                           [implementato]
  → motore a impulso Lv 1–10: più velocità ⇒ più asteroidi
    in mappa (primo assaggio della velocità di viaggio)          [implementato]
  → mining offline: si ferma a magazzino pieno, tetto 24 h       [implementato]
  → viaggio (evoluzione 1b): mappa stellare, campi di asteroidi
    con tabelle di spawn, rotte a tempo reale (mining in rotta)   [implementato, v0.7]
  → mercantili in rotta → Cookie (il primo rivela la stazione)    [implementato, v0.7]
  → stazione ARGO: missioni → Cookie → tier Mk II all'hangar
    + rame (il suo campo si attiva) + sala mappe                  [implementato, v0.7]
```

### Struttura della progressione a lungo termine (decisa il 24/07/2026)

Quattro piani, dal piccolo al grande (motivazioni in [decisioni.md](decisioni.md)):

| Piano | Cos'è | Scala | Esempio |
|---|---|---|---|
| **Livelli (1–5)** | crescita quotidiana a bordo, per linea di upgrade | minuti/ore | Danni del laser Lv 3 |
| **Tier "Mk"** | il gradino grosso di un modulo, all'**hangar** delle stazioni, sbloccato dalle missioni Cookie; il conteggio resta basso perché gated dalle stazioni raggiunte | giorni | Laser minerario **Mk II** (rompe il rame, riapre i livelli 1–5) |
| **Milestone nominate** | eventi unici della mappa, mai numerati; ognuna sblocca contenuto (minerali, moduli, meccaniche) e prende il nome dalla storia | settimane | l'arrivo alla prima stazione |
| **Universi** | i capitoli massimi (l'universo 1 è quello attuale); eventuale sede di un futuro prestige | mesi | universo 2, oltre un nuovo buco nero |

Regole di pacing: non si allunga un capitolo gonfiando i numeri (il capitolo 1 da ~24 h va bene così); ogni capitolo punta a durare 2-3 volte il precedente.

### Campi di asteroidi e viaggio (decisi il 24/07/2026)

- L'universo è fatto di **campi di asteroidi discreti** sulla mappa stellare, ognuno con la sua **tabella di spawn** (es. campo di partenza 100% ferro; campo del rame 65% ferro / 35% rame; campo del silicio 40/40/20; rare "vene pure" come punti di interesse speciali). Il minerale vecchio non sparisce mai del tutto (serve alle ricette).
- **Doppio gating** dei minerali nuovi: il **tier del laser** decide quali asteroidi si rompono, la **forza/massa del raggio traente** quali minerali si trainano. Mai asteroidi rompibili con drop non raccoglibili: se un campo è troppo avanzato, è il laser a non scalfire.
- **Viaggio a tempo reale**: si sceglie il punto di interesse sulla mappa, durata = distanza / velocità (livello del motore a impulso). In rotta il mining continua con la tabella **"spazio aperto"** (spawn rarefatto e povero); all'arrivo scatta la tabella del campo. Il viaggio avanza anche **offline**. Nessun costo in carburante. I **mercantili** sono incontri casuali in rotta.
- La **sala mappe** (costruibile dopo la prima visita alla stazione) rivela la composizione dei campi non visitati; senza, i loro pannelli mostrano "???".
### La mappa è un grafo di sistemi solari (idea 38, implementata nella v0.10)

- I **sistemi solari** sono i **nodi**, le tratte percorribili sono gli **archi**: si viaggia da sistema a sistema, anche a **più salti** (percorso minimo calcolato sul grafo), e ogni sistema contiene i propri **campi di asteroidi e stazioni**. La distanza si legge come **numero di salti**, non come pixel.
- L'universo 1 ha **5 sistemi** e **7 tratte**:

| Sistema | Contiene | Note |
|---|---|---|
| **EOS** | Campo di partenza · Cintura densa | il sistema di casa |
| **VESTA** | Vena ricca · Anello esterno | rocce grosse e vecchie |
| **ARGO** | Stazione Argo · Campo del rame | la milestone: hangar, missioni, primo rame |
| **KORAX** | Vena mista | metà ferro, metà rame |
| **THULE** | Filone di rame | il più lontano |

- I **mercanti** non sono più incontri casuali: **cinque navi percorrono i loro archi** avanti e indietro con moto **deterministico** (funzione dell'orologio UTC: si muovono anche a gioco chiuso, senza salvare nulla) e si **intercettano** passando entro 3,5 UA. Si vedono muoversi sulla mappa dopo il primo incontro: deviare diventa una **decisione informata**. Il primo mercantile resta garantito a metà della prima rotta — deve rivelare Argo.
- Le rotte multi-salto **non prevedono attracchi intermedi**: si attraversa e basta, l'arrivo è uno solo (anche offline). I **gate di teletrasporto** delle stazioni-checkpoint future saranno archi speciali.
- La **sala mappe** svela la composizione dei campi e i nomi dei sistemi non ancora visitati.

### Numeri correnti (v0.10 del 25/07/2026, da validare col playtest)

**Campi dell'universo 1** (velocità di crociera: 3 UA/min, +20% per livello del motore a impulso; **in rotta non si mina**: alla partenza la nave **scatta in avanti**, il **laser smette di sparare** e il **raggio traente si spegne**, gli asteroidi e i minerali non raccolti restano indietro, la nave va verso il centro della visuale a motori spinti e stelle e mondo accelerano **×8** con le stelle in scia. Partire è una scelta: adesso, o dopo aver ripulito il campo):

| Campo | Sistema | Composizione | Densità (spawn) | Asteroidi densi | Note |
|---|---|---|---|---|---|
| Campo di partenza | EOS | 100% ferro | 1,0 (bassa) | — | casa |
| Cintura densa | EOS | 100% ferro | 1,25 (alta) | **35%** | stesso sistema: ci si sposta in meno di un minuto |
| Vena ricca | VESTA | 100% ferro | 0,8 | — | asteroidi grossi: scala 1,25–1,6, **HP ×2, drop ×2** |
| Anello esterno | VESTA | 100% ferro | 1,1 | 25% | il secondo campo di Vesta |
| Stazione Argo | ARGO | 100% ferro | 0,45 | — | nascosta ("???") finché un mercantile non la rivela |
| Campo del rame | ARGO | 65% ferro / 35% rame | 1,05 | 20% (sul ferro) | si attiva alla prima visita alla stazione |
| Vena mista | KORAX | 50% ferro / 50% rame | 1,0 | 20% (sul ferro) | oltre il campo del rame |
| Filone di rame | THULE | 20% ferro / 80% rame | 0,9 | 15% (sul ferro) | il più lontano; senza laser Mk II è inutilizzabile da sé |

**Asteroide denso** (fix post-v0.7): roccia scura compatta, **45 HP**, droppa **5 minerali**, scala +15%; la **corazza chiede ≥ 9 danni per colpo** (≈ danni Lv 5 su 10 al Mk I) — col laser al minimo non si scalfisce. La densità alta di un campo è nel mix di densi, non solo nello spawn: lo schermo non si affolla.

**Rame**: asteroide da **80 HP** (vs 15 del ferro), si rompe solo col **laser Mk II** e l'IA lo bersaglia solo da **IA Lv 2**; il grezzo pesa **2 unità** e il raggio lo traina a metà velocità; 3 rame → 1 lingotto di rame in fonderia (serve la **fonderia Lv 2**: 60 rame grezzo, per forza minati a mano).

**Mercantili** (dalla v0.10 vivono sul grafo, vedi sopra): si **incrociano** lungo la rotta quando la nave passa entro **3,5 UA** da uno di loro — primo garantito (va abbordato per rivelare Argo, e aspetta fino all'arrivo), gli altri restano a tiro **30 secondi**. Banner "Mercantile in avvicinamento…": il tap devia verso di lui e **mette in pausa il timer di viaggio**, che riprende alla chiusura del banco. **Non comprano più lingotti** (il canale lingotti → Cookie è chiuso): al banco si **compra** ferro grezzo (2-3 per 1 Cookie) e lingotti di rame (12-15 Cookie l'uno, poche decine per incontro), e ogni mercantile abbordato **offre un incarico**. **Alla stazione solo baratto VERSO IL BASSO** (niente Cookie, mai verso l'alto): 1 rame → 2 ferro; ad Argo si comprano lingotti di rame a **10 Cookie** in quantità.

**Missioni** (ripetibili e randomizzate, decisione del 24/07 dopo la v0.9). Ogni missione vale un numero di **equivalenti asteroide (eq)** e paga **0,75-1,00 Cookie per eq**, con **tetto di 200 Cookie**:

| Cosa | Vale | | Archetipo | Richiesta | Ricompensa |
|---|---|---|---|---|---|
| asteroide di ferro · lingotto di ferro | 1 eq | | consegna lingotti di ferro | 60-200 (×10) | 60-200 |
| asteroide denso | 3 eq | | consegna lingotti di rame | 10-40 (×10) | 50-200 |
| asteroide di rame · lingotto di rame | 5 eq | | abbatti asteroidi in un campo | 30-200 (×5) | 30-170 |
| 30 unità di grezzo | 10 eq | | raccogli grezzo in un campo | 90-600 (×30) | 30-170 |

Tasso **0,85-1,00** per le missioni che consumano materiale (consegne), **0,75-0,90** per quelle che non consumano nulla; ricompensa arrotondata alle decine. **Rigenerazione**: la bacheca di Argo si rinnova **a ogni attracco**, ogni mercantile abbordato offre un incarico; **2 missioni attive** alla volta (fino a 5 con la Sala comunicazioni). Nessun limite giornaliero: il freno è il viaggio.

**Hangar — evoluzioni Mk II** (la base Mk II = il massimo del Mk I, i livelli 1–10 si riaprono e si pagano in lingotti di rame):

| Modulo | Costo | Effetto Mk II (Lv 1 → 10) |
|---|---|---|
| Laser minerario | **500 Cookie** | rompe il rame; danni 15 → 30, fascio 0,55 → 0,35 s |
| Raggio traente | **500 Cookie** | **secondo fascio**; trazione 2,8 → 4,8 u/s |
| Magazzino | **500 Cookie** | capacità 3500 → 12000 unità |

*(500 Cookie è il prezzo del **primo** salto di tier; quelli successivi si decideranno quando arriveranno.)*

**Offline**: la rotta avanza a gioco chiuso (arrivo compreso, stazione inclusa) ma **in rotta non si mina nemmeno offline**; il mining offline usa la tabella del campo di arrivo/corrente, e densi e rame contano solo se il laser li rompe (il rame anche solo con **IA Lv 2**). Restano il tetto delle 24 h e lo stop a magazzino pieno.

**Salvataggio v8** (migrazione automatica): la calibrazione della fonderia diventa il **livello** del modulo (fonderia costruita ⇒ almeno Lv 1); le missioni della vecchia catena fissa decadono e la bacheca si rigenera al primo attracco; la sala comunicazioni entra nel salvataggio; il sistema si deduce dal campo, quindi la mappa a grafo non richiede conversioni. *(v7: livelli 0-5 → 1-10 in proporzione, IA installata → IA Lv 1.)*

### Deposito a unità e upgrade del tier 1 (direttive 12 e 14 del 23/07/2026, implementati)

Il **minerale grezzo** vive nel **magazzino**, con un limite misurato in **unità di carico** (il ferro vale 1; i minerali futuri ne varranno sempre di più); a magazzino pieno si fonde per liberare spazio. I **lingotti** non hanno limite. *(Il deposito era nato "dentro" il raggio traente con la direttiva 12; con la 14 è passato al magazzino. Energia, deuterio e reattore restano ritirati — storia completa in decisioni.md.)*

Gli upgrade vivono **nel pannello del proprio modulo**. Dal ribilanciamento del 24/07 (v0.9) i livelli vanno da **1 a 10** (9 acquisti, si parte da Lv 1) con **costi esponenziali ~×1,6 per livello**: è lo scarto tra costi esponenziali e rendita lineare a creare i muri naturali del genere, senza limiti giornalieri. Al Mk II le stesse tabelle si pagano in **lingotti di rame**.

| Modulo | Upgrade | Effetto Lv 1 → 10 (Mk I) | Costi per livello (lingotti) |
|---|---|---|---|
| Laser minerario | Velocità di fuoco (tap **e** IA) | fascio 1,00 s → 0,55 s (cadenza ~1,12 → ~0,67 s) | 3, 5, 8, 12, 20, 30, 50, 80, 125 |
| Laser minerario | Danni | 5 → 15 per laserata (vale anche per il tap) | 3, 5, 8, 12, 20, 30, 50, 80, 125 |
| Laser minerario | IA di mira (per minerale) | Lv 1 riconosce il ferro · Lv 2 il rame | 5 lingotti ferro · 10 lingotti rame |
| Magazzino | Capacità grezzo | 1000 → 3500 unità | 4, 7, 11, 16, 27, 40, 67, 105, 165 |
| Motore a impulso | Velocità | asteroidi ×1 → ×2,5 · crociera ×1 → ×2 | 4, 7, 11, 16, 27, 40, 67, 105, 165 |
| Raggio traente | Forza di trazione | 0,8 → 2,8 u/s | 4, 7, 11, 16, 27, 40, 67, 105, 165 |
| Sala comunicazioni | Rete di bordo | slot missioni 2 → 5 · ricompense +3%/livello | 4, 7, 11, 16, 27, 40, 67, 105, 165 |
| Fonderia | Livello = minerale fondibile (in GREZZO) | Lv 1 ferro (= costruzione) · Lv 2 rame | 30 ferro grezzo · 60 rame grezzo |

**Il ciclo "a mano prima, automatico poi" vale per ogni minerale** (decisione 24/07 notte): nuovo minerale → lo mini a mano (gate del laser Mk) → porti la fonderia al livello di quel minerale pagando **nel suo grezzo** → fondi i primi lingotti → insegni all'IA a riconoscerlo pagando in quei lingotti → automazione completa. Senza IA Lv 2 la torretta ignora il rame anche col laser Mk II (il tap resta libero: anti-softlock).

Altri numeri della prima implementazione (da validare col playtest):

| Cosa | Valore |
|---|---|
| Carico del ferro grezzo | 1 unità per minerale (3 unità di ferro → 1 lingotto) |
| Mining offline | richiede IA del laser + raggio traente costruito; resa = collo di bottiglia reale tra spawn (accelerato dal motore), abbattimento e traino sequenziale; si ferma a **magazzino pieno**; tetto **24 h** (idea 1) |
| Schermata mining dinamica | spawn ogni 3,5–6,5 s a motore base (÷ fattore motore; max in campo 5+livello motore); discesa 0,4 u/s × fattore mondo; margine d'ingresso del laser IA 0,5 u |
| Mining sempre attivo | la schermata mining non si ferma nei menù: interno e fonderia sono overlay (segnalazione 15) |
| Migrazione salvataggi | v2/v3: i campi energetici decadono; v4: "raggio installato" ⇒ modulo raggio costruito; il resto sopravvive sempre |

Regole fisse: il gioco attivo batte sempre il passivo; il mining manuale (tap e trascina) è sempre gratuito e disponibile (anti-softlock); a magazzino pieno si fonde per liberare spazio. Ogni upgrade si compra **nel pannello del suo modulo** (direttiva 14: la sala comandi è il ponte, non il negozio).

## Visione a medio-lungo termine

Idee valutate e messe in roadmap il 23/07/2026 (dettagli in [idee.md](idee.md), ordine di lavoro in [stato.md](stato.md)):

1. ~~**Fonderia comoda**~~ ✓ (23/07) — X di chiusura e "produci tutti"; il "Crafta tutto" globale arriverà con più materiali
2. ~~**Deposito a peso + schermata mining dinamica**~~ ✓ (23/07 sera, direttive 12-13) — hanno sostituito il sistema energetico della v0.3, ritirato dopo il playtest
3. ~~**Mappa stellare + viaggio (1b completa)**~~ ✓ (24/07, v0.7) — mappa dell'universo 1 coi campi, rotte a tempo reale, mining in rotta, viaggio offline
4. ~~**Mercantili e "Cookie"**~~ ✓ (24/07, v0.7) — incontri casuali in rotta a prezzi variabili; il primo rivela la Stazione Argo
5. ~~**Prima stazione spaziale**~~ ✓ (24/07, v0.7) — la **Stazione Argo**: missioni → Cookie → evoluzioni Mk II all'hangar, campo del rame attivato, ricetta del rame in fonderia, commercio, sala mappe costruibile, raggio traente Mk II = secondo fascio
6. ~~**Ribilanciamento del pacing**~~ ✓ (24/07, v0.9) — livelli 1-10 esponenziali, IA e fonderia per minerale, baratto solo verso il basso
7. ~~**Rifiniture e fix**~~ ✓ (25/07, v0.10) — cadenza unica del laser, tap che punta anche l'IA, partenza a razzo, fonderia a livelli, barra risorse senza lingotti, mercante con quantità rapide, impostazioni, zoom sulla mappa
8. ~~**Riassetto dell'economia**~~ ✓ (25/07, v0.10) — missioni ripetibili e randomizzate, Sala comunicazioni, 500 Cookie il primo tier, fine della vendita dei lingotti, acquisto lingotti coi Cookie, Vena mista e Filone di rame
9. ~~**Mappa stellare a sistemi solari**~~ ✓ (25/07, v0.10) — 5 sistemi come nodi, 7 tratte come archi, viaggio multi-salto, campi e stazioni dentro i sistemi; i **mercanti percorrono rotte visibili** e si intercettano (moto deterministico, anche a gioco chiuso)
10. ~~**Changelog e strumenti di sviluppo**~~ ✓ (25/07, v0.10) — tasto **CHANGELOG** nelle impostazioni (ultime 10 versioni, testo generato a ogni build da `changelog.md`), pulsante **SVILUPPATORE** separato con le risorse di test, e il **"»" verde** dentro le schede di viaggio e costruzione per chiudere subito l'attesa
11. **Prossimo**: silicio, titanio, alluminio coi Mk successivi (pesi crescenti); altre stazioni come checkpoint con gate di teletrasporto (che nel grafo sono archi speciali) e milestone successive; intro a fumetto; eventuale ritorno di reattore/energia (e del "quadro elettrico") se il design lo richiederà; l'universo 2 come capitolo massimo
12. **Milestone "Relitti"** (idea 19) — relitti di stazione esplorabili: minigioco a stanze procedurali, HP della tuta, oggetti equipaggiabili, oggetti come ricompensa delle missioni. **In fondo alla roadmap**, dopo tutto il resto

## UI generale

- Barra risorse in alto: **Cookie** a sinistra (compaiono quando la valuta entra in gioco) e **CARICO peso/max** (cassa) a destra, arancio a deposito pieno. **I lingotti non stanno più nella barra**: il totale si legge dove si spende (pannelli dei moduli, hangar, fonderia), perché il carico è l'unico numero su cui si agisce mentre si mina.
- In basso nella schermata mining: quadrato **mappa stellare** a sinistra, **INTERNO NAVE** al centro, quadrato **impostazioni** a destra (mockup 8). In rotta compare il **banner di viaggio** sotto la barra risorse.
- Intro narrativa a fumetto prima del gameplay (vedi `old/img1.jpeg`), che termina con "[ INIZIA IL VIAGGIO ]".
- La mappa della stazione Aeterna (`old/img2.jpeg`) è il riferimento del "prima" — utile per l'intro ed eventualmente come visione a lungo termine della progressione.
