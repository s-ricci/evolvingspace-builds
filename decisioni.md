# EvolvingSpace — Registro decisioni

> Documento vivo: quando una decisione viene presa, spostarla da "Aperte" a "Prese" con data e motivazione.
> Design completo in [GDD.md](GDD.md).

## Decisioni prese

### 2026-07-22 — Comportamento della schermata mining
**Scelta:** opzione **a)** — 2 asteroidi statici; la nave aspetta che vengano distrutti e raccolti prima di avanzare (nuova ondata a campo libero).
**Motivazione:** è la versione già descritta nel GDD e la più rapida per arrivare a un primo giocabile che validi il loop "spara → raccogli → crafta". In un idle/incremental la prima schermata fa da tutorial: la semplicità è un pregio. L'opzione **b)** (asteroidi in avvicinamento + barra velocità) resta la **evoluzione naturale a medio termine** — dà la sensazione di viaggio coerente con la lore — e per questo lo spawner degli asteroidi è astratto dietro un'interfaccia (`IAsteroidSpawner`), così il passaggio a b) non richiederà di riscrivere la schermata. L'opzione **c)** (esplorazione procedurale con due levette) è **scartata**: cambia genere, richiede due mani e attenzione continua, l'opposto di un idle giocabile con un pollice.

### 2026-07-22 — Meccanica di distruzione asteroidi e raccolta minerali
**Scelta:** opzione **a)** — tap per sparare col laser, poi trascini i minerali verso l'astronave (versione del GDD).
**Motivazione:** negli incremental le azioni manuali di oggi sono gli upgrade automatici di domani: tap manuale → futuro upgrade "torretta automatica"; trascinamento manuale → futuro upgrade "raggio traente/magnete". L'opzione b) (nave che segue il dito con sparo automatico) brucerebbe in partenza l'upgrade della torretta e contrasta con la nave fissa dell'opzione 1a. Avvertenza di bilanciamento: trascinare i minerali è piacevole con 3 drop, tedioso con 30 — è esattamente il fastidio che renderà desiderabile l'upgrade del magnete, ma va tenuto d'occhio.

### 2026-07-22 — Progressione degli upgrade, ruolo del FUEL e limite offline
**Scelta:** catena di progressione: tap manuale (gratis, sempre disponibile) → **torretta automatica** lenta (5 lingotti, 1 colpo ogni 2,5 s) → upgrade di velocità e danni → **riparazione reattore** → produzione di **FUEL** → **mining offline limitato dall'autonomia del serbatoio** (capacità upgradabile) → **raggio traente a portata limitata** (upgradabile) → **viaggio** (evoluzione 1b) con velocità che consuma FUEL. Gli upgrade si comprano dalle stanze della nave (sala comandi), stessa grammatica del popup della fonderia.
**Motivazione e regole fisse:**
1. *Il gioco attivo batte sempre il passivo*: la torretta automatica parte volutamente molto più lenta del tap (~3 colpi/s a mano vs 1 colpo/2,5 s); gli upgrade riducono il divario, non lo invertono.
2. *Il mining manuale non consuma mai FUEL* (anti-softlock): a serbatoio vuoto si torna a minare a mano, la partita non si blocca mai.
3. *FUEL = energia dei sistemi automatici* (torretta offline, poi velocità di viaggio): il serbatoio diventa il **cap offline diegetico** — il limite classico degli idle, ma spiegato dal mondo di gioco e potenziabile come pezzo di nave (autonomia 30 min → 1 h → …).
4. *Il raggio traente ha senso proprio in movimento*: nella 1b i drop scorrono via con il mondo; la portata limitata (e upgradabile) del raggio è ciò che permette di sostenere velocità più alte. Velocità = manopola rischio/rendimento (più asteroidi/min ma più drop persi) + consumo FUEL + avanzamento del viaggio.
5. La riparazione del reattore è lo *snodo centrale*: sblocca insieme FUEL, mining offline e (dopo) il viaggio.
6. La capienza del magazzino come seconda leva del cap offline è rimandata: una leva alla volta.

### 2026-07-22 — Recupero di bordo lento (coerenza idle online/offline)
**Scelta:** la nave ha di serie un sistema di recupero debolissimo: i minerali droppati **derivano verso il caccia a 0,15 u/s** (~30-40 s dal campo asteroidi), con la calamita rapida solo nell'ultimo tratto. La resa del mining offline usa lo stesso modello del ciclo idle online (uccisione + deriva, ~35 s di raccolta per ondata).
**Motivazione:** senza raccolta automatica di base l'idle online si bloccava dopo una sola ondata (lo spawner attende il campo libero) mentre l'offline assumeva raccolta completa: incoerente in entrambe le direzioni. La deriva lenta fa ciclare l'idle davvero, resta 5-6 volte più lenta del trascinamento manuale (il gioco attivo continua a battere il passivo) e dà al futuro **raggio traente** il ruolo di *potenziare* una meccanica esistente (velocità/portata di attrazione) invece di introdurla da zero — stesso schema della torretta. Ciò che si vede a schermo stando fermi è ciò che il salvataggio simula.

### 2026-07-22 — Raggio traente visibile, un oggetto alla volta
**Scelta:** il raggio traente è un **fascio visibile** (cono verde-acqua pulsante dalla pancia della nave) che aggancia **un minerale alla volta** — il più vicino — e lo traina a bordo. L'upgrade è la **forza di trazione** = velocità di traino (0,8 → 2,8 u/s sui 5 livelli, 4-20 lingotti). Sostituisce la deriva ambientale invisibile del recupero di bordo; la simulazione offline diventa sequenziale (minerali per ondata × distanza/forza).
**Motivazione:** feedback visivo — si *vede* cosa sta facendo la nave, e l'upgrade si vede (traina più in fretta). "Un oggetto alla volta" dà un futuro alla statistica: con i prossimi tipi di asteroide/minerale la velocità diventerà `forza / massa` e sotto una forza minima i materiali pesanti non si solleveranno — gating naturale della progressione dei materiali. La raccolta sequenziale è più lenta della vecchia deriva parallela: l'idle rallenta un po' e il trascinamento manuale (parallelo e gratuito) guadagna ancora valore.

### 2026-07-23 — Sistema energetico: deuterio → reattore → energia → batterie (il FUEL sparisce)
**Scelta:** il reattore non produce più FUEL dal nulla: **brucia deuterio** (nuovo minerale, dall'asteroide di deuterio — idea 3 del [registro idee](idee.md)) e produce **energia**. L'energia alimenta i sistemi della nave e, se avanza, si accumula nelle **batterie**. Reattore (produzione) e batterie (capacità) sono upgradabili separatamente. Tre sotto-decisioni:
1. **Il FUEL viene sostituito dall'ENERGIA** in tutto il gioco: anche il viaggio dell'evoluzione 1b consumerà energia (i motori sono un sistema di bordo come gli altri). Il "rifornimento fuel" della futura stazione spaziale diventa acquisto di deuterio. Niente doppia valuta energetica.
2. **I sistemi consumano energia anche online** (torretta automatica, raggio traente, poi motori): online il reattore copre i consumi e l'avanzo carica le batterie. Da bilanciare perché l'inizio partita non sia frustrante.
3. **Offline il reattore è in standby** (flavor: protocollo di sicurezza senza pilota a bordo) e i sistemi attingono solo dalle batterie: la carica delle batterie **è** il cap offline. Obiettivo di bilanciamento: capacità iniziale ≈ 12–24 h di offline (idea 1).
**Motivazione:** il FUEL era l'unica risorsa prodotta "gratis" in un gioco dove tutto si estrae e si trasforma — incoerente. La catena deuterio → reattore → energia → batterie dà uno scopo immediato al deuterio, rende il cap offline perfettamente diegetico (reattore spento ⇒ durata offline = carica batterie) e trasforma la futura schermata delle automazioni offline (idea 2) nel "quadro elettrico" della nave: quali sistemi lasciare attaccati alle batterie, ognuno col suo consumo. Online si gioca finché c'è deuterio, offline finché c'è batteria: il gioco attivo continua a battere il passivo, come da regola fissa. Migrazione salvataggi: FUEL esistente → energia in batteria, reattore riparato resta riparato. *(Aggiorna la regola 3 della decisione del 22/07 sulla progressione: dove si legge FUEL/serbatoio, ora vale energia/batterie.)*

<!-- Formato:
### AAAA-MM-GG — Titolo decisione
**Scelta:** ...
**Motivazione:** ...
-->

## Decisioni aperte

### Grammatica UI degli upgrade: pannello centrale o oggetti fisici?
Dall'idea 5 del [registro idee](idee.md): oggi tutti gli upgrade si comprano dal pannello della sala comandi; la proposta è potenziare gli strumenti **cliccandoli fisicamente** nella schermata interno nave (ed eventualmente raggrupparli in una stanza "Armeria/torrette"). Vale per raggi traenti, torretta e reattore/batterie: da decidere una volta sola, prima di aggiungere i raggi multipli.
