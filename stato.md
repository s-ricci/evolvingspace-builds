# EvolvingSpace — Stato dei lavori

> Documento vivo: aggiornare a ogni sessione di lavoro (cosa è fatto, cosa è in corso, prossimo passo).

## Aggiornato al 2026-07-22

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
- **Documentazione online**: `documentazione.html` pubblicata come `index.html` sul repo → **https://s-ricci.github.io/evolvingspace-builds/** (GitHub Pages); per ripubblicarla dopo una modifica: `Tools/publish-docs.ps1`

**In corso:**
- Build v0.2 con release GitHub e updater a bordo
- Test su telefono: reset, installazione raggio, aggiornamento automatico, tap/drag su touch, latenza audio

**Prossimo passo:**
1. Upgrade capacità serbatoio (ultimo tassello della catena prima del viaggio)
2. Evoluzione 1b della schermata mining: asteroidi in avvicinamento + barra velocità che consuma FUEL
