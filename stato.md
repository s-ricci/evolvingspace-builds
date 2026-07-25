# EvolvingSpace — Stato dei lavori

> Documento vivo: aggiornare a ogni sessione di lavoro (cosa è fatto, cosa è in corso, prossimo passo).

## Aggiornato al 2026-07-25 (sera — v0.11)

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
  - **Laserata continua** (terza passata): il colpo non è più un impulso — il laser resta acceso **1 secondo pieno**, aggancia e segue il bersaglio, spalma il danno in 5 tocchi (scintille + micro-shake a ogni tocco), fascio che trema e torretta che vibra; suono sintetizzato da 1 s su sorgente dedicata (si ferma se il bersaglio esplode in anticipo). Mentre spara la torretta è occupata (tap ignorati). **Nota bilanciamento**: il ritmo manuale passa da ~3 tap/s a 1 laserata/s a parità di danno — da valutare col playtest se ritoccare danni o HP

- **Asset dello store + colonna sonora** (23/07, notte):
  - **Sfondo "skybox"**: la pipeline è URP col renderer 2D (niente skybox di camera), quindi la faccia frontale dello **Starfield Skybox** (asset store) copre lo schermo come fondale dietro le stelle procedurali, leggermente scurita per leggibilità (copia in `Resources/Background/skybox_stars.png`; se manca si torna al colore pieno)
  - **Colonna sonora**: `Dust and Static.mp3` in loop dal `GameBootstrap`, volume basso (0,13) per lasciare in primo piano laser e raggio traente (file in `Resources/Audio/`)
  - **Laserata: fascio a due strati** col materiale "pieno" del pacchetto spell (`M_BeamParticles_6000` in `Resources/VFX/`, fallback Sprites/Default): esterno con **colore modulato in stile "flowing"** — base rossiccia alla bocca, banda calda che scorre lungo il raggio verso il bersaglio (~2 giri/s), punta aranciata — nucleo quasi bianco, bagliori di bocca e d'impatto; materiale opaco ⇒ fascio a z negativa (sopra le sprite via depth). *Nota: si era provato il prefab `VFX_Flowing_Beam_Main` del pacchetto (un'istanza per laserata, emitter fuori asse spenti, piano di collisione sul bersaglio) ma il getto in particelle restava difficile da domare in 2D — scartato, tenuta solo la sua palette*
  - **Suono del laser più acuto** (terza revisione): portante 880 → **1500 Hz**
  - **Ronzio del raggio traente**: loop sintetizzato senza giunzioni (210/316/424 Hz, battito a 3 Hz) su sorgente dedicata, parte solo mentre sta trainando, volume 0,1

- **Build v0.4 pubblicata** (23/07, notte): release su GitHub (APK 43 MB + version.json) e copia su Drive. Contiene tutto il lavoro della giornata: rimozione energia, deposito a carico in unità, schermata mining dinamica, grafica vera (asteroidi/nave PNG, fondale skybox), laserata da 1 s con colore "flowing", musica in loop, suoni rivisti

- **Interno nave a lista di moduli** (23/07, notte — direttive 14-16, dopo la v0.4):
  - `ShipInteriorScreen` riscritta: **overlay a canvas opaco** con la lista dei moduli costruiti (sala comandi, magazzino, motore a impulso, laser minerario + fonderia e raggio traente quando costruiti), tap sul modulo → pannello di dettaglio/upgrade; in fondo **"COSTRUISCI MODULO"** (fonderia gratis · 30 s; raggio traente 4 lingotti · 20 s), righe con conto alla rovescia durante la costruzione
  - **Upgrade per modulo**: il deposito del grezzo passa al **magazzino** (stesso sistema a unità, 5 livelli); il laser minerario ha velocità/danni (5 livelli) + **"IA" a 1 livello** (ex torretta automatica, 5 lingotti — velocità e danni ora acquistabili anche senza IA); **nuovo motore a impulso** (5 livelli, 5-25 lingotti): più velocità ⇒ spawn fino a ×2,5, mondo (discesa/stelle) fino a +40%, max asteroidi in campo 5+livello. La sala comandi resta senza upgrade (è il ponte, non il negozio)
  - **Fix segnalazione 15**: la schermata mining è **sempre attiva** — interno e fonderia sono overlay; laser IA, raggio e spawner continuano a lavorare nei menù e la barra risorse si aggiorna in diretta
  - **Salvataggio v5**: raggio traente come modulo con stato/tempo di costruzione, livello motore; migrazione v4 ("installato" ⇒ costruito); offline aggiornato (spawn scalato dal motore, richiede IA + raggio costruito)
  - **Musica a 0,18** (direttiva 16); compilazione batch pulita (0 errori; solo warning di deprecazione negli script del pacchetto spell)

- **Menù impostazioni** (23/07, notte — direttiva 17):
  - In mining la **X rossa è sostituita da un'icona impostazioni** (tre slider stilizzati, sprite procedurale) **tutta a destra**; tap → popup "IMPOSTAZIONI"
  - In alto le due opzioni audio: **slider volume musica + MUTA** e **slider volume effetti + MUTA** (nuovo `GameAudioSettings` su PlayerPrefs: le preferenze sopravvivono al reset del salvataggio; musica, laserata e ronzio del raggio si riallineano in diretta)
  - In fondo il **tasto rosso "RESETTA IL GIOCO"** che apre la solita conferma (il reset non è più a portata di pollice in gioco)
  - Nuovi `UiKit.MakeSlider` e `SettingsPanel`; creato **[prompt-gemini.md](prompt-gemini.md)** in Docs con i tre prompt richiesti (interno nave a lista, pannello modulo con upgrade, menù impostazioni)

- **Design system dai mockup Gemini** (23/07, notte — i tre mockup generati con [prompt-gemini.md](prompt-gemini.md) sono in `Docs/mockups/`):
  - Nuovo **`UiTheme`** (palette condivisa: sfondo blu notte, card `#1C2330` bordate `#30363D`, titoli oro `#F0B429`, testo secondario grigio-azzurro, verde/grigio per gli upgrade, ambra per "in costruzione") e nuovi mattoni in `UiKit`: **card bordate** e **popup in stile mockup** (pannello bordato + X centrata sul bordo inferiore)
  - **9 icone pixel-art ritagliate dai mockup** (`Resources/Sprites/Icons`): sedia, cassa, motore, laser, fonderia, raggio + cronometro, esplosione, chip
  - **Interno nave ristilizzato**: righe = card bordate con icona, nome e stato; riga **ambrata con nome oro** durante la costruzione; "COSTRUISCI MODULO" azzurro
  - **Pannelli dei moduli in stile mockup 2**: riga "LINGOTTI: N" con l'icona del lingotto sotto il titolo, card di upgrade con icona, descrizione dinamica, **badge livello**, costo con lingotto e pulsante **MIGLIORA verde/grigio** (anche le voci di costruzione usano le card, col badge che mostra il tempo)
  - **Impostazioni allineate al mockup 3**: manopola crema, tasto muto scuro/ambrato, reset rosso `#C43B33`; fonderia su sfondo tema

- **Build v0.5 pubblicata** (23/07, notte): release su GitHub (APK + version.json) e copia su Drive. Contiene: interno a lista di moduli col design system dei mockup, motore a impulso, mining sempre attivo nei menù, menù impostazioni, salvataggio v5

- **Fix offline su smartphone** (24/07, segnalazione 18): l'offline si calcolava solo all'avvio, ma su Android l'app viene sospesa e **ripresa**, non riavviata → al rientro non veniva conteggiato nulla. Ora `SaveManager` fa il **catch-up alla ripresa**: ricarica lo stato salvato in pausa (costruzioni + mining offline riapplicati) e mostra il Rapporto di bordo; guardia contro il "resume" fasullo del primo avvio Android

- **Rifiniture del 24/07**: il popup "Costruisci modulo" **non mostra più i moduli già costruiti** (le voci rimaste risalgono; "Nessun nuovo modulo disponibile" quando la lista è vuota); [prompt-gemini.md](prompt-gemini.md) esteso con i prompt di **fonderia** e **pagina principale** (dal secondo si adottano solo HUD/menù)

- **UI aggiornata coi mockup 4 e 5** (24/07 — in `Docs/mockups/`):
  - **Barra risorse** in stile mockup 5: bordata, con **icona lingotto + conteggio lingotti a sinistra** e **icona cassa + carico grezzo/capacità a destra** (niente più etichette testuali; il carico si tinge d'arancio a pieno). Vale per tutte le schermate
  - **Pulsante "INTERNO NAVE" azzurro** con l'icona della navicella (ritagliata dal mockup); impostazioni invariata a destra
  - **Fonderia in stile mockup 4**: ricetta in **card bordata** con le icone vere di **grezzo e lingotto** (ritagliate dal mockup), freccia e **CRAFT rossi** `#C43B33`, "PRODUCI TUTTI" rosso a tutta larghezza, X e "TORNA ALLA NAVE" scuri da tema
  - Le icone lingotto dei pannelli interni (header "LINGOTTI:" e costi) ora usano l'icona ritagliata; 5 nuovi ritagli in `Resources/Sprites/Icons` (ore, ingot, hud_ingot, hud_crate, ship)

- **Build v0.6 pubblicata** (24/07): release su GitHub e copia su Drive. Contiene: **fix dell'offline alla ripresa dell'app** (segnalazione 18), popup costruzione senza i moduli già costruiti, UI aggiornata coi mockup 4-5 (barra risorse lingotti/carico con icone, INTERNO NAVE azzurro con navicella, fonderia a card con CRAFT/PRODUCI TUTTI rossi)

- **v0.6 testata su telefono: tutto ok** (24/07). Feedback dal playtest: in ~24 h si raggiunge il cap di tutti gli upgrade (Lv 5 tier 1) — il contenuto attuale "finisce"
- **Decisa la progressione a lungo termine** (24/07, tre decisioni confermate — vedi [decisioni.md](decisioni.md) e il [GDD](GDD.md)): gerarchia a quattro piani (livelli 1–5 → tier **"Mk"** per modulo, all'hangar, gated dalle stazioni → **milestone nominate** → universi); **campi di asteroidi discreti** con tabelle di spawn e doppio gating (tier del laser per rompere, forza/massa del raggio per trainare); **viaggio a tempo reale** sulla mappa con mining in rotta ("spazio aperto"), avanzamento offline, mercantili come incontri in rotta

- **Mockup 6-12 ricevuti** (24/07, da [prompt-gemini.md](prompt-gemini.md)) e **tappe 3-4-5 implementate in un colpo solo** su richiesta, con quattro decisioni chiuse prima di partire (vedi [decisioni.md](decisioni.md)): nome **ARGO** confermato, stazione rivelata dal **primo mercantile**, **rame = valuta dei livelli Mk II**, **sala mappe inclusa**
- **Tappa 3 — Mappa stellare + viaggio** (`Assets/Scripts/Travel/`): `StarMap` (campi con tabelle di spawn: partenza, cintura densa, vena ricca con asteroidi grossi HP×2/drop×2, stazione, campo del rame 65/35 — numeri nel [GDD](GDD.md)), `TravelSystem` (rotte a tempo reale: 3 UA/min +20%/lv motore; in rotta tabella "spazio aperto"), `StarMapScreen` (mappa in stile mockup 6: POI, nave, rotta a puntini col tempo, popup destinazione con barre di composizione — "???" se campo non visitato e senza sala mappe), banner di viaggio e **pulsante mappa** in mining (mockup 8); lo spawner legge densità/mix/taglia dal campo corrente
- **Tappa 4 — Mercantili e Cookie**: `TradePopup` (mockup 9) con prezzi casuali a ogni incontro, quantità a stepper, compra/vendi; primo incontro garantito in rotta che **rivela la Stazione Argo**, poi ~1 rotta su 2; valuta **Cookie** in `GameResources` e terza voce della barra risorse (compare quando la valuta entra in gioco)
- **Tappa 5 — Stazione Argo** (`StationScreen`, mockup 10-12): hub con immagine ritagliata dal mockup e tre servizi; **missioni** in catena (2 attive alla volta: consegna 50 lingotti, 30 asteroidi in Cintura Densa, vendi 20 lingotti ai mercantili, 60 ferro nella Vena Ricca → 70 Cookie totali); **commercio** a prezzi fissi; **hangar** con evoluzioni **Mk II** (laser 40 · raggio 35 · magazzino 30 Cookie): il laser Mk II rompe gli asteroidi di **rame** (40 HP, grezzo da 2 unità trainato a metà velocità), il raggio Mk II ha il **secondo fascio**, i livelli 1–5 riaperti si pagano in **lingotti di rame** (ricetta nuova in fonderia, con "PRODUCI TUTTI" su entrambe); **sala mappe** costruibile (8 lingotti · 30 s) che rivela la composizione dei campi
- **Salvataggio v6** (retrocompatibile) e **offline esteso**: campo corrente, rotta in corso (avanza a gioco chiuso, arrivo e attracco compresi), campi visitati, Cookie/rame/Mk/missioni/sala mappe; mining offline in due fasi (spazio aperto lungo la rotta + campo d'arrivo, col mix ferro/rame); Rapporto di bordo esteso (arrivo, ferro, rame)
- **7 icone nuove ritagliate dai mockup** (cookie, mercantile, stazione, missioni, commercio, hangar, mappa) in `Resources/Sprites/Icons`
- **Compilazione batch pulita** (0 errori, 0 warning di progetto) e **build v0.7 pubblicata** (24/07): APK su release GitHub + copia su Drive

- **Fix su revisione della v0.7** (24/07, sera — tre direttive, decisioni in [decisioni.md](decisioni.md), numeri nel [GDD](GDD.md)):
  1. **Asteroidi densi**: roccia scura da 45 HP che droppa 5 minerali, corazza che chiede ≥ 9 danni per colpo (danni Lv 2); la densità dei campi diventa un mix spawn+densi (Cintura Densa: ×1,25 e 35% densi; Campo del Rame: 20% sul ferro) — lo schermo non si affolla ma la resa cresce
  2. **Viaggio senza mining**: in rotta niente spawn e laser a riposo; la nave si porta al centro della visuale coi motori spinti (fiamme ×2) e le stelle sfrecciano (×5); i **mercantili sono rari** (~1 su 5, primo garantito) e si **avvistano** col banner "Mercantile in avvicinamento…" — il tap devia e **mette in pausa il timer di viaggio**, che riprende chiudendo il banco; il primo **abbordato** rivela Argo
  3. **Stazione a baratto**: il commercio di Argo scambia solo lingotti (4 ferro → 1 rame · 1 rame → 2 ferro), niente Cookie — che restano ai mercantili e alle missioni
  4. **Mappa alleggerita e trascinabile**: area virtuale ~860×1500 esplorabile col drag (il tap sui campi resta tap), centratura automatica sul campo corrente all'apertura, cluster più piccoli, via le didascalie (restano nel popup), nomi in chip scure semitrasparenti, pulsante "TORNA AL MINING" in fondo
  - Offline coerente (in rotta non si mina nemmeno da chiusi; densi/rame contano solo se rompibili); compilazione batch pulita (0 errori, 0 warning)

- **Build v0.8 pubblicata** (24/07): release su GitHub (APK + version.json) e copia su Drive. Contiene tutte le tappe 3-5 della v0.7 più i fix della revisione: asteroidi densi, viaggio senza mining con mercantili a deviazione (pausa del timer), stazione a baratto, mappa trascinabile

- **v0.8 testata** (24/07, sera): funziona, ma **playtest impietoso sul pacing** — tutto maxato in 1 ora anche partendo da zero e senza mai barattare. Diagnosi: costi aritmetici in un genere a costi esponenziali + baratto verso l'alto concettualmente sbagliato. **Ribilanciamento in discussione** (decisione aperta in [decisioni.md](decisioni.md)): costi a raddoppio per livello, baratto solo verso il basso, rame 80 HP, evoluzioni più care, mercantili proporzionali alla rotta, niente limiti giornalieri. Nessuna modifica senza consenso

- **Ribilanciamento v0.9 implementato** (24/07, notte — pacchetto approvato + estensione IA/fonderia, decisione in [decisioni.md](decisioni.md), numeri nel [GDD](GDD.md)):
  - **Livelli 1-10** con costi esponenziali ~×1,6 (laser 3→125, altre linee 4→165 per livello); al Mk II le stesse tabelle in lingotti di rame; migrazione proporzionale (Lv 3/5 → Lv 6/10)
  - **IA per minerale**: Lv 1 riconosce il ferro (5 lingotti), Lv 2 il rame (10 lingotti di rame — fatti a mano per forza); senza IA Lv 2 la torretta ignora il rame anche col Mk II (tap libero, offline coerente)
  - **Calibrazione fonderia**: costruzione gratis, poi forno al ferro (10 grezzo) e forno al rame (25 grezzo) — il pulsante CRAFT diventa CALIBRA finché il forno non è tarato; ogni minerale nuovo va minato a mano la prima volta
  - **Baratto solo verso il basso** (1 rame → 2 ferro, via la direzione ferro → rame), **rame a 80 HP**, **evoluzioni a 60/80/70 Cookie**, **mercantili proporzionali alla rotta** (~10%/min, primo garantito), nessun limite giornaliero
  - **Salvataggio v7** retrocompatibile; compilazione batch pulita (0 errori, 0 warning)

- **Build v0.9 pubblicata e test superato** (24/07, notte): release su GitHub + Drive; il ribilanciamento regge al playtest — livelli 1-10 esponenziali, IA e fonderia per minerale, baratto verso il basso, mercantili proporzionali alla rotta

- **19 direttive nuove dal Google Doc valutate e recepite** (24/07, dopo la v0.9 — idee **19-37** in [idee.md](idee.md), otto decisioni chiuse in [decisioni.md](decisioni.md)). La revisione ha trovato quattro incongruenze, tutte risolte prima di scrivere codice:
  1. **Il rubinetto dei Cookie si chiudeva mentre il prezzo saliva** (tier da 210 → 1500 Cookie complessivi, con la vendita dei lingotti eliminata e una catena di sole 4 missioni da 70 Cookie): le **missioni diventano ripetibili e randomizzate**, con la bacheca di Argo che si rinnova a ogni attracco e un incarico per mercantile abbordato
  2. **"Limitare quanto compra il mercante" vs "eliminare la vendita dei lingotti"**: vince la seconda — i mercantili non comprano più nulla, vendono e danno incarichi; la missione "Rotta commerciale" (vendi 20 lingotti) va sostituita e migrata
  3. **I Cookie che comprano lingotti riaprivano il baratto verso l'alto** chiuso in v0.9: prezzo fissato al doppio del valore equo (10 Cookie il lingotto di rame) e regola permanente che non scenda mai sotto il valore in equivalenti
  4. **Sforzo e premio erano invertiti** nelle missioni di esempio (200 lingotti di ferro = ~200 asteroidi per 150 Cookie, contro 30 asteroidi per 200): tutte le missioni ora si pagano in **equivalenti asteroide**, 0,75-1,00 Cookie l'uno, tetto 200, cifre tonde
- **Decisioni prese in questa sessione** (dettagli e motivazioni in [decisioni.md](decisioni.md), numeri nel [GDD](GDD.md)): economia dei Cookie · missioni in equivalenti asteroide · **Sala comunicazioni** (nuovo modulo: slot missioni 2→5, consegna a distanza, +3% ricompense per livello) · **fonderia a livelli per minerale** (via le calibrazioni: Lv 1 = costruzione a 30 ferro grezzo, Lv 2 = 60 rame grezzo; le ricette dei minerali scoperti compaiono anche se bloccate) · **cadenza unica del laser** (timer del fascio, tap con prelazione che ridirige il fascio e punta anche l'IA, IA con 0,4 s di reazione) · **partenza a razzo** (laser e raggio staccati, campo che resta indietro, mondo ×8) · **due campi nuovi** (Vena mista 50/50, Filone di rame 20/80) · **relitti esplorabili = milestone in fondo alla roadmap**, insieme agli oggetti come ricompensa · tetto agli asteroidi grossi **rinviato**

- **Idee 39-40 registrate** (25/07, direttiva): **changelog delle versioni** e **strumenti di sviluppo** nelle impostazioni — nuova **tappa 4** della roadmap, subito dopo la mappa a sistemi solari. I tre punti aperti sono stati **chiusi in giornata** (decisione in [decisioni.md](decisioni.md)): pannello **"SVILUPPATORE" separato** in fondo alle impostazioni per le risorse (il changelog resta un tasto normale), **"»" verde dentro la scheda** di viaggio e di costruzione per saltare l'attesa, risorse di test **sempre disponibili senza gate**

- **[changelog.md](changelog.md) creato** (25/07): storico delle 8 release pubblicate (v0.2 → v0.9) ricostruito da questo documento, scritto per chi gioca. È la **fonte del changelog in gioco** (idea 39); in gioco si mostreranno solo le ultime 10 versioni. Pubblicato anche sul repo delle build e nella pagina unica. **Regola nuova: una voce va scritta a ogni build, prima di pubblicare la release** — l'aggancio ad `AndroidBuilder` si fa con la tappa 4

- **Tappe 1-4 della roadmap implementate in sequenza** (25/07): tutto quello che era rimasto sulla carta dalle idee 19-40 è ora codice. Quattro blocchi:

  **Tappa 1 — pacchetto rifiniture** (idee 20-26, 29, 30, 32):
  - **Cadenza unica del laser**: un solo timer (durata del fascio + 0,12 s di respiro) per tap e IA; la linea "velocità di fuoco" ora **accorcia il fascio** (1,00 → 0,55 s ai Lv 1-10, Mk II 0,55 → 0,35) e si vede su entrambi. Il **tap ha la prelazione** e a fascio acceso lo **ridirige**; l'IA aspetta **0,4 s di reazione** e **punta il bersaglio scelto col tap** finché è vivo (idea 24). Resa offline ricalcolata sulla nuova cadenza (`ShipState.AiShotInterval`)
  - **Fonderia a livelli** (idea 32): via le calibrazioni — è un modulo come gli altri, Lv 1 = costruzione a **30 ferro grezzo · 30 s**, Lv 2 = **60 rame grezzo · 60 s**. Pannello suo (con "APRI IL FORNO"), ricette dei minerali scoperti sempre visibili e spente con "serve fonderia Lv N"
  - **Partenza a razzo** (idea 26): mondo **×8**, stelle allungate in scia con transizione morbida, laser e **raggio traente staccati** per tutta la rotta, il campo resta indietro
  - QoL: **barra risorse** con Cookie a sinistra e carico a destra (via i lingotti, idea 21); i **lingotti — ferro e rame — si leggono dove si spendono** (idea 22) e nel magazzino stanno a sinistra (idea 23); banco del mercante con **"+" a ripetizione e MAX** (idea 20, nuovo `RepeatPress`); impostazioni con **X grande e staccata dal reset** (idea 29); **zoom a pinch** sulla mappa (idea 30, nuovo `MapPanZoom` riusabile)

  **Tappa 2 — riassetto dell'economia** (idee 31, 33-37):
  - `Missions` riscritto: missioni **generate, ripetibili e randomizzate**, valore in **equivalenti asteroide** (0,75-1,00 Cookie/eq, tetto 200, cifre tonde). Quattro archetipi: consegna lingotti di ferro/rame, abbatti asteroidi in un campo, raccogli grezzo in un campo. **Bacheca di Argo che si rinnova a ogni attracco** (e solo lì: riaprire il pannello non la rigenera) e **un incarico per mercantile abbordato**
  - Nuovo modulo **SALA COMUNICAZIONI** (10 lingotti · 60 s, sbloccata dalla stazione): missioni seguite e **consegnate da bordo**, slot 2 → 5 (`2 + (Lv-1)/3`), ricompense **+3% per livello**
  - **Evoluzioni Mk II a 500 Cookie**; i mercantili **non comprano più lingotti**: vendono ferro grezzo (2-3 per Cookie) e **lingotti di rame** (12-15 Cookie, scorte limitate), ad Argo a **10 Cookie** — il doppio del valore equo, mai una scorciatoia. Baratto di stazione invariato (1 rame → 2 ferro)
  - Due campi nuovi: **Vena mista** (50/50) e **Filone di rame** (20/80); nuova vista a lista scorrevole delle missioni (`MissionListView`, condivisa tra stazione e sala comunicazioni)

  **Tappa 3 — mappa stellare a grafo di sistemi solari** (idea 38):
  - `StarMap` rifatto: **5 sistemi** (EOS, VESTA, ARGO, KORAX, THULE) come nodi, **7 tratte** come archi, i campi vivono *dentro* i sistemi con coordinate locali. Percorsi con **Dijkstra**: si viaggia da sistema a sistema, anche a **più salti**, e la durata è la lunghezza del percorso più l'avvicinamento al campo
  - `MerchantTraffic`: **5 mercanti** che percorrono i loro archi avanti e indietro con moto **deterministico dall'orologio UTC** — si muovono davvero anche a gioco chiuso, senza salvare nulla. Si **intercettano** passando loro vicino (raggio 3,5 UA); il primo incontro resta garantito a metà della prima rotta, perché deve rivelare Argo
  - `StarMapScreen` a **due viste**: il grafo dei sistemi (archi accesi sulla rotta, mercanti in movimento, alone sul sistema corrente) e il dettaglio di un sistema coi suoi campi. Il popup di destinazione dice ora la distanza in **salti**
  - Migrazione gratis: il sistema si deduce dal campo, i salvataggi vecchi restano validi

  **Tappa 4 — changelog e strumenti di sviluppo** (idee 39-40):
  - `ChangelogImporter` (editor) porta le **ultime 10 versioni** di [changelog.md](changelog.md) in `Resources/Text/changelog.txt`; gira da solo a ogni build. In gioco, tasto **CHANGELOG** nelle impostazioni con la lista scorrevole
  - Pulsante **SVILUPPATORE** separato in fondo alle impostazioni: +1000 lingotti di ferro, +1000 di rame, +1000 Cookie, sempre disponibili
  - Pulsantino verde **"»"** dentro la scheda dell'attesa: banner di viaggio e riga del modulo in costruzione (fonderia compresa, anche in upgrade)

- **Salvataggio v8** con migrazione: la "calibrazione" della fonderia diventa il suo **livello** (fonderia costruita ⇒ almeno Lv 1), le missioni della vecchia catena fissa **decadono** (compresa "vendi 20 lingotti", che non esiste più) e la bacheca si rigenera al primo attracco; sala comunicazioni salvata
- **Prova di fumo automatica** (`Assets/Editor/SmokeTest.cs`, menu **EvolvingSpace → Prova di fumo**): apre la scena in play mode e fa un giro completo (risorse, fonderia, tutte le schermate, viaggio interno, salto verso Argo, mercantile, missioni), fallendo se qualcosa lancia a runtime. La compilazione pulita non bastava: quasi tutta l'UI nasce da codice
- Verifica: **compilazione batch pulita** (0 errori, 0 warning di progetto) e **prova di fumo superata**

- **Rifiniture sulla v0.10 dopo il playtest in editor** (25/07, sera tardi — provate in locale prima di pubblicare, poi **build v0.11**):
  - **Barra risorse**: i Cookie si vedono sempre, anche a zero; icona e numero sono un gruppo affiancato che si stringe sul contenuto (prima il numero del carico finiva lontanissimo dalla sua cassa)
  - **Mappa stellare**: fondale skybox proprio (faccia sinistra del pacchetto, diversa da quella del mining); rotte **tratteggiate da centro-sole a centro-sole**; targhetta del nome che si stringe sul testo e sta più vicina alla stella; **nave accanto al nome** invece che sopra il sole — e non sparisce più (era un bug: entrando nella vista di un altro sistema veniva nascosta e nessuno la riattivava)
  - **Interno nave**: la lista si **ricompatta** (i moduli non ancora costruiti non lasciano più buchi) e **"+ COSTRUISCI MODULO"**, verde smorzato, sta in coda all'ultima riga
  - **Pannelli dei moduli**: via il totale dei lingotti in cima; il costo si legge `[icona] necessari/in magazzino`, allineato a destra, con le cifre compattate (12,3k · 123k · 12,3M) e in rosso quando non bastano
  - **Banner di rotta anche sulla mappa** (estratto in `TravelBannerUI`, condiviso col mining): destinazione, conto alla rovescia, "»" e deviazione verso il mercantile da qualunque schermata
  - **Sistemi non esplorati chiusi**: il tap apre la scheda di rotta ("SISTEMA NON ESPLORATO") invece della vista interna; il nome del sistema resta visibile (è una stella), il contenuto no
  - **Composizione dei campi** solo nel campo di casa o con la Sala Mappe: altrove "composizione sconosciuta", densità compresa
  - **All'ormeggio non si mina** (né online né offline): la pagina di attracco diventa la schermata di casa, via il tasto "RIPARTI", e in basso compaiono mappa stellare · interno nave · impostazioni. I pulsanti di ritorno dicono dove portano ("TORNA ALLA STAZIONE" / "TORNA AL MINING")

**In corso:**
- Playtest lungo del pacing sui giorni (tier 1 ~1-2 giorni, tier 2 ~3-5: il verdetto vero arriva col gioco quotidiano)
- Da validare col gioco vero: la nuova cadenza del laser (l'IA a Lv 1 passa da 2,5 s a ~1,1 s: l'effetto è auto-limitato dallo spawn), il rubinetto dei Cookie con le missioni ripetibili, e i tempi di viaggio sul grafo (il rubinetto era tarato sui ~7-10 minuti verso Argo)

> **Ripresa dei lavori (scritta il 25/07 a fine giornata):** il progetto è alla **v0.11**, pubblicata su GitHub e Drive; le tappe 1-4 della roadmap sono chiuse, la documentazione è online e allineata. La v0.11 è stata provata **in editor**, non ancora sul telefono: la prima cosa da fare è il **playtest sul dispositivo**, guardando in particolare l'**attracco** (è la nuova schermata di casa: i tre pulsanti in basso e il laser che deve restare zitto), la **mappa** (banner di rotta, sistemi non esplorati, tratteggi) e i **costi** nei pannelli dei moduli, visti finora solo a valori bassi.
>
> Prima di una build: `Assets/Editor/SmokeTest.cs` (menu **EvolvingSpace → Prova di fumo**, o `-executeMethod EvolvingSpace.EditorTools.SmokeTest.Run`) fa un giro completo in play mode e fallisce se qualcosa lancia. Attenzione: **la prova cancella il salvataggio locale** dell'editor (regala risorse di test e poi ripulisce) — se serve una partita di prova, rifarla dopo.
>
> Poi si riparte dalla roadmap qui sotto: il prossimo blocco di contenuto sono i **minerali nuovi** (silicio, titanio, alluminio), che la fonderia a livelli e il ciclo "a mano prima, automatico poi" sono già pronti ad accogliere.

**Prossimo passo (roadmap aggiornata il 25/07 — dettagli nel [GDD](GDD.md)):**
1. **Silicio, titanio, alluminio** coi Mk successivi (pesi crescenti), nuovi campi e nuove ricette — il ciclo "a mano prima, automatico poi" è già pronto ad accoglierli, e la fonderia a livelli lo rende meccanico: un livello per minerale
2. **Altre stazioni** come checkpoint con gate di teletrasporto (nel grafo: **archi speciali**) e milestone successive; **intro a fumetto**; l'**universo 2** come capitolo massimo
3. **Milestone "Relitti"**: relitti di stazione esplorabili con minigioco, HP della tuta ed equipaggiamento — dopo tutto il resto
4. Da rivedere dopo il playtest della v0.10: taratura della banda 0,75-1,00 Cookie/eq se i tempi di viaggio sul grafo si rivelano diversi; eventuale **modulo sensori** per vedere le rotte dei mercanti da più lontano (oggi si vedono tutte dopo il primo incontro); tetto agli asteroidi grossi (idea 27, rinviata)
