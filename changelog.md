# EvolvingSpace — Changelog

> Storico completo delle versioni pubblicate, scritto **per chi gioca** (cosa cambia sullo schermo, non come è fatto dentro).
> È la fonte del changelog **in gioco** (idea 39, tappa 4 della roadmap): lì si mostreranno solo le **ultime 10 versioni**, qui resta tutto.
>
> **Regola di rilascio:** una voce nuova va scritta **a ogni build**, prima di pubblicare la release. Poche righe, verbo al presente, niente numeri di versione del salvataggio.

Le APK sono nelle [Releases](https://github.com/s-ricci/evolvingspace-builds/releases). La **v0.1** fu il primo APK, prima che esistesse l'auto-updater: è finita solo su Drive e non ha una release.

---

## v0.9 — 24/07/2026

- Ribilanciamento del pacing: livelli **1-10** con costi esponenziali (~×1,6) invece di 1-5 aritmetici
- L'**IA del laser** ora va per minerale: Lv 1 riconosce il ferro, Lv 2 il rame
- La **fonderia** va calibrata su ogni minerale nuovo: il primo va sempre minato a mano
- Baratto solo verso il basso (1 rame → 2 ferro), rame a **80 HP**, evoluzioni più care, mercantili proporzionali alla durata della rotta

## v0.8 — 24/07/2026

- **Asteroidi densi**: roccia da 45 HP che droppa 5 minerali, ma serve un laser da ≥ 9 danni per scalfirla
- In viaggio non si mina più: la nave sfreccia e i **mercantili si avvistano**, il tap devia e mette in pausa il timer
- Ad Argo si baratta (4 ferro → 1 rame), niente Cookie
- Mappa stellare **trascinabile**, centrata sul campo dove ti trovi

## v0.7 — 24/07/2026

- **Mappa stellare e viaggio**: rotte a tempo reale tra i campi, che avanzano anche a gioco chiuso
- **Mercantili** in rotta e valuta **Cookie**; il primo incontro rivela la Stazione Argo
- **Stazione Argo**: missioni, commercio, hangar con le evoluzioni **Mk II** (il laser Mk II rompe il rame, il raggio Mk II ha due fasci), sala mappe costruibile

## v0.6 — 24/07/2026

- **Fix**: su Android l'app viene sospesa e ripresa, non riavviata — al rientro il mining offline non veniva conteggiato. Ora c'è il recupero alla ripresa
- Il popup di costruzione non mostra più i moduli già costruiti
- Barra risorse, pulsante nave e fonderia rifatti sui mockup

## v0.5 — 23/07/2026

- **Interno nave a lista di moduli** con pannelli di dettaglio e upgrade, in stile grafico nuovo
- Nuovo modulo **motore a impulso**: più velocità, più asteroidi
- Il mining non si ferma più nei menù: continua a lavorare in sottofondo
- **Menù impostazioni** con volumi separati per musica ed effetti

## v0.4 — 23/07/2026

- Il sistema energetico è stato **ritirato** dopo il playtest
- Il grezzo ora **pesa**: deposito a capacità limitata, si fonde per liberare spazio
- Schermata mining **dinamica**: la nave avanza, gli asteroidi cadono dall'alto e vanno colpiti prima che escano
- **Prima grafica vera** (asteroidi e caccia disegnati), laserata continua da 1 secondo, musica in loop

## v0.3 — 23/07/2026

- **Sistema energetico**: asteroidi di deuterio, reattore che li brucia, batterie come cap dell'offline *(ritirato nella v0.4)*
- Fonderia più comoda: X di chiusura e "produci tutti"

## v0.2 — 22/07/2026

- Prima build con **auto-aggiornamento**: il gioco si accorge da solo delle versioni nuove
- Mining, interno nave, fonderia, salvataggio con progresso offline, torretta automatica e raggio traente
