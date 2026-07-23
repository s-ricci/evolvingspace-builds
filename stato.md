# EvolvingSpace — Stato dei lavori

> Documento vivo: aggiornare a ogni sessione di lavoro (cosa è fatto, cosa è in corso, prossimo passo).

## Aggiornato al 2026-07-23

**Fatto:**
- Progetto Unity 6000.4.10f1 creato
- Documentazione riorganizzata in `Docs/` (GDD.md, decisioni.md, stato.md + `documentazione.html` che li raccoglie in un'unica pagina); materiali originali spostati in `Docs/old/`
- Chiuse le due decisioni sulla schermata mining: opzioni **1a** (asteroidi statici a ondate) e **2a** (tap + trascina) — vedi [decisioni.md](decisioni.md)
- **Schermata mining implementata** (primo giocabile, grafica segnaposto generata a runtime):
  - `Assets/Scripts/Core/` — `GameResources` (risorse IRON/FUEL), `PlaceholderSprites` (sprite pixel-art procedurali), `FloatingText` (numeri di danno e "+1")
  - `Assets/Scripts/Mining/` — `MiningScreen` (controller + input col nuovo Input System), `IAsteroidSpawner` (astrazione per la futura evoluzione 1b) + `StaticAsteroidSpawner` (ondate di 2 asteroidi), `Asteroid` (15 HP, droppa 3 minerali), `Turret` (laser, 5 danni a tap), `Mineral` (trascinabile, raccolta vicino al caccia), `Debris` (esplosione)
  - `Assets/Scripts/UI/ResourceBarUI` — barra IRON/FUEL in alto; pulsante "INTERNO NAVE" in basso (segnaposto)
  - `Assets/Editor/MiningSceneBuilder` — menu **EvolvingSpace → Genera scena Mining** che crea `Assets/Scenes/Mining.unity`
- **Rifiniture di game feel** (4 pacchetti, sessione del 22/07):
  1. *Colpi incisivi* — screen-shake (`CameraShake`), squash & stretch e lampo rosso sull'asteroide colpito, scintille d'impatto (`Debris.Sparks`), torretta che ruota verso il bersaglio con rinculo
  2. *Raccolta appagante* — il minerale vola verso la nave alla raccolta, "pop" del contatore IRON, mini-calamita sotto 1,5 unità dal caccia (assaggio del futuro upgrade magnete), luccichio periodico sui minerali
  3. *Mondo vivo* — asteroidi che ruotano/oscillano, scale-in allo spawn, scurimento progressivo col danno (niente barra HP), fiamma motori animata + bobbing nave (`ShipVisuals`), stelle che brillano (`Starfield`)
  4. *Audio segnaposto* — suoni sintetizzati a runtime (`ProceduralAudio`): pew laser, crack esplosione, blip raccolta
- Fix latenza audio: buffer DSP portato da 1024 a **256** ("Best latency") in `ProjectSettings/AudioManager.asset`, attacco istantaneo del blip di raccolta, warm-up di clip e sorgenti all'avvio della schermata
- **Schermata 2 — Interno nave implementata**:
  - `Assets/Scripts/Core/GameBootstrap` — radice del gioco: camera, EventSystem, audio e le due schermate attivate una alla volta (niente caricamento scene: lo stato sopravvive al cambio)
  - `Assets/Scripts/Ship/ShipState` — stato della fonderia (rotta → in costruzione → riparata) su base `Time.time`: i 30 s avanzano anche mentre si sta minando
  - `Assets/Scripts/Ship/ShipInteriorScreen` — piantina top-down con le 4 stanze del GDD (sala comandi con poltrona e personaggio, magazzino con pallet, fonderia rotta, reattore pulsante); tap sulla fonderia → popup "Costruisci (30 secondi)" → barra progresso → fonderia riparata; i minerali raccolti compaiono sui pallet (fino a 12); tap sulle altre stanze → testi flavor
  - `Assets/Scripts/UI/UiKit` — fabbrica uGUI condivisa (canvas, bottoni, testi)
  - I pulsanti "INTERNO NAVE" / "TORNA AL MINING" commutano le schermate
  - `Assets/Editor/GameSceneBuilder` — nuovo menu **EvolvingSpace → Genera scena di gioco** che crea `Assets/Scenes/Game.unity` e rimuove la vecchia `Mining.unity` (builder vecchio eliminato)

- **Schermata 3 — Crafting implementata** (chiude il primo loop completo: mina → ripara → crafta):
  - `Assets/Scripts/Ship/CraftingScreen` — terza schermata del `GameBootstrap`; si apre col tap sulla fonderia riparata. Ricetta orizzontale come da GDD: 3 × minerale grezzo → (freccia rossa) → 1 × lingotto, pulsante rosso **CRAFT** a destra, attivo solo con ≥ 3 minerali. Al craft: clang metallico sintetizzato, pop dell'icona lingotto, contatori "hai: N" sotto le icone
  - `GameResources` esteso: `Ingots`, `TrySpendIron`, `AddIngots`; sprite procedurale del lingotto in `PlaceholderSprites`
  - Il tap sul magazzino ora mostra anche i lingotti
  - Nessuna rigenerazione scena necessaria: `Game.unity` contiene solo camera + `GameBootstrap`, le schermate nascono a runtime

- **Persistenza dello stato implementata**:
  - `Assets/Scripts/Core/SaveSystem` — salvataggio JSON in `Application.persistentDataPath/save.json` (scrittura atomica via file temporaneo): risorse (ferro, fuel, lingotti) + stato fonderia. Salva anche l'orario UTC: **la costruzione della fonderia avanza a gioco chiuso** (progresso offline); orologio spostato all'indietro ⇒ nessun progresso
  - `Assets/Scripts/Core/SaveManager` — autosalvataggio ogni 10 s + su pausa app (mobile) e uscita; caricamento in `GameBootstrap.Awake` prima della creazione delle schermate
  - Menu editor **EvolvingSpace → Cancella salvataggio** per ripartire da zero nei test

- **Decisa la progressione degli upgrade** (vedi [decisioni.md](decisioni.md) e la sezione "Progressione e potenziamenti" del GDD): torretta automatica → upgrade → reattore → FUEL come cap offline → raggio traente → viaggio 1b
- **Torretta automatica livello 1 implementata** (primo sink dei lingotti):
  - Acquisto dal popup della **sala comandi** (5 lingotti, stessa grammatica del popup fonderia); pulsante attivo solo con abbastanza lingotti, "Installata ✓" dopo l'acquisto
  - `ShipState.AutoTurret` + `TryBuyAutoTurret`; salvata nel salvataggio (campo nuovo, i salvataggi esistenti restano validi)
  - In mining: 1 colpo ogni 2,5 s sul primo asteroide in campo (registro statico `Asteroid.Active`), volutamente molto più lenta del tap (~3 colpi/s); la torretta cambia livrea (azzurrina) quando è automatica
  - Per ora non consuma nulla: il costo in FUEL arriverà con la riparazione del reattore

- **Upgrade torretta implementati** (nel pannello sala comandi, visibili dopo l'installazione):
  - Velocità di fuoco Lv 0–5: 1 colpo/2,5 s → 1 colpo/1,25 s; Danni Lv 0–5: 5 → 15 per colpo (valgono anche per il tap manuale: è la stessa torretta)
  - Costi crescenti: 3, 6, 9, 12, 15 lingotti per livello, per linea
- **Reattore → FUEL → mining offline implementati** (lo snodo centrale della progressione):
  - Tap sul reattore → popup "Ripara (10 lingotti · 60 s)"; barra progresso; nocciolo che passa da ciano fiacco a verde vivace quando è online
  - A reattore attivo produce **1 FUEL ogni 20 s, solo online** (offline il serbatoio si svuota e basta — il cap resta un cap); serbatoio da 30 FUEL; barra risorse ora mostra `FUEL n/30`
  - **Mining offline**: alla riapertura, con torretta installata e reattore riparato (al momento del salvataggio), la torretta ha minato per min(tempo passato, FUEL disponibile) — 1 FUEL = 1 minuto. La resa simula il ritmo reale delle ondate (colpi per asteroide, 2 asteroidi, tempo di raccolta), così l'offline resta meno redditizio del gioco attivo, come da regola fissa
  - Popup **"Rapporto di bordo"** all'avvio con ferro minato e carburante consumato
  - Salvataggio esteso (v2, retrocompatibile: i salvataggi vecchi caricano con reattore rotto e livelli a 0)

- **Recupero di bordo lento** (fix coerenza idle, decisione in [decisioni.md](decisioni.md)): i minerali derivano verso il caccia a 0,15 u/s (~30-40 s), calamita rapida nell'ultimo tratto. L'idle online ora cicla davvero (prima si bloccava dopo un'ondata: lo spawner attendeva il campo libero); la formula offline usa lo stesso modello (~35 s di raccolta per ondata, prima 6,8 s — era troppo generosa). Il raggio traente futuro potenzierà velocità/portata della deriva

- **Revisione grafica della schermata mining** (22/07): ondate portate a **4 asteroidi** più piccoli (scala 0,9–1,2 invece di 1,4–1,9; formula offline allineata); nave ridotta (~1,25 unità di larghezza) e **sprite del caccia ridisegnata** — pixel art 19×27 disegnata pixel per pixel (mappa caratteri in `PlaceholderSprites.ShipRows`): muso affusolato, abitacolo con cupola e riflesso, ali a freccia con punte rosse, doppia gondola motori con **due fiamme** indipendenti (`ShipVisuals`)

- **Raggio traente visibile implementato** (ridisegnato su proposta del 22/07 — vedi [decisioni.md](decisioni.md)):
  - **Fascio visibile**: cono verde-acqua pulsante dalla pancia della nave (`TractorBeam`), che aggancia **un minerale alla volta** (il più vicino) e lo traina a bordo; sostituisce la deriva ambientale invisibile
  - Upgrade **forza di trazione** Lv 0–5 (pannello sala comandi, riga sempre visibile): velocità di traino 0,8 → 2,8 u/s; costi 4, 8, 12, 16, 20 lingotti; predisposto per i futuri materiali pesanti (velocità = forza/massa, forza minima per sollevarli)
  - Simulazione offline resa sequenziale (minerali per ondata × distanza/forza); il trascinamento manuale resta parallelo e gratuito
  - Livello incluso nel salvataggio (retrocompatibile)

- **Prima build Android** (`Assets/Editor/AndroidBuilder.cs`, menu **EvolvingSpace → Build APK Android** o batch): APK IL2CPP/ARM64, portrait, debug key, package `com.evolvingspace.game`; compila in `Builds/` e copia in `G:\Il mio Drive\APK`. Fix al primo tentativo: la `SampleScene` del template era rimasta in posizione 0 delle Build Settings (schermata blu sul telefono) — rimossa, ora `Game.unity` è l'unica scena; il generatore di scena la imposta come sola scena in build

- **Pulsante reset** in mining: quadrato rosso con la X accanto a "INTERNO NAVE" → popup di conferma ("Annulla"/"Resetta") → cancella il salvataggio, azzera lo stato e ricarica la scena (`SaveSystem.ResetAll`)
- **Raggio traente ora va installato** (4 lingotti, pannello sala comandi, come la torretta): finché non è costruito il fascio non parte e i minerali si raccolgono solo a mano; niente resa offline senza raggio. Migrazione: i salvataggi con livelli già comprati lo considerano installato

- **Auto-updater (stadio A) implementato**:
  - `AndroidBuilder` ora incrementa la versione a ogni build (versionCode + versionName `0.<code>`), scrive `Builds/version.json` e pubblica APK + json come **release GitHub** su `s-ricci/evolvingspace-builds` via `gh` CLI (URL stabile `releases/latest/download/...`)
  - `UpdateChecker` (in `GameBootstrap`): all'avvio su dispositivo scarica il version.json remoto; se più nuovo → popup "Aggiornamento disponibile" con **Scarica** (apre il download; installazione sopra la vecchia conserva i salvataggi) / "Più tardi". Silenzioso se offline o senza release
  - Permessi risolti: `gh` ora autenticata come `s-ricci` (ADMIN sul repo)
- **Documentazione online**: i tre Markdown (GDD, decisioni, stato) sono leggibili direttamente su **github.com/s-ricci/evolvingspace-builds** (con README indice), e `documentazione.html` è la versione a pagina unica su **https://s-ricci.github.io/evolvingspace-builds/** (GitHub Pages). `Tools/publish-docs.ps1` pubblica tutto insieme e tiene le due versioni allineate

- **Registro delle idee creato** (23/07): le proposte del documento Google condiviso vengono valutate in [idee.md](idee.md) e finiscono in roadmap o tra le scartate (con motivazione). Prime 11 idee importate e valutate: tutte in roadmap
- **Decisione presa: sistema energetico** (23/07, vedi [decisioni.md](decisioni.md)): deuterio → reattore → energia → batterie. Il FUEL sparisce (unica valuta energetica), i sistemi consumano energia anche online, offline il reattore è in standby e le batterie sono il cap (iniziale ~12–24 h). Sostituisce l'upgrade serbatoio in roadmap; migrazione salvataggi FUEL → energia
- **Fonderia comoda implementata** (tappa 1 della roadmap, 23/07): pulsante **X** in alto a destra che riporta alla nave (grigio-blu, per non confonderla con la X rossa del reset in mining) e pulsante **"PRODUCI TUTTI (×N)"** sotto la ricetta, che converte in un colpo tutto il ferro disponibile in lingotti (attivo solo con ≥ 3 minerali, mostra quanti lingotti produrrà). Il **"Crafta tutto"** globale è rinviato a quando ci saranno più materiali (deuterio): con una sola ricetta coinciderebbe col "produci tutti"
- **Sistema energetico implementato** (tappa 2 della roadmap, 23/07 — numeri nel [GDD](GDD.md)):
  - **Asteroide di deuterio**: 25% di probabilità per ondata che uno dei 4 asteroidi sia di deuterio (roccia bluastra, venature ciano); droppa 3 minerali di deuterio trascinabili/trainabili come il ferro
  - **`EnergySystem`** (nuovo, tick in `GameBootstrap`): il reattore riparato brucia deuterio (1 = 10 energia) e produce 6–16 energia/min; l'energia alimenta torretta automatica (1 E/min) e raggio traente (0,5 E/min), l'avanzo carica le batterie; a batterie piene copre solo i consumi. Senza energia i sistemi automatici si fermano; tap e trascinamento manuali sempre gratis (anti-softlock)
  - **Upgrade reattore** (produzione, Lv 0–5, 5-25 lingotti, solo a reattore riparato) e **batterie** (capacità 1080→3780 = 12→42 h offline, Lv 0–5, 4-20 lingotti) nel pannello sala comandi
  - **Offline**: reattore in standby, i sistemi attingono solo dalle batterie — minuti = min(tempo passato, energia/1,5); resa con quota deuterio; "Rapporto di bordo" esteso (ferro, deuterio, energia consumata)
  - **Barra risorse a tre voci**: `IRON · DEUT · ENERGIA n/cap`; FUEL eliminato ovunque
  - **Salvataggio v3** con migrazione: 1 FUEL → 36 energia (serbatoio pieno = batteria Lv 0 piena), salvataggi v2 compatibili

- **Build v0.3 pubblicata** (23/07, sera): prima build con fonderia comoda + sistema energetico; release su GitHub con APK e version.json, copia su Drive. Testata in editor e **sul telefono: tutto ok** (auto-update, migrazione FUEL, touch)

- **Direttive 12-13 dal Google Doc recepite** (23/07, sera — registrate in [idee.md](idee.md), decisioni in [decisioni.md](decisioni.md)): dopo il playtest della v0.3 si è deciso il **passo indietro sull'energia**. Chiusa anche la decisione aperta sulla grammatica UI: gli upgrade restano nel pannello della sala comandi
- **Sistema energetico rimosso** (direttiva 12): via `EnergySystem`, deuterio (asteroide e minerale), riparazione/upgrade del reattore e batterie; la stanza del reattore resta nella piantina come flavor ("Spento. Per ora la nave non ne ha bisogno."). **Salvataggio v4** con migrazione: i campi energetici dei v2/v3 decadono senza risarcimento, tutto il resto sopravvive
- **Deposito a carico del raggio traente** (direttiva 12): il grezzo non entra più in magazzino ma resta in un deposito con limite in **unità di carico** (ferro = 1 unità; base 1000, upgrade "capacità" Lv 0–5 → 3500, costi 4-20 lingotti — 4ª linea del pannello sala comandi, tutte cappate a Lv 5 = tier 1). A deposito pieno raggio fermo e raccolta bloccata ("Deposito pieno!"), si fonde per liberare peso. Il **magazzino è senza limite ma solo lingotti** (ora si vedono i lingotti sui pallet). Barra risorse: `IRON n · CARICO peso/max` (arancio a pieno). L'offline si ferma a deposito pieno, con tetto a 24 h (idea 1); rapporto di bordo aggiornato
- **Schermata mining dinamica** (direttiva 13): stelle che pulsano e **scorrono verso il basso** (parallasse: le più luminose più veloci — la nave "avanza"); `FallingAsteroidSpawner` al posto delle ondate statiche: asteroidi random dalla cima (uno ogni 3,5–6,5 s, max 5 in campo), deriva laterale leggera, discesa costante 0,4 u/s, **despawn in fondo** (ferro perso); il laser automatico aggancia solo asteroidi interamente in campo sotto un margine d'ingresso (0,5 u) e dà priorità al più basso; minerali droppati **più piccoli** (scala 1,05 vs 1,5) che "scappano" dall'esplosione (1,6–2,4 u/s smorzati subito, catturabili dal raggio)
- Verifica: **compilazione batch Unity pulita** (0 errori, 0 warning). Da provare in editor e su telefono

- **Prima grafica vera** (23/07, sera — 7 PNG pixel-art forniti via `Assets/downloads`, ora in `Assets/Resources/Sprites`):
  - **6 varianti di asteroide** (`asteroid_iron_01..06`) scelte a caso a ogni spawn (`GameSprites.RandomAsteroid`); restano la rotazione a velocità random sobria (8–22°/s, verso casuale), lo scale-in, lo scurirsi col danno
  - **Caccia `ship_level1`** (528×634) normalizzato alle dimensioni del segnaposto; **motori accesi** sui due ugelli veri della sprite: fiamma arancione che guizza + nucleo chiaro + alone caldo (`ShipVisuals` rifatta)
  - **Esplosione potenziata** (`Debris.Burst`): lampo caldo + onda d'urto che si espande + 10 schegge nei toni di roccia dei PNG + 5 braci arancioni; nuovo sprite procedurale `Glow` (disco morbido) in `PlaceholderSprites`
  - Infrastruttura: `GameSprites` (Core) carica i PNG da `Resources/Sprites`, li normalizza in unità mondo (asteroide largo 1, caccia 1,19) e **ripiega sui segnaposto se un file manca**; `Assets/Editor/SpriteImportSettings` forza l'import pixel-art (filtro Point, no mipmap, non compresso)

- **Rifiniture su feedback** (23/07, sera — seconda passata):
  - **Torretta** più in basso (dentro lo scafo) e **dietro la sprite della nave**: spunta solo la punta della canna che ruota quando prende la mira
  - **Spawn distanziati**: il nuovo asteroide nasce ad almeno 1,4 unità dagli altri (10 tentativi, poi la posizione migliore)
  - **Minerali in fuga radiale**: ventaglio regolare (~120° con jitter) in direzione opposta all'esplosione, velocità ridotta (1,2–1,7 u/s smorzate); poi **non si fermano**: scendono "col cielo" alla velocità del mondo (0,4 u/s) verso la nave — al contatto col caccia si raccolgono da soli, se lo mancano escono in fondo (persi, come gli asteroidi ignorati)
  - **Stelle**: luccichio più marcato (base fioca + picchi luminosi netti, curva quadratica)
  - **Deposito in unità di carico**: capacità base **1000 unità** (Lv 0–5 → 3500); 1 ferro = 1 unità, 3 unità di ferro = 1 lingotto (invariato)
  - **Fonderia**: lista dei craft ancorata **in alto** (le prossime ricette si accoderanno sotto)
  - **Laserata continua** (terza passata): il colpo non è più un impulso — il laser resta acceso **1 secondo pieno**, aggancia e segue il bersaglio, spalma il danno in 5 tocchi (scintille + micro-shake a ogni tocco), fascio che trema e torretta che vibra; nuovo **ronzio sintetizzato da 1 s** (`ProceduralAudio.PlayLaserBeam`, sorgente dedicata che si ferma se il bersaglio esplode in anticipo). Mentre spara la torretta è occupata (tap ignorati). **Nota bilanciamento**: il ritmo manuale passa da ~3 tap/s a 1 laserata/s a parità di danno — da valutare col playtest se ritoccare danni o HP

**In corso:**
- Test in editor/telefono delle novità del 23/07 sera: deposito a peso (pieno → fonde → riparte), schermata dinamica (ritmo di spawn e despawn), migrazione dei salvataggi v3 con energia, offline con i nuovi limiti
- Poi build **v0.4** da pubblicare (menu EvolvingSpace → Build APK Android)

**Prossimo passo (roadmap rivista il 23/07 sera — dettagli in [idee.md](idee.md)):**
1. ~~**Fonderia comoda**~~ ✓ fatta (23/07)
2. ~~**Sistema energetico**~~ ✓ fatto e **ritirato lo stesso giorno** (direttiva 12: sostituito dal deposito a peso; reinseribile in futuro)
3. ~~**Schermata mining dinamica**~~ ✓ fatta (23/07 sera, direttiva 13 — anticipa la parte visiva della 1b)
4. **Viaggio (1b completa) + mappa stellare**: barra velocità, mappa con punti di interesse; raggi traenti multipli (la decisione sulla grammatica UI è chiusa: pannello sala comandi)
5. **Mercantili e valuta "Cookie"**: incontri casuali, compravendita a prezzi variabili
6. **Prima stazione spaziale**: hub con missioni (→ Cookie → sblocco Tier), commercio, hangar, nuovi minerali (spawn 0% fino all'arrivo, pesi crescenti), stanze costruibili (sala mappe)
