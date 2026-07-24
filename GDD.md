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
- **Tap su un asteroide** → dalla torretta parte una **laserata continua di 1 secondo** che aggancia il bersaglio e lo segue mentre scende; il danno arriva spalmato in 5 tocchi regolari (numeri, scintille e vibrazione per tutta la durata), col ronzio del laser che dura quanto il fascio. Durante la laserata la torretta è occupata: altri tap vengono ignorati.
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
| Magazzino | sì | Stiva i lingotti (senza limite) e il **minerale grezzo** (limite in unità di carico, 5 livelli — il deposito non è più del raggio traente) |
| Motore a impulso | sì | 5 livelli: più velocità di avanzamento ⇒ **più asteroidi entrano in mappa** |
| Laser minerario | sì | Upgrade velocità e danno (5 livelli l'uno) + **1 livello di "IA"** che lo automatizza (ex torretta automatica) |
| Fonderia | costruibile (gratis · 30 s) | 3 grezzo → 1 lingotto (ferro e, dalla v0.7, rame); nessun upgrade. Tap sulla riga → schermata crafting |
| Raggio traente | costruibile (4 lingotti · 20 s) | Traina i minerali a bordo (velocità = forza / massa); upgrade forza di trazione (5 livelli); **Mk II: secondo fascio** |
| Sala mappe | costruibile (8 lingotti · 30 s), sbloccata dalla prima visita alla stazione | Rivela la composizione dei campi non visitati sulla mappa stellare |

### Schermata 3 — Crafting (fonderia)

Click sulla fonderia riparata → schermata di crafting. Ricetta disposta in orizzontale:

```
3 × [minerale ferro grezzo]  →  1 × [lingotto di ferro]     [CRAFT]
```

- Freccia rossa come indicatore di conversione, pulsante rosso "Craft" a destra.
- Premendo "Craft" si consumano 3 minerali grezzi e si produce 1 lingotto.

## Progressione e potenziamenti

Catena decisa il 22/07/2026 e rivista il 23/07/2026 sera con la **direttiva 12** — via il sistema energetico, dentro il deposito a peso (dettagli e motivazioni in [decisioni.md](decisioni.md)):

```
tap manuale (gratis, sempre)
  → "IA" del laser minerario (5 lingotti): spara da solo,
    1 laserata/2,5 s; upgrade velocità e danni (Lv 0–5)          [implementata]
  → raggio traente (modulo: 4 lingotti · 20 s): aggancia 1
    minerale alla volta; forza di trazione Lv 0–5 = 0,8→2,8 u/s;
    in futuro i materiali pesanti richiederanno più forza        [implementato]
  → magazzino: il grezzo occupa unità di carico, la capacità
    è l'upgrade del modulo (1000→3500)                           [implementato]
  → motore a impulso Lv 0–5: più velocità ⇒ più asteroidi
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

### Numeri della v0.7 (implementati il 24/07/2026, da validare col playtest)

**Campi dell'universo 1** (velocità di crociera: 3 UA/min, +20% per livello del motore a impulso; **in rotta non si mina**: niente spawn, laser a riposo, nave verso il centro della visuale a motori spinti, stelle e mondo accelerati ×4-5):

| Campo | Composizione | Densità (spawn) | Asteroidi densi | Note |
|---|---|---|---|---|
| Campo di partenza | 100% ferro | 1,0 (bassa) | — | casa |
| Cintura densa | 100% ferro | 1,25 (alta) | **35%** | ~12 UA dalla partenza (4 min a motore base) |
| Vena ricca | 100% ferro | 0,8 | — | asteroidi grossi: scala 1,25–1,6, **HP ×2, drop ×2** |
| Stazione Argo | 100% ferro | 0,45 | — | nascosta ("???") finché un mercantile non la rivela |
| Campo del rame | 65% ferro / 35% rame | 1,05 | 20% (sul ferro) | si attiva alla prima visita alla stazione |

**Asteroide denso** (fix post-v0.7): roccia scura compatta, **45 HP**, droppa **5 minerali**, scala +15%; la **corazza chiede ≥ 9 danni per colpo** (danni Lv 2) — col laser al minimo non si scalfisce. La densità alta di un campo è nel mix di densi, non solo nello spawn: lo schermo non si affolla.

**Rame**: asteroide da **40 HP** (vs 15 del ferro), si rompe solo col **laser Mk II**; il grezzo pesa **2 unità** e il raggio lo traina a metà velocità; 3 rame → 1 lingotto di rame in fonderia.

**Mercantili** (fix post-v0.7): si **avvistano** in rotta — primo garantito (va abbordato per rivelare Argo, e aspetta fino all'arrivo), poi **rari** (~1 rotta su 5, finestra di 30 s tra il 35% e il 70% del tragitto). Banner "Mercantile in avvicinamento…": il tap devia verso di lui e **mette in pausa il timer di viaggio**, che riprende alla chiusura del banco. Prezzi casuali: vende lingotti di ferro a 2-4 Cookie, di rame a 5-8; compra ferro grezzo a 2-3 per 1 Cookie. **Alla stazione solo baratto tra lingotti** (niente Cookie, tassi peggiori): 4 ferro → 1 rame · 1 rame → 2 ferro.

**Missioni di Argo** (2 attive alla volta, in catena): Consegna di ferro (50 lingotti → 20 Cookie) · Sciame in arrivo (30 asteroidi nella Cintura Densa → 15) · Rotta commerciale (vendi 20 lingotti ai mercantili → 15) · Spedizione mineraria (60 ferro nella Vena Ricca → 20).

**Hangar — evoluzioni Mk II** (in Cookie; la base Mk II = il massimo del Mk I, i livelli 1–5 si riaprono e si pagano in lingotti di rame):

| Modulo | Costo | Effetto Mk II (Lv 0 → 5) |
|---|---|---|
| Laser minerario | 40 Cookie | rompe il rame; danni 15 → 30, velocità 1,25 → 0,75 s |
| Raggio traente | 35 Cookie | **secondo fascio**; trazione 2,8 → 4,8 u/s |
| Magazzino | 30 Cookie | capacità 3500 → 12000 unità |

**Offline**: la rotta avanza a gioco chiuso (arrivo compreso, stazione inclusa) ma **in rotta non si mina nemmeno offline**; il mining offline usa la tabella del campo di arrivo/corrente, e densi e rame contano solo se il laser li rompe. Restano il tetto delle 24 h e lo stop a magazzino pieno.

### Deposito a unità e upgrade del tier 1 (direttive 12 e 14 del 23/07/2026, implementati)

Il **minerale grezzo** vive nel **magazzino**, con un limite misurato in **unità di carico** (il ferro vale 1; i minerali futuri ne varranno sempre di più); a magazzino pieno si fonde per liberare spazio. I **lingotti** non hanno limite. *(Il deposito era nato "dentro" il raggio traente con la direttiva 12; con la 14 è passato al magazzino. Energia, deuterio e reattore restano ritirati — storia completa in decisioni.md.)*

Gli upgrade vivono **nel pannello del proprio modulo**, tutti cappati a **Lv 5 = tier 1** (i tier successivi arriveranno con le missioni "Cookie" della stazione spaziale):

| Modulo | Upgrade | Effetto Lv 0 → 5 | Costi (lingotti/livello) |
|---|---|---|---|
| Laser minerario | Velocità di fuoco (IA) | 1 laserata/2,5 s → 1/1,25 s | 3, 6, 9, 12, 15 |
| Laser minerario | Danni | 5 → 15 per laserata (vale anche per il tap) | 3, 6, 9, 12, 15 |
| Laser minerario | IA di mira (1 livello) | il laser spara da solo | 5 |
| Magazzino | Capacità grezzo | 1000 → 3500 unità | 4, 8, 12, 16, 20 |
| Motore a impulso | Velocità | asteroidi in mappa ×1 → ×2,5 (mondo +40%) | 5, 10, 15, 20, 25 |
| Raggio traente | Forza di trazione | 0,8 → 2,8 u/s | 4, 8, 12, 16, 20 |

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
6. Più avanti: silicio, titanio, alluminio coi Mk successivi (pesi crescenti); altre stazioni come checkpoint con gate di teletrasporto e milestone successive; intro a fumetto; eventuale ritorno di reattore/energia (e del "quadro elettrico") se il design lo richiederà; l'universo 2 come capitolo massimo

## UI generale

- Barra risorse in alto: **lingotti** (icona) a sinistra, **Cookie** al centro (compaiono quando la valuta entra in gioco), **CARICO peso/max** (cassa) a destra, arancio a deposito pieno (mockup 5 e 10).
- In basso nella schermata mining: quadrato **mappa stellare** a sinistra, **INTERNO NAVE** al centro, quadrato **impostazioni** a destra (mockup 8). In rotta compare il **banner di viaggio** sotto la barra risorse.
- Intro narrativa a fumetto prima del gameplay (vedi `old/img1.jpeg`), che termina con "[ INIZIA IL VIAGGIO ]".
- La mappa della stazione Aeterna (`old/img2.jpeg`) è il riferimento del "prima" — utile per l'intro ed eventualmente come visione a lungo termine della progressione.
