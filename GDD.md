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
- Sopra il caccia: 4 piccoli asteroidi che danno l'idea di essere pieni di ferro *(erano 2 nel design iniziale; portati a 4 e rimpiccioliti nella revisione grafica del 22/07/2026)*.
- **Tap su un asteroide** → dalla torretta parte un raggio laser rapido verso il bersaglio → sopra l'asteroide compare il numero del danno ("5").
- HP asteroide: **15** → servono 3 colpi da 5 per distruggerlo.
- Alla distruzione: piccola animazione esplosiva, l'asteroide scompare e droppa **3 minerali di ferro grezzo** nella stessa posizione.
- **Trascina col dito** i minerali verso il caccia: quando sono abbastanza vicini scompaiono e appare un "+1" che svanisce rapidamente (feedback di raccolta).
- In basso: pulsante per passare alla schermata dell'interno nave.

> ✅ **Deciso il 22/07/2026** (vedi [decisioni.md](decisioni.md)): asteroidi statici a ondate (opzione 1a) e meccanica tap + trascina (opzione 2a). Lo spawner degli asteroidi è astratto (`IAsteroidSpawner`) per poter evolvere in futuro verso gli asteroidi in avvicinamento con barra velocità (opzione 1b). Le azioni manuali (tap, trascina) sono pensate per diventare upgrade automatici più avanti (torretta automatica, raggio traente).

### Schermata 2 — Interno nave

Piantina del caccia vista dall'alto, poche stanze:

| Stanza | Stato iniziale |
|---|---|
| Sala comandi | Poltrona con il personaggio seduto |
| Magazzino | Mezzo spoglio, un paio di pallet vuoti |
| Fonderia | Rotta, malandata, al centro della stanza |
| Reattore | Malandato ma funzionante |

- **Click sulla fonderia rotta** → pop-up che spiega che è rotta; pulsante verde **"Costruisci"** con indicazione "(30 secondi)".
- Premendo "Costruisci" parte una barra che si riempie in 30 secondi; al termine la stanza si trasforma: resta trasandata ma la fonderia diventa utilizzabile.
- I minerali di ferro grezzo raccolti vengono **visualizzati automaticamente sui pallet** del magazzino.

### Schermata 3 — Crafting (fonderia)

Click sulla fonderia riparata → schermata di crafting. Ricetta disposta in orizzontale:

```
3 × [minerale ferro grezzo]  →  1 × [lingotto di ferro]     [CRAFT]
```

- Freccia rossa come indicatore di conversione, pulsante rosso "Craft" a destra.
- Premendo "Craft" si consumano 3 minerali grezzi e si produce 1 lingotto.

## Progressione e potenziamenti

Catena decisa il 22/07/2026 e rivista il 23/07/2026 col **sistema energetico** (dettagli e motivazioni in [decisioni.md](decisioni.md)):

```
tap manuale (gratis, sempre)
  → torretta automatica lenta (5 lingotti, 1 colpo/2,5 s)        [implementata]
  → upgrade torretta: velocità di fuoco, danni (Lv 0–5)          [implementati]
  → riparazione reattore (10 lingotti · 60 s)                    [implementata*]
  → mining offline limitato dall'energia accumulata              [implementato*]
  → raggio traente visibile: fascio che aggancia 1 minerale
    alla volta; forza di trazione Lv 0–5 = velocità di traino
    0,8→2,8 u/s (4-20 lingotti/livello); in futuro i materiali
    pesanti richiederanno più forza (velocità = forza/massa)     [implementato]
  → sistema energetico: deuterio → reattore → energia → batterie
    (sostituisce il FUEL; upgrade reattore e batterie)
  → viaggio (evoluzione 1b): barra velocità che consuma energia
```

*\* Implementati nella versione provvisoria a FUEL (il reattore produce 1 FUEL/20 s dal nulla, serbatoio da 30): sarà sostituita dal sistema energetico qui sotto.*

### Sistema energetico (deciso il 23/07/2026)

**Deuterio → reattore → energia → batterie.** Il deuterio è un nuovo minerale (asteroide dedicato, spawn 25% al posto di uno di ferro). Il reattore lo brucia e produce energia; l'energia alimenta i sistemi della nave (torretta automatica, raggio traente, poi i motori) e l'avanzo carica le batterie.

- **Il FUEL non esiste più**: unica valuta energetica, l'ENERGIA. Anche il viaggio la consuma (i motori sono un sistema di bordo).
- **Online**: il reattore copre i consumi finché c'è deuterio; l'avanzo va in batteria.
- **Offline**: il reattore è in standby (protocollo di sicurezza senza pilota a bordo); i sistemi attingono solo dalle batterie → **la carica delle batterie è il cap offline**. Capacità iniziale tarata su 12–24 h.
- **Upgrade su due assi**: reattore (produzione) e batterie (capacità), separati.
- Il mining manuale non consuma mai energia (anti-softlock, regola invariata).

Regole fisse: il gioco attivo batte sempre il passivo; il mining manuale non consuma mai energia (anti-softlock); le batterie sono il cap offline del gioco. Gli upgrade si comprano dalle stanze della nave (popup della sala comandi, come per la fonderia) — *grammatica in discussione, vedi decisione aperta in [decisioni.md](decisioni.md)*.

## Visione a medio-lungo termine

Idee valutate e messe in roadmap il 23/07/2026 (dettagli in [idee.md](idee.md), ordine di lavoro in [stato.md](stato.md)):

1. **Fonderia comoda** — X di chiusura, "produci tutti", "Crafta tutto" (QoL immediata)
2. **Sistema energetico** — deuterio, reattore, energia, batterie; offline fino a 12–24 h col popup di recap
3. **Viaggio (1b) + mappa stellare** — asteroidi in avvicinamento, barra velocità, mappa con punti di interesse; raggi traenti multipli
4. **Mercantili e "Cookie"** — incontri casuali di commercio, valuta galattica, compravendita di ferro e deuterio a prezzi variabili
5. **Prima stazione spaziale** — hub dell'universo 1: rifornimento deuterio, commercio, hangar. Con lei arrivano i **Tier** della strumentazione (evoluzioni esponenziali, solo agli hangar), i **nuovi minerali** (rame, silicio, poi titanio, alluminio — spawn 0% fino all'arrivo) e le **stanze costruibili** (es. sala mappe)
6. **Quadro elettrico** — schermata di configurazione delle automazioni offline (quando le automazioni saranno ≥ 2)
7. Più avanti: altre stazioni come checkpoint con gate di teletrasporto

## UI generale

- Barra risorse in alto: **IRON** / **FUEL** (vedi mockup `old/img3.jpeg`) — col sistema energetico diventerà **IRON** / **DEUTERIO** / **ENERGIA**.
- Intro narrativa a fumetto prima del gameplay (vedi `old/img1.jpeg`), che termina con "[ INIZIA IL VIAGGIO ]".
- La mappa della stazione Aeterna (`old/img2.jpeg`) è il riferimento del "prima" — utile per l'intro ed eventualmente come visione a lungo termine della progressione.
