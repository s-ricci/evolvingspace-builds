# EvolvingSpace — Stato dei lavori

> Documento vivo: aggiornare a ogni sessione di lavoro (cosa è fatto, cosa è in corso, prossimo passo).

## Aggiornato al 2026-07-24

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
  4. **Mappa alleggerita**: cluster più piccoli, via le didascalie (restano nel popup), nomi in chip scure semitrasparenti; se i campi cresceranno, il passo dopo è la mappa trascinabile
  - Offline coerente (in rotta non si mina nemmeno da chiusi; densi/rame contano solo se rompibili); compilazione batch pulita (0 errori, 0 warning). **Build v0.8 non ancora lanciata**

**In corso:**
- Da buildare la **v0.8** coi fix, poi test su telefono: viaggio "pulito" con banner mercantile e pausa, asteroidi densi in Cintura, baratto in stazione, mappa alleggerita — più tutto il giro della v0.7 (Argo, missioni, Mk II, rame, sala mappe, offline, migrazioni)

**Prossimo passo (roadmap aggiornata il 24/07 — dettagli nel [GDD](GDD.md)):**
1. **Playtest e bilanciamento della v0.7** (prezzi, tempi di rotta, resa del rame, costi Mk II)
2. **Silicio, titanio, alluminio** coi Mk successivi (pesi crescenti), nuovi campi e nuove ricette
3. **Altre stazioni** come checkpoint con gate di teletrasporto e milestone successive; **intro a fumetto**; l'**universo 2** come capitolo massimo
