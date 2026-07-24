# EvolvingSpace — Registro decisioni

> Documento vivo: quando una decisione viene presa, spostarla da "Aperte" a "Prese" con data e motivazione.
> Design completo in [GDD.md](GDD.md).

## Decisioni prese

### 2026-07-22 — Comportamento della schermata mining
**Scelta:** opzione **a)** — 2 asteroidi statici; la nave aspetta che vengano distrutti e raccolti prima di avanzare (nuova ondata a campo libero).
**Motivazione:** è la versione già descritta nel GDD e la più rapida per arrivare a un primo giocabile che validi il loop "spara → raccogli → crafta". In un idle/incremental la prima schermata fa da tutorial: la semplicità è un pregio. L'opzione **b)** (asteroidi in avvicinamento + barra velocità) resta la **evoluzione naturale a medio termine** — dà la sensazione di viaggio coerente con la lore — e per questo lo spawner degli asteroidi è astratto dietro un'interfaccia (`IAsteroidSpawner`), così il passaggio a b) non richiederà di riscrivere la schermata. L'opzione **c)** (esplorazione procedurale con due levette) è **scartata**: cambia genere, richiede due mani e attenzione continua, l'opposto di un idle giocabile con un pollice.
*(Superata il 23/07 sera: gli asteroidi ora scendono dalla cima della mappa — vedi "Schermata mining dinamica".)*

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
*(Superata il 23/07 sera, dopo il playtest della v0.3: energia, deuterio e reattore sono stati ritirati — vedi "Passo indietro sull'energia".)*

### 2026-07-23 (sera) — Passo indietro sull'energia: via deuterio e reattore, il grezzo pesa e vive nel raggio traente
**Scelta:** dal Google Doc, dopo il playtest della v0.3 (direttiva 12 del [registro idee](idee.md)). Quattro punti:
1. **Energia, deuterio e reattore vengono rimossi** dal gioco (asteroide di deuterio compreso); si reinseriranno più avanti se serviranno. I sistemi automatici (torretta, raggio traente) tornano a non consumare nulla.
2. **Il magazzino non ha limite ma non contiene minerale grezzo**: ospita solo materiali lavorati (lingotti). Il grezzo è trattenuto in un **deposito interno al raggio traente** con **limite in peso**: ogni tipo di minerale ha un suo peso (ferro = 1; i minerali futuri peseranno sempre di più) e a deposito pieno non si raccoglie altro finché non si fonde.
3. **Gli upgrade di bordo restano nel pannello della sala comandi** — questo chiude la decisione aperta sulla grammatica UI — e sono quattro linee: velocità di fuoco del laser, danni del laser, forza di trazione del raggio traente, peso trasportabile del deposito. **Tutte cappate a Lv 5 = tier 1.**
4. **I tier si sbloccheranno con le missioni della stazione spaziale**, che daranno la valuta "Cookie"; finché stazione e valuta non esistono, si resta a Lv 5 tier 1.
**Motivazione:** l'economia energetica introdotta ieri (secondo minerale, reattore, batterie, consumi al minuto) aggiungeva troppa gestione troppo presto rispetto al cuore del gioco. Il limite in peso sul grezzo ricrea lo stesso ritmo "torna in fonderia e svuota" con una sola valuta, dà un corpo concreto al sistema forza/massa già previsto per il raggio traente e sposta il cap offline dalle batterie a **deposito pieno + tetto di 24 h** (idea 1). Il mining manuale resta sempre gratuito (anti-softlock, regola invariata). Migrazione salvataggi: energia, deuterio e livelli di reattore/batterie decadono senza risarcimento; tutto il resto (risorse, lingotti, livelli, installazioni) sopravvive.

### 2026-07-23 (sera) — Schermata mining dinamica (supera la 1a)
**Scelta:** dal Google Doc (direttiva 13 del [registro idee](idee.md)): stelle che pulsano ed effetto di nave in avanzamento; asteroidi che compaiono random dalla cima della mappa, con piccolo margine di deriva laterale e **discesa lenta e costante**, e che **scompaiono in fondo** se non distrutti; il **laser automatico** aggancia solo asteroidi interamente dentro l'area di gioco e comunque sotto un margine d'ingresso in alto; alla distruzione i minerali (più piccoli di prima) **"scappano" dall'esplosione** con una spinta contenuta, catturabile dal raggio traente.
**Motivazione:** è la componente visiva dell'evoluzione **1b** anticipata senza barra velocità né mappa: il mondo scorre, la nave "viaggia", e il despawn in fondo introduce la prima pressione temporale dolce del gioco (un asteroide ignorato è ferro perso). Il margine d'ingresso del laser automatico lascia al giocatore la prelazione sui bersagli appena entrati e evita colpi su asteroidi mezzi fuori schermo.

<!-- Formato:
### AAAA-MM-GG — Titolo decisione
**Scelta:** ...
**Motivazione:** ...
-->

### 2026-07-23 (sera) — Grammatica UI degli upgrade: pannello della sala comandi
**Scelta:** gli upgrade di bordo si comprano dal **pannello della sala comandi** (punto 3 della direttiva 12, che elenca esplicitamente le quattro linee "uppabili nella sala comandi"). L'idea della stanza "Armeria/torrette" con potenziamento a tap sugli oggetti fisici (idea 5) resta nel cassetto per quando arriveranno i raggi traenti multipli.
**Motivazione:** era la decisione aperta che bloccava la tappa dei raggi multipli; il Google Doc l'ha chiusa confermando la grammatica attuale. Un pannello unico scala meglio con poche linee di upgrade cappate a Lv 5; il potenziamento "fisico" tornerà in discussione se gli strumenti da gestire diventeranno tanti.
*(Superata la sera stessa: con l'interno a lista di moduli — direttiva 14 — ogni upgrade vive nel pannello del proprio modulo.)*

### 2026-07-23 (notte) — Interno nave a lista di moduli; gli upgrade vivono nel loro modulo
**Scelta:** dal Google Doc dopo la v0.4 (direttiva 14 del [registro idee](idee.md)). L'interno nave abbandona la piantina top-down: è una **lista dei moduli costruiti** con in fondo il tasto **"Costruisci modulo"** (ogni modulo ha un tempo di costruzione). Moduli di partenza: sala comandi (per ora senza upgrade), **magazzino** (che eredita il deposito del grezzo dal raggio traente: 5 livelli, stesso sistema a unità), **motore a impulso** (nuovo: 5 livelli, più velocità di avanzamento ⇒ più asteroidi in mappa), **laser minerario** (velocità e danno a 5 livelli + 1 livello di **"IA"** che lo automatizza — l'ex torretta automatica). Costruibili: **fonderia** (come oggi, nessun upgrade) e **raggio traente** (con l'upgrade di trazione). Ogni modulo ha il suo pannello di upgrade: tap sulla riga → dettaglio.
**Motivazione:** ogni potenziamento accanto alla cosa che potenzia — sparisce il pannellone unico della sala comandi (supera la decisione sulla grammatica UI presa poche ore prima: la sala comandi resta il "ponte", non il negozio). La lista è pronta ad accogliere le stanze costruibili future (idea 11) e il motore a impulso introduce la manopola di ritmo che diventerà la velocità di viaggio della 1b.

### 2026-07-23 (notte) — Il mining non si ferma mai: schermata sempre attiva, menù come overlay
**Scelta:** la schermata mining resta **sempre attiva**; interno nave e fonderia diventano **overlay a canvas opaco** disegnati sopra (segnalazione 15). Niente più disattivazione reciproca delle schermate.
**Motivazione:** con l'architettura precedente laser automatico, raggio traente e spawner si congelavano appena si apriva un menù — un idle che smette di essere idle quando lo guardi da dentro la nave. Con l'overlay il mondo continua a girare (si sente pure il laser lavorare mentre sei nei menù) e la barra risorse si aggiorna in diretta. Il canvas opaco blocca anche i tap verso la schermata sotto.

### 2026-07-24 — Progressione a lungo termine: livelli → tier "Mk" → milestone nominate → universi
**Scelta:** confermata la gerarchia a **quattro piani** proposta dopo il playtest della v0.6 (in ~24 h si raggiungeva il cap Lv 5 tier 1 e il contenuto finiva):
1. **Livelli (1–5)** — la crescita quotidiana a bordo, per linea di upgrade. Scala dei minuti/ore.
2. **Tier per modulo**, mostrati come **"Mk II", "Mk III"…** — l'acquisto grosso all'**hangar** delle stazioni, sbloccato dalle missioni Cookie (idea 9). Il numero resta strutturalmente basso perché **gated dalle stazioni raggiunte**: mai un "tier 400".
3. **Milestone nominate** — gli unlock maggiori: eventi unici della mappa (prima stazione, portale…), mai numerate; ognuna sblocca contenuto nuovo (minerali, moduli, meccaniche). Prendono il nome dalla storia.
4. **Universi** — i capitoli a lunghissimo termine (l'"universo 1" è già nel GDD); eventuale sede di un futuro prestige.
**Motivazione:** i numeri infiniti stanno nei livelli piccoli, i gradini grossi sono pochi, nominati e fatti di contenuto — la lezione dei giochi che invecchiano bene (Egg Inc. coi capitoli nominati, Idle Planet Miner, Deep Town); quelli che numerano i gradini grossi li svuotano di significato. "Mk" è la grammatica sci-fi da equipaggiamento (FTL, Elite Dangerous), non da contatore. Non si allunga il tier 1 gonfiando i numeri: un capitolo 1 da ~24 h è nella norma del genere, i capitoli successivi dureranno ciascuno 2-3 volte il precedente.

### 2026-07-24 — Campi di asteroidi discreti con tabelle di spawn; doppio gating dei minerali nuovi
**Scelta:** i nuovi minerali arrivano per **campi discreti sulla mappa stellare**, ognuno con la sua tabella di spawn (es. campo di partenza 100% ferro; campo del rame 65% ferro / 35% rame; campo del silicio 40/40/20; più rare "vene pure" come punti di interesse speciali). Regole: il **minerale vecchio non sparisce mai del tutto** (serve alle ricette); il gating dei minerali nuovi è doppio e già previsto dal design — il **tier del laser** decide quali asteroidi si rompono (idea 9), la **forza/massa del raggio traente** quali minerali si trainano. **Niente asteroidi rompibili con drop non raccoglibili** (frustrazione pura): se un campo è troppo avanzato, è il laser a non scalfire. La **sala mappe** (idea 11) rivelerà la composizione dei campi.
**Motivazione:** rispetto a una tabella globale che cambia con la distanza (alla Mr. Mine), i campi discreti danno uno scopo alla mappa stellare, danno agency ("mi serve rame → vado al campo del rame", alla Idle Planet Miner), si bilanciano a tabelle e fanno da sponda alla sala mappe.

### 2026-07-24 — Viaggio a tempo reale tra i campi; mining in rotta; mercantili come incontri
**Scelta:** sulla mappa si sceglie il punto di interesse e la nave **viaggia in tempo reale**: durata = distanza / velocità (livello del motore a impulso, che diventa ciò che il nome promette). Durante la rotta il mining **non si ferma**: tabella "spazio aperto" (spawn rarefatto e povero); all'arrivo scatta la tabella del campo. Il **viaggio avanza anche offline**. Nessun costo in carburante (l'energia è ritirata; il tempo è già un costo — se l'energia tornerà, il viaggio è il posto ovvio dove riattaccarla). I **mercantili** (idea 6) diventano **incontri casuali in rotta**.
**Motivazione:** la selezione istantanea del campo (alla Idle Planet Miner) sarebbe più comoda ma ucciderebbe la fantasia del viaggio, cuore della lore; il mining in rotta rende il viaggio tempo di gioco degradato anziché tempo morto, e "parto e chiudo l'app" è un'ottima azione idle.

### 2026-07-24 (pomeriggio) — Dettagli della v0.7: nome ARGO, scoperta via mercantile, rame = valuta dei livelli Mk II, sala mappe inclusa
**Scelta:** quattro punti chiusi prima dell'implementazione delle tappe 3-5 (tutte confluite nella v0.7):
1. La prima stazione si chiama **ARGO** (il nome del mockup diventa definitivo).
2. La stazione è nascosta sulla mappa ("???" / zona sconosciuta) e **la rivela il primo mercantile** incontrato in rotta, trasmettendone le coordinate. Il primo incontro è garantito sul primo viaggio; poi ~1 rotta su 2.
3. **Il rame paga i livelli Mk II**: in fonderia 3 rame grezzo → 1 lingotto di rame; dopo l'evoluzione Mk II di un modulo, i suoi livelli 1–5 riaperti si comprano in lingotti di rame (il ferro resta la valuta dei moduli Mk I). Il rame grezzo pesa **2 unità** di carico e il raggio lo traina a metà velocità (velocità = forza/massa).
4. La **sala mappe entra nella v0.7**: modulo costruibile (8 lingotti · 30 s) sbloccato dalla prima visita alla stazione; senza, i campi non ancora visitati mostrano "???" al posto della composizione.
**Motivazione:** (1) un nome vero serve subito e ARGO suona bene; (2) lega le tappe in modo naturale — viaggi → incontri il commercio → scopri la stazione — e dà al primo mercantile un momento memorabile; (3) dà un sink immediato al rame appena sbloccato, senza aspettare contenuti futuri; (4) con la composizione dei campi nascosta la sala mappe ha uno scopo dal primo giorno.

### 2026-07-24 (sera) — Fix post-v0.7: asteroidi densi, viaggio senza mining, mercantili a deviazione, stazione a baratto
**Scelta:** tre direttive dopo la revisione della v0.7:
1. **Asteroidi densi**: la densità alta di un campo non si traduce solo in più asteroidi ma in un **mix** con una nuova variante — roccia scura compatta, **45 HP**, droppa **5 minerali**, e una **corazza che chiede almeno 9 danni per colpo** (danni Lv 2): chi ha il laser al minimo non li scalfisce. Più densità ⇒ più densi in proporzione (Cintura Densa: spawn ×1,25 e 35% di densi — prima era solo ×1,6 di spawn; Campo del Rame: 20% di densi sul ferro). Così lo schermo non si affolla ma la resa cresce.
2. **In viaggio non si mina**: niente spawn in rotta, il laser riposa; la nave si porta **verso il centro della visuale** coi motori spinti (fiamme ×2) e le stelle sfrecciano (mondo ×4-5) per dare l'idea dell'alta velocità. I **mercantili diventano rari** (~1 rotta su 5, il primo resta garantito) e non aprono più il banco a schermo pieno: compare un banner **"Mercantile in avvicinamento…"** e toccarlo devia verso di lui — **la rotta va in pausa** finché non si chiude il banco, poi riprende. Il primo mercantile **abbordato** (non solo avvistato) rivela la Stazione Argo.
3. **Commercio di stazione = solo baratto tra lingotti**, molto meno redditizio dei mercantili e senza Cookie: 4 lingotti di ferro → 1 di rame; 1 di rame → 2 di ferro. I Cookie girano solo in rotta (e con le missioni).
**Motivazione:** (1) varietà e progressione del danno senza saturare campo e pollice; (2) il viaggio diventa un momento con la sua identità (velocità, attesa, eventi) invece di un mining più povero, e i mercantili rari con la deviazione a scelta valgono di più; (3) se gli incontri sono rari devono restare la fonte buona di Cookie — la stazione copre solo la conversione di comodo tra metalli.
**Coerenza offline**: in rotta non si mina nemmeno offline; i densi contano nella resa solo se il laser li rompe.

### 2026-07-24 (sera) — Mappa stellare alleggerita: trascinabile, cluster piccoli e nomi in chip
**Scelta:** la mappa diventa **trascinabile (pan)**: l'area virtuale è ~860×1500 su uno schermo da 720×1280, i campi respirano e all'apertura la vista si **centra sul campo corrente** (o sul punto medio della rotta in corso). In più i punti di interesse perdono le didascalie (restano nel popup di destinazione) e mostrano **solo il nome dentro una "chip" scura semitrasparente** sotto un cluster ridotto. Il drag parte da qualunque punto (anche sopra un campo: il tap resta tap sotto la soglia di trascinamento) e un suggerimento discreto in basso dice "trascina per esplorare la mappa".
**Motivazione:** le scritte si sovrapponevano ai sassi e la mappa risultava piena; il pan risolve alla radice (spazio virtuale invece di compressione) e scala già ai campi futuri, la chip dà uno sfondo costante al testo, e togliere le didascalie dimezza il testo a schermo senza perdere informazioni (sono a un tap di distanza).

### 2026-07-24 (notte) — Ribilanciamento dell'economia (playtest v0.8: tutto maxato in 1 ora anche da zero) + IA e fonderia "per minerale"
**Contesto:** i costi dei livelli erano aritmetici (3, 6, 9, 12, 15) mentre la rendita cresce con gli upgrade: i livelli alti diventavano più facili dei bassi e i due tier collassavano in un'ora, anche partendo da zero e senza baratto. Il baratto ferro → rame era una falla a qualunque rapporto. I limiti giornalieri sono scartati (snervanti: dicono "smetti di giocare").
**Scelta (pacchetto approvato, implementato nella v0.9):**
1. **Livelli 1-10** (si parte da Lv 1: la nave ha già qualcosa) con **costi esponenziali ~×1,6 per livello**: linee laser 3, 5, 8, 12, 20, 30, 50, 80, 125 (≈ 330 lingotti a linea); raggio/magazzino/motore 4, 7, 11, 16, 27, 40, 67, 105, 165 (≈ 440). Al Mk II le stesse tabelle si pagano in lingotti di rame. Il flusso di minerali NON si tocca (è la parte divertente): la leva sono i costi. Migrazione proporzionale: Lv 3/5 → Lv 6/10.
2. **Baratto solo verso il basso**: 1 rame → 2 ferro; la direzione ferro → rame sparisce (qualunque rapporto verso l'alto prima o poi diventa una scorciatoia).
3. **Rame più duro**: asteroide 40 → 80 HP.
4. **Evoluzioni Mk II**: 60 (laser) · 80 (raggio) · 70 (magazzino) Cookie; missioni invariate (70 totali).
5. **Mercantili proporzionali alla rotta**: probabilità ≈ 10%/minuto di viaggio (10 min ≈ garantito) — niente attesa RNG pura per i Cookie; il primo resta garantito.
6. **Nessun limite giornaliero**; l'unico tetto offline resta il magazzino pieno (diegetico).
7. **IA per minerale** (proposta accolta): l'IA impara un minerale alla volta — Lv 1 riconosce il ferro (5 lingotti di ferro), Lv 2 il rame (10 lingotti di rame, fatti a mano per forza); senza IA Lv 2 la torretta ignora il rame anche col laser Mk II (il tap manuale resta libero, anti-softlock; l'offline segue).
8. **Calibrazione della fonderia** (proposta accolta): costruzione gratis come oggi, poi il forno si tara su ogni minerale pagando in grezzo — Lv 1 "forno al ferro" (10 ferro grezzo), Lv 2 "forno al rame" (25 rame grezzo). Ogni minerale nuovo va **minato a mano la prima volta**: è la regola fondativa ("le azioni manuali di oggi sono gli upgrade automatici di domani") trasformata in ciclo ripetibile per ogni metallo futuro.
**Obiettivo di pacing:** tier 1 ~1-2 giorni, tier 2 ~3-5 giorni (giocatore attivo 30-60 min/giorno + offline); da rivalidare con playtest da zero. Se il capitolo 2 resta corto, la leva successiva è contenuto (silicio/Mk III), non altri numeri.
**Migrazione (salvataggio v7):** livelli in proporzione; IA installata ⇒ IA Lv 1; fonderia costruita ⇒ forno al ferro (⇒ anche al rame se ci sono già lingotti di rame).

## Decisioni aperte

*(nessuna al momento)*
