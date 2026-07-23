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
- **Tap su un asteroide** → dalla torretta parte un raggio laser rapido verso il bersaglio → sopra l'asteroide compare il numero del danno ("5").
- HP asteroide: **15** → servono 3 colpi da 5 per distruggerlo.
- Alla distruzione: piccola animazione esplosiva, l'asteroide scompare e droppa **3 minerali di ferro grezzo**, piccoli, che "scappano" dall'esplosione con una spinta subito smorzata (il raggio traente riesce a riprenderli).
- La **torretta automatica** aggancia solo asteroidi interamente dentro l'area di gioco, sotto un margine d'ingresso in alto, e dà priorità a quello più in basso (che sta per sfuggire); il tap manuale è libero.
- **Trascina col dito** i minerali verso il caccia: quando sono abbastanza vicini scompaiono e appare un "+1" che svanisce rapidamente (feedback di raccolta). Il grezzo finisce nel **deposito del raggio traente** (limite in peso): a deposito pieno non si raccoglie finché non si fonde.
- In basso: pulsante per passare alla schermata dell'interno nave.

> ✅ **Deciso il 22/07/2026** (vedi [decisioni.md](decisioni.md)): meccanica tap + trascina (opzione 2a); le azioni manuali (tap, trascina) sono pensate per diventare upgrade automatici (torretta automatica, raggio traente — entrambi implementati). Le ondate statiche dell'opzione 1a sono state **superate il 23/07/2026** dalla schermata dinamica (direttiva 13): resta da aggiungere, per la 1b completa, la barra velocità legata al viaggio.

### Schermata 2 — Interno nave

Piantina del caccia vista dall'alto, poche stanze:

| Stanza | Stato iniziale |
|---|---|
| Sala comandi | Poltrona con il personaggio seduto; pannello con le 4 linee di upgrade |
| Magazzino | Mezzo spoglio, un paio di pallet vuoti |
| Fonderia | Rotta, malandata, al centro della stanza |
| Reattore | Spento — tornerà in una fase futura (direttiva 12 del 23/07/2026) |

- **Click sulla fonderia rotta** → pop-up che spiega che è rotta; pulsante verde **"Costruisci"** con indicazione "(30 secondi)".
- Premendo "Costruisci" parte una barra che si riempie in 30 secondi; al termine la stanza si trasforma: resta trasandata ma la fonderia diventa utilizzabile.
- Il **magazzino non ha limite ma non contiene minerale grezzo**: sui pallet si vedono i **lingotti**. Il grezzo resta nel deposito del raggio traente (limite in peso).

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
  → torretta automatica lenta (5 lingotti, 1 colpo/2,5 s)        [implementata]
  → upgrade torretta: velocità di fuoco, danni (Lv 0–5)          [implementati]
  → raggio traente visibile: fascio che aggancia 1 minerale
    alla volta; forza di trazione Lv 0–5 = velocità di traino
    0,8→2,8 u/s (4-20 lingotti/livello); in futuro i materiali
    pesanti richiederanno più forza (velocità = forza/massa)     [implementato]
  → deposito del raggio traente: il grezzo pesa, il peso
    massimo trasportabile è la 4ª linea di upgrade               [implementato]
  → mining offline: si ferma a deposito pieno, tetto 24 h        [implementato]
  → viaggio (evoluzione 1b): mappa stellare e barra velocità
  → stazione spaziale: missioni → Cookie → sblocco dei tier
```

### Deposito a peso e upgrade del tier 1 (direttiva 12 del 23/07/2026, implementati)

Il **minerale grezzo non entra in magazzino**: resta in un deposito interno al **raggio traente**, con un limite in **peso**. Ogni tipo di minerale ha un suo peso (il ferro pesa 1; i minerali futuri peseranno sempre di più) e il peso massimo trasportabile si potenzia in sala comandi. Il **magazzino** non ha limite ma ospita solo materiali lavorati (lingotti). *(Energia, deuterio e reattore sono stati ritirati: si reinseriranno più avanti se serviranno — la storia completa è in decisioni.md.)*

Le **4 linee di upgrade** della sala comandi, tutte cappate a **Lv 5 = tier 1** (i tier successivi arriveranno con le missioni "Cookie" della stazione spaziale):

| Linea | Effetto Lv 0 → 5 | Costi (lingotti/livello) |
|---|---|---|
| Velocità di fuoco del laser | 1 colpo/2,5 s → 1 colpo/1,25 s | 3, 6, 9, 12, 15 |
| Danni del laser | 5 → 15 per colpo (vale anche per il tap) | 3, 6, 9, 12, 15 |
| Forza di trazione del raggio | 0,8 → 2,8 u/s | 4, 8, 12, 16, 20 |
| Peso trasportabile (deposito) | 20 → 70 | 4, 8, 12, 16, 20 |

Altri numeri della prima implementazione (da validare col playtest):

| Cosa | Valore |
|---|---|
| Peso del ferro grezzo | 1 per minerale |
| Mining offline | richiede torretta + raggio installati; resa = collo di bottiglia reale tra spawn, abbattimento e traino sequenziale; si ferma a **deposito pieno**; tetto **24 h** (idea 1) |
| Schermata mining dinamica | spawn ogni 3,5–6,5 s (max 5 in campo); discesa 0,4 u/s; margine d'ingresso del laser automatico 0,5 u |
| Migrazione salvataggi v2/v3 | energia, deuterio e livelli reattore/batterie decadono senza risarcimento; risorse, lingotti, livelli e installazioni sopravvivono |

Regole fisse: il gioco attivo batte sempre il passivo; il mining manuale (tap e trascina) è sempre gratuito e disponibile (anti-softlock); a deposito pieno si fonde per liberare peso. Gli upgrade si comprano dal **pannello della sala comandi** (grammatica confermata dalla direttiva 12).

## Visione a medio-lungo termine

Idee valutate e messe in roadmap il 23/07/2026 (dettagli in [idee.md](idee.md), ordine di lavoro in [stato.md](stato.md)):

1. ~~**Fonderia comoda**~~ ✓ (23/07) — X di chiusura e "produci tutti"; il "Crafta tutto" globale arriverà con più materiali
2. ~~**Deposito a peso + schermata mining dinamica**~~ ✓ (23/07 sera, direttive 12-13) — hanno sostituito il sistema energetico della v0.3, ritirato dopo il playtest
3. **Viaggio (1b completa) + mappa stellare** — barra velocità, mappa con punti di interesse; raggi traenti multipli
4. **Mercantili e "Cookie"** — incontri casuali di commercio, valuta galattica, compravendita a prezzi variabili
5. **Prima stazione spaziale** — hub dell'universo 1: **missioni che danno Cookie e sbloccano i Tier** della strumentazione (evoluzioni esponenziali, solo agli hangar), commercio, hangar, i **nuovi minerali** (rame, silicio, poi titanio, alluminio — spawn 0% fino all'arrivo, ciascuno col suo peso crescente) e le **stanze costruibili** (es. sala mappe)
6. Più avanti: altre stazioni come checkpoint con gate di teletrasporto; eventuale ritorno di reattore/energia (e del "quadro elettrico") se il design lo richiederà

## UI generale

- Barra risorse in alto: **IRON** / **CARICO peso/max** (dal 23/07/2026 sera, direttiva 12; il mockup `old/img3.jpeg` mostrava la vecchia coppia IRON/FUEL). Il CARICO si tinge d'arancio a deposito pieno.
- Intro narrativa a fumetto prima del gameplay (vedi `old/img1.jpeg`), che termina con "[ INIZIA IL VIAGGIO ]".
- La mappa della stazione Aeterna (`old/img2.jpeg`) è il riferimento del "prima" — utile per l'intro ed eventualmente come visione a lungo termine della progressione.
