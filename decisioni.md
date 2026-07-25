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

### 2026-07-24 (dopo la v0.9) — Economia dei Cookie: il rubinetto sono le missioni ripetibili
**Contesto:** dal Google Doc (idee 31, 33, 34, 36). Il listino delle evoluzioni sale da 60-80-70 a **500 Cookie per il primo tier**, mentre la vendita dei lingotti ai mercantili — l'unica fonte davvero ripetibile — viene eliminata. Con le missioni di allora (catena chiusa da 70 Cookie totali) il tier 2 sarebbe diventato irraggiungibile.
**Scelta:**
1. **Via il canale lingotti → Cookie**: i mercantili non comprano più lingotti (né la stazione, che baratta solo verso il basso). Il rubinetto dei Cookie sono le **missioni**, che diventano **ripetibili e randomizzate**.
2. **500 Cookie il passaggio a Mk II** di ogni modulo; i tier successivi si decideranno più avanti.
3. **I Cookie comprano lingotti** (idea 31): ad Argo in quantità, dai mercantili poca roba e a prezzo peggiore. Prezzo di riferimento **10 Cookie per lingotto di rame** — il *doppio* del valore equo (un lingotto di rame vale ~5 "equivalenti asteroide", cioè ~5 Cookie al tasso delle missioni): resta la comodità per colmare l'ultimo buco, mai una scorciatoia. Regola permanente: **il prezzo d'acquisto di un lingotto non scende mai sotto il suo valore in equivalenti**, altrimenti torna dalla finestra il baratto verso l'alto chiuso il 24/07.
**Motivazione:** una valuta forte con un solo rubinetto è più facile da bilanciare di tre canali che si scavalcano; e legare i Cookie alle missioni (cioè a obiettivi progettati) invece che alla vendita (cioè al tempo di mining) permette di alzare il prezzo dei tier senza rendere il gioco un macinino.

### 2026-07-24 (dopo la v0.9) — Missioni ripetibili: valore in "equivalenti asteroide", randomizzate
**Scelta:** ogni missione ha un valore calcolato in **equivalenti asteroide (eq)** — l'unità di lavoro del giocatore — e paga **0,75-1,00 Cookie per eq**, con **tetto di 200 Cookie a missione**.

| Cosa | Vale |
|---|---|
| 1 asteroide di ferro (15 HP, 3 grezzo) | **1 eq** |
| 1 lingotto di ferro (3 grezzo) | **1 eq** |
| 1 asteroide denso (45 HP, 5 grezzo) | 3 eq |
| 1 asteroide di rame (80 HP) | **5 eq** |
| 1 lingotto di rame | **5 eq** |
| 30 unità di ferro grezzo | 10 eq |

- **Tasso**: 0,85-1,00 per le missioni che *consumano* materiale (consegne), 0,75-0,90 per quelle che non consumano nulla (abbattimenti, raccolte) — restano tutte dentro la banda 0,75-1,00 richiesta.
- **Cifre tonde**: asteroidi a multipli di **5**, lingotti a multipli di **10**, grezzo a multipli di **30**, ricompensa arrotondata alle **decine**.
- **Archetipi** (il campo, quando serve, è scelto tra quelli già visitati): consegna lingotti di ferro 60-200 · consegna lingotti di rame 10-40 · abbatti asteroidi in un campo 30-200 · raccogli grezzo in un campo 90-600.
- **Rigenerazione**: la **bacheca di Argo si rinnova a ogni attracco** e ogni **mercantile abbordato offre un incarico**. Missioni attive contemporaneamente: **2**, che salgono con la Sala comunicazioni. Niente rigenerazione automatica a tempo: **nessun limite giornaliero** (regola del 24/07 confermata), il freno è il viaggio.
**Motivazione:** gli "equivalenti asteroide" sono l'unico modo di pagare allo stesso prezzo lavori diversi (rompere, raccogliere, fondere, consegnare) senza creare la scorciatoia del momento — è la lezione della v0.8, dove i costi aritmetici avevano invertito il rapporto sforzo/premio. Il tetto a 200 impedisce alla missione singola di regalare mezzo tier; la banda randomizzata dà varietà senza cambiare il pacing. Il freno del rubinetto è **il viaggio ad Argo**, che è già tempo in cui non si mina: costa senza dire al giocatore di smettere di giocare.
**Stima di pacing:** ~2-4 missioni per ciclo, ~500 Cookie per ora di gioco attivo → un tier ogni ~1-2 ore giocate, i tre tier del capitolo in ~3-6 giorni a 30-60 min/giorno. Da rivalidare col playtest; la leva di taratura è la banda 0,75-1,00.

### 2026-07-24 (dopo la v0.9) — Sala comunicazioni: la manopola del rubinetto
**Scelta:** nuovo modulo costruibile (**10 lingotti · 60 s**, sbloccato dalla prima visita ad Argo) con la lista delle missioni in corso e il loro avanzamento.
- Senza la sala: le missioni si prendono e si consegnano **solo ad Argo**, 2 slot attivi.
- Con la sala: si **consegna a distanza** e si ricevono incarichi via radio dai mercantili; **slot = 2 + (Lv-1)/3** → 2 slot a Lv 1, 3 a Lv 4, 4 a Lv 7, 5 a Lv 10.
- **Ricompense +3% per livello** (+27% a Lv 10), costi della linea standard (4, 7, 11, 16, 27, 40, 67, 105, 165 lingotti).
**Motivazione:** la direttiva chiedeva una stanza per vedere e chiudere le missioni; le si dà anche il ruolo di **regolare la portata del rubinetto** (quante missioni puoi tenere aperte), che è esattamente la manopola che serviva all'economia. Il bonus alle ricompense resta piccolo e cappato apposta: è un moltiplicatore sulla valuta forte, se cresce troppo svuota il prezzo dei tier.

### 2026-07-24 (dopo la v0.9) — Fonderia a livelli per minerale: via le calibrazioni
**Scelta:** la fonderia diventa un modulo come gli altri, con **livelli che corrispondono ai minerali fondibili** (niente più "calibrazione"):
- **Lv 1 = costruzione: 30 ferro grezzo · 30 s** → produce lingotti di ferro.
- **Lv 2 = 60 rame grezzo · 60 s** → produce lingotti di rame.
- Ogni minerale futuro sarà un livello in più, pagato **nel grezzo di quel minerale**.
- Nella schermata della fonderia **compaiono tutte le ricette dei minerali scoperti**, anche quelle non ancora fondibili: la riga è spenta e avvisa *"serve fonderia Lv N"*.
**Motivazione:** un solo concetto (il livello del modulo) al posto di due (costruzione + calibrazione), e la fonderia entra nella grammatica degli altri moduli. Il ciclo fondativo **"ogni minerale nuovo si mina prima a mano"** sopravvive intatto, anzi si rafforza: il rame grezzo per il Lv 2 va estratto a mano (l'IA lo ignora finché non è Lv 2, e l'IA Lv 2 si paga in lingotti di rame che senza fonderia Lv 2 non esistono). Mostrare le ricette bloccate rende visibile il prossimo obiettivo invece di nasconderlo.
**Nota:** la prima fonderia costa **grezzo** e non lingotti per forza di cose — senza fonderia i lingotti non esistono.

### 2026-07-24 (dopo la v0.9) — Cadenza unica del laser: il tempo lo detta il fascio, l'IA ha un tempo di reazione
**Contesto:** oggi manuale e automatico hanno due orologi separati (`MiningScreen._nextAutoShot` avanza solo quando spara l'IA), quindi appena finisce una laserata manuale ne parte una automatica su un bersaglio scelto da lei. La direttiva chiede **una sola cadenza**, valida per entrambi.
**Scelta:**
1. **Un solo timer per il laser**: la cadenza è **durata del fascio + 0,12 s di respiro**, chiunque prema il grilletto.
2. La linea di upgrade **"velocità di fuoco" accorcia il fascio**: **1,00 s → 0,55 s** sui livelli 1-10 (cadenza ~1,12 s → ~0,67 s). Così l'upgrade **si vede su entrambi**, come chiesto.
3. Il **tap manuale ha la prelazione**: spara appena il laser è carico e, se il fascio è già acceso, **lo ridirige** sul nuovo bersaglio. L'IA invece spara solo dopo **0,4 s di "tempo di reazione"** da quando il laser è pronto.
4. Il **tap punta anche l'IA**: dopo un tap, il bersaglio scelto dal giocatore resta quello dell'IA finché è vivo e agganciabile.
**Motivazione:** è l'unica delle tre letture di "cadenza unica" che non rompe niente. Tenere la cadenza dell'IA (2,5 s) anche per il tap dimezzerebbe il ritmo dell'early game, che è il tutorial del gioco; togliere del tutto il vantaggio manuale contraddirebbe la regola fissa *"il gioco attivo batte sempre il passivo"*. Il tempo di reazione di 0,4 s conserva quella regola a costo quasi zero di complessità: chi gioca attivo spara sempre per primo e sceglie il bersaglio, chi lascia fare all'IA perde un'incollatura a colpo.
**Conseguenza da tenere d'occhio:** il laser IA a Lv 1 passa da 2,5 s a ~1,1 s di cadenza. L'effetto è **auto-limitato dallo spawn** (a motore base arriva un asteroide ogni ~5 s: il collo di bottiglia si sposta su spawn e traino, +50% circa di resa invece del +120% teorico) e rende il **motore a impulso** più prezioso. La formula del mining offline va ricalcolata sulla nuova cadenza.

### 2026-07-24 (dopo la v0.9) — La partenza è una scelta: si stacca tutto e il campo resta indietro
**Scelta:** quando parte una rotta la nave **accelera davvero** (mondo ×8 invece di ×4, stelle in scia), gli asteroidi e i minerali non raccolti **restano indietro**, il **laser smette di sparare** e il **raggio traente si spegne** per tutta la durata del viaggio.
**Motivazione:** la direttiva chiedeva più velocità percepita; staccare laser e raggio la rende anche coerente (in crociera non si mina già oggi) e trasforma la partenza in una piccola decisione di gioco — *parto adesso o finisco di raccogliere quello che ho in campo?* — che è esattamente il tipo di scelta che un idle deve offrire senza costare nulla in complessità.

### 2026-07-24 (dopo la v0.9) — Due campi di rame oltre il primo
**Scelta:** oltre al Campo del Rame (65/35) la mappa guadagna **Vena Mista (50/50)** e **Filone di Rame (20/80)**, sempre più lontani. Nessun gate esplicito: senza **laser Mk II** il rame non si scalfisce, quindi un campo 20/80 è già di per sé inutile a chi non è pronto — la regola *"se un campo è troppo avanzato è il laser a non scalfire"* fa da sola il lavoro. La composizione resta "???" senza sala mappe.
**Motivazione:** un solo campo di rame rendeva il rame un rubinetto a portata fissa; tre campi con quote crescenti danno una progressione geografica al secondo metallo (e un motivo per rimettersi in viaggio) senza inventare meccaniche. Il ferro non sparisce mai del tutto neanche nel 20/80, come da regola dei campi discreti.

### 2026-07-24 (dopo la v0.9) — Relitti esplorabili: milestone, in fondo alla roadmap
**Scelta:** i **relitti di stazione esplorabili** (incontro casuale, minigioco a stanze procedurali, HP della tuta, oggetti equipaggiabili) sono una **milestone nominata**, da affrontare **dopo tutto il resto della roadmap**; gli **oggetti come ricompensa delle missioni** entrano insieme a loro, non prima.
**Motivazione:** è la feature più grande mai messa in cantiere (stanze procedurali, combattimento, inventario, arte a schermo pieno) e l'unica che porta contenuto **solo-online**, quindi rinforza la regola "attivo batte passivo" senza toccare i numeri del mining. Ma va progettata con calma: alla frequenza proposta (1% al minuto ≈ un relitto ogni 100 minuti di gioco) rischia di costare settimane per contenuto che si vede ogni due o tre giorni, e una spedizione in corso deve sopravvivere alla sospensione dell'app su Android (la trappola della segnalazione 18). Punti da chiudere quando toccherà: frequenza e pity timer, se il relitto vive nel campo (contraltare dei mercantili, che vivono nel viaggio), perdita totale o parziale del bottino a 0 HP, e la pipeline grafica delle stanze.

### 2026-07-24 (dopo la v0.9) — Rinviato: tetto agli asteroidi grossi nei campi
**Scelta:** il tetto a "1 grosso in Vena Ricca / 2 nel campo denso" **non si tocca per ora** (numeri invariati), su richiesta esplicita.
**Motivazione:** registrata per non perderla. Quando si riprenderà: nella Vena Ricca **tutti** gli asteroidi sono grossi (scala 1,25-1,6, HP ×2, drop ×2) e ce ne sono ~3 in campo, quindi un tetto a 1 taglierebbe la resa di circa il 40% e renderebbe la Vena Ricca peggiore della Cintura Densa — il tetto va accompagnato da una compensazione (grosso ancora più ricco, o densità più alta).

### 2026-07-24 (dopo la roadmap) — La mappa diventa un grafo di sistemi solari; i mercanti si intercettano
**Scelta:** la mappa stellare viene rifatta come **rete di sistemi solari**: i sistemi sono **nodi**, gli **archi** sono le tratte percorribili, il viaggio va **da sistema a sistema**, e ogni sistema contiene i suoi **campi di asteroidi, stazioni e altro**. I **mercanti smettono di essere incontri casuali**: percorrono rotte **visibili sugli archi** e si possono **intercettare**. Entra in roadmap come **tappa 3**, subito prima dei nuovi minerali; i dettagli si chiudono quando ci si arriverà.
**Motivazione:** la mappa piatta a punti di interesse regge 5-7 campi e diventa una nuvola indistinta con silicio, titanio, alluminio e le stazioni successive. Il grafo dà gerarchia (dentro il sistema / tra i sistemi), rende la distanza leggibile come numero di salti e scala senza affollare lo schermo; i **gate di teletrasporto** previsti per le stazioni-checkpoint diventano semplicemente archi speciali. Il guadagno maggiore è sul mercante: da lotteria (~10% al minuto di rotta) a **decisione informata** — lo vedi muoversi, valuti la deviazione, scegli — che è la direzione già presa da tutto il resto del gioco e toglie il caso dall'unica fonte di Cookie.
**Cosa supera:** le decisioni del 24/07 su "mercantili come incontri casuali in rotta" e sul banner di avvistamento con deviazione; da decidere quanto sopravvive (in particolare il primo mercantile garantito che rivela Argo).
**Da chiudere quando toccherà:** se i mercanti si muovono anche a gioco chiuso (serve una simulazione deterministica, altrimenti il salvataggio non regge); **rotte multi-salto** anche offline, con arrivi e attracchi intermedi (oggi la simulazione offline gestisce una tratta sola); cosa rivela cosa (sala mappe sui sistemi, ed eventuale modulo sensori per vedere le rotte dei mercanti); migrazione del salvataggio da `campo` a `sistema + campo`; rivalidazione del rubinetto dei Cookie, oggi tarato sui ~7-10 minuti di rotta verso Argo.

### 2026-07-25 — Menù sviluppatore separato, "finisci subito" dentro la scheda, nessun gate sulle risorse di test
**Scelta:** gli strumenti di debug (idea 40) stanno **dietro un pulsante a parte** dentro le impostazioni — un "SVILUPPATORE" in fondo al menù, sotto il reset, che apre un pannello suo con le tre voci di risorse (+1000 lingotti di ferro, +1000 lingotti di rame, +1000 Cookie); il **changelog** (idea 39) resta invece un tasto normale del menù, è roba da giocatore. Il **"finisci subito" non sta nelle impostazioni**: è un **pulsantino quadrato verde con "»"** dentro la scheda che mostra l'attesa — banner di viaggio e riga del modulo in costruzione — accanto alla barra di progresso. Le risorse di test sono **sempre disponibili**, senza alcun gate: i 1000 lingotti di rame arrivano anche a fonderia non ancora salita a Lv 2.
**Motivazione:** il pulsante separato risolve il rischio del pollice — con quattro tasti che regalano risorse in mezzo agli slider del volume, prima o poi se ne preme uno per sbaglio e il salvataggio è rovinato; e il giorno dell'uscita si spegne un pulsante solo invece di ripulire un menù. Il "»" nella scheda è più preciso di un tasto unico "chiudi tutto": si salta esattamente la cosa che si sta guardando, senza toccare le altre in corso, e non serve navigare in un altro menù mentre si aspetta. Sulle risorse vince la ragione per cui esistono: sono strumenti di test, un gate le renderebbe inutili proprio nel caso che serve provare (verificare il rame *prima* di aver fatto la fonderia Lv 2). Il rame di test entra come lingotti già fatti, quindi non scavalca nessuna regola di gioco vera — scavalca solo il tempo.
**Da chiudere quando toccherà:** se il pannello sviluppatore vada nascosto in release con una costante di compilazione o lasciato accessibile.

### 2026-07-25 (sera, implementando la tappa 3) — I punti aperti del grafo di sistemi solari
**Contesto:** l'idea 38 lasciava sei punti da chiudere "quando toccherà". Toccava: ecco cosa si è deciso implementando, con le alternative scartate.
1. **I mercanti si muovono anche a gioco chiuso, e senza salvare niente.** La posizione di ogni mercante è una **funzione deterministica dell'orologio UTC** (arco + periodo + fase, moto avanti-indietro): riaprendo l'app sono dove sarebbero stati. L'alternativa — simularli e salvarne lo stato — avrebbe aggiunto dati al salvataggio e una simulazione da riallineare a ogni caricamento, per lo stesso risultato visibile.
2. **Intercettazione per prossimità.** Mentre la nave percorre la rotta, se passa entro **3,5 UA** da un mercante scatta il solito banner "in avvicinamento" e il tap devia (rotta in pausa, come prima). Non serve un sistema di appuntamenti: la scelta informata sta nel *decidere la rotta* guardando dove sono.
3. **Il primo mercantile garantito sopravvive**, ma come **rete di sicurezza**: se a metà della prima rotta non si è incrociato nessuno, uno si fa vivo lo stesso. Senza, la Stazione Argo potrebbe restare nascosta per sempre — un vicolo cieco che nessuna eleganza di design ripaga.
4. **Rotte multi-salto: nessun attracco intermedio.** Il percorso attraversa i sistemi ma non ci si ferma; l'arrivo è uno solo, alla destinazione. L'offline resta quello di prima (un solo arrivo da risolvere) e la regola "in rotta non si mina" non ha eccezioni da spiegare.
5. **Migrazione gratuita**: il sistema si **deduce dal campo** (`FieldDef.SystemId`), quindi il salvataggio continua a memorizzare solo il campo corrente e i salvataggi vecchi restano validi senza conversioni.
6. **Cosa rivela cosa**: la **sala mappe** ora svela anche i nomi dei sistemi non visitati (oltre alla composizione dei campi); le rotte dei mercanti si vedono **dopo il primo incontro**. Un modulo "sensori" dedicato resta un'idea per quando i mercanti saranno tanti.
**Motivazione:** il grafo doveva dare struttura senza gonfiare il salvataggio né moltiplicare i casi limite dell'offline, che è la parte più fragile del gioco (segnalazione 18 docet). Tutte e sei le scelte vanno nella stessa direzione: **stato minimo, comportamento leggibile**.

### 2026-07-25 (sera) — Cinque sistemi, non tre: quanto grande farlo
**Scelta:** l'universo 1 nasce con **5 sistemi** (EOS di casa, VESTA, ARGO, KORAX, THULE) e **7 tratte**, con più di un percorso possibile verso quasi tutte le mete. I sette campi esistenti si distribuiscono tra i sistemi; VESTA ne guadagna uno nuovo (**Anello esterno**, ferro con densi) perché un sistema con un campo solo non è un sistema.
**Motivazione:** con 3 nodi il grafo non si sarebbe distinto dalla mappa piatta di prima; con 8-10 sarebbe stato vuoto di contenuto. Cinque bastano a rendere leggibile la distanza in salti, a dare due strade alternative (quindi una scelta) e a lasciare spazio ai sistemi che arriveranno coi minerali nuovi. I **gate di teletrasporto** delle stazioni future entreranno come archi speciali, senza toccare la struttura.

### 2026-07-25 (sera) — Prova di fumo automatica prima delle build
**Scelta:** il progetto ha ora una **prova di fumo** (`Assets/Editor/SmokeTest.cs`, anche da riga di comando) che apre la scena in play mode, gira tutte le schermate e simula un giro di gioco (risorse, fonderia, viaggio interno, salto verso Argo, mercantile, missioni), fallendo se qualcosa lancia a runtime.
**Motivazione:** in questo progetto la compilazione pulita dice poco — **quasi tutta l'interfaccia nasce da codice a runtime**, quindi un riferimento nullo o un pannello costruito male non si vede finché non lo si apre, e con l'auto-updater l'errore arriva dritto sui telefoni. Dieci secondi di play mode in batch costano molto meno di una build da ritirare.

## Decisioni aperte

*(nessuna al momento)*
