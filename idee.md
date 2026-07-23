# EvolvingSpace — Registro delle idee

> Le idee nascono nel [documento Google condiviso](https://docs.google.com/document/d/1T0WU_tlMBqnkFqK1EBIKvT2eLAm7uZf5NNEjZJ3do3M/edit), vengono valutate qui e finiscono **in roadmap** oppure tra le **scartate** (sempre con motivazione). Niente si perde.

Stati possibili: `🔎 in valutazione` · `✅ in roadmap` · `❌ scartata`

---

## Importate il 2026-07-23 (verdetti confermati il 23/07)

### 1. Progresso offline con cap a 24 ore
**Idea:** offline progress con limite bloccato a 24 h (aumentabile con potenziamenti); al riavvio popup con tempo trascorso e recap risorse, chiudibile con una X. Il consumo energetico offline va bilanciato perché la dotazione iniziale copra 12–24 h (potenziabile poi con una nuova stanza o col magazzino).
**Valutazione:** il mining offline esiste già (popup "Rapporto di bordo") ma copre solo ~30 minuti: l'idea è di fatto un ribilanciamento del cap offline. Assorbita dalla decisione sul **sistema energetico** del 23/07 (vedi [decisioni.md](decisioni.md)): il cap offline diventa la carica delle **batterie**, con capacità iniziale tarata su 12–24 h; l'upgrade batterie sostituisce il vecchio "upgrade serbatoio" della roadmap.
**Stato:** ✅ in roadmap — dentro il sistema energetico (tappa 2)
> **Aggiornamento 23/07 sera** (direttiva 12): col ritiro del sistema energetico le batterie non sono più il cap offline; restano il tetto delle 24 h e il **deposito a peso** del raggio traente (l'offline si ferma quando è pieno).

### 2. Schermata di configurazione delle automazioni offline
**Idea:** schermata per scegliere quali funzioni automatiche restano attive offline, con velocità di consumo regolabile per azione, così da ottimizzare il tempo offline.
**Valutazione:** col sistema energetico del 23/07 diventa il **"quadro elettrico" della nave**: quali sistemi lasciare attaccati alle batterie, ognuno col suo consumo di energia al minuto. Ha senso quando esisteranno **più** azioni offline (oggi c'è solo il mining): farla ora produrrebbe una schermata con una sola voce.
**Stato:** ✅ in roadmap — congelata (direttiva 12 del 23/07: richiede il ritorno del sistema energetico, oltre a ≥ 2 automazioni offline)

### 3. Asteroide di deuterio (spawn 25%)
**Idea:** nuovo asteroide, il deuterio, che spawna al posto di uno di ferro con rate del 25%.
**Valutazione:** primo secondo minerale del gioco, costo implementativo basso (sprite + tipo risorsa + spawner). La domanda "a cosa serve il deuterio?" è chiusa dalla decisione del 23/07: è **il combustibile del reattore** (deuterio → reattore → energia → batterie). Va implementato insieme al sistema energetico.
**Stato:** ❌ scartata per ora (23/07 sera, direttiva 12) — implementata nella v0.3 e poi rimossa insieme a reattore ed energia; l'asteroide di deuterio non spawna più. Reinseribile in futuro se servirà.

### 4. Fonderia più comoda (X di chiusura, produzione in blocco, "Crafta tutto")
**Idea:** X in alto a destra per tornare alla nave; pulsanti per produrre tutti i lingotti possibili con le risorse disponibili; tasto "Crafta tutto" per tutte le risorse in un colpo solo.
**Valutazione:** quality-of-life a basso costo e alto valore, standard del genere idle. Il "Crafta tutto" globale diventerà utile con più materiali (vedi idea 3); X e "produci tutti" si possono fare subito.
**Stato:** ✅ in roadmap — subito (tappa 1)

### 5. Più raggi traenti, potenziabili fisicamente dall'interno nave (stanza "Armeria/torrette")
**Idea:** upgrade per aumentare il **numero** di raggi traenti; vederli fisicamente nella schermata interno nave e potenziarli singolarmente cliccandoci sopra, invece del pannello generico; eventualmente una stanza "Armeria/torrette" dedicata.
**Valutazione:** due cose in una: (a) raggi multipli = upgrade di gameplay pulito e coerente col sistema forza/massa già predisposto → in roadmap; (b) spostare i potenziamenti dal pannello sala comandi agli oggetti fisici è un cambio di grammatica UI da decidere una volta per tutte (vale anche per torretta e reattore) → **decisione aperta** in [decisioni.md](decisioni.md).
**Stato:** ✅ in roadmap (raggi multipli, tappa 3); grammatica UI **decisa il 23/07 sera** (direttiva 12): gli upgrade restano nel pannello della sala comandi — vedi [decisioni.md](decisioni.md)

### 6. Mercantili e valuta galattica "Cookie"
**Idea:** incontri casuali con mercantili (al posto degli asteroidi) con cui commerciare; valuta "Cookie" ottenibile inizialmente solo dal commercio; comprare/vendere ferro e deuterio con disponibilità e prezzi casuali a ogni incontro.
**Valutazione:** apre l'economia del gioco e dà un secondo uso ai minerali; presuppone il deuterio (idea 3) e sta in piedi meglio con l'evoluzione 1b (asteroidi in avvicinamento → il mercantile "arriva" allo stesso modo). Da progettare con calma il bilanciamento dei prezzi.
**Stato:** ✅ in roadmap — dopo l'evoluzione 1b (tappa 4). *Nota 23/07 sera: col deuterio ritirato si commercia per ora solo ferro/lingotti; la direttiva 12 aggiunge una seconda fonte di Cookie, le **missioni** alla stazione spaziale (serviranno a sbloccare i tier).*

### 7. Mappa stellare
**Idea:** mappa che mostra la posizione attuale e i punti di interesse; più avanti basi spaziali come checkpoint con gate di teletrasporto.
**Valutazione:** è la spina dorsale del "viaggio" già previsto dal GDD (evoluzione 1b = viaggiare consumando energia). Il teletrasporto tra checkpoint è un buon anti-frustrazione per il late game.
**Stato:** ✅ in roadmap — insieme al viaggio 1b (tappa 3)

### 8. Prima stazione spaziale (hub dell'universo 1)
**Idea:** stazione di riferimento dell'universo 1 (ricco di ferro e deuterio) con servizi: rifornimento (acquisto deuterio, dopo la decisione energetica del 23/07), zona commercio, hangar per potenziare l'astronave, altri. *Nota 23/07 sera: col deuterio ritirato il servizio di rifornimento è da ridefinire; la direttiva 12 aggiunge le **missioni** che danno Cookie e sbloccano i tier.*
**Valutazione:** destinazione naturale del primo viaggio: dà uno scopo alla mappa (idea 7) e una casa a commercio (idea 6), tier (idea 9) e stanze nuove (idea 11). Grosso blocco di contenuto: va dopo che il viaggio funziona.
**Stato:** ✅ in roadmap — milestone dopo il viaggio (tappa 5)

### 9. Tier della strumentazione (potenziabili solo agli hangar)
**Idea:** tier per fonderia, torretta, attrattore, reattore, magazzino, potenziabili solo negli hangar delle stazioni; ogni tier aumenta esponenzialmente gli effetti e fa evolvere lo strumento (es. torretta che sblocca rame/silicio, poi titanio/alluminio).
**Valutazione:** ottima struttura di progressione a lungo termine: i livelli comprati a bordo restano la crescita "piccola", i tier agli hangar quella "grande". Il gating dei minerali per tier della torretta è elegante e si sposa con l'idea 10.
**Stato:** ✅ in roadmap — con la stazione (tappa 5). *Nota 23/07 sera (direttiva 12): i livelli di bordo sono cappati a 5 per il tier 1; i tier si sbloccano con le missioni Cookie della stazione.*

### 10. Nuovi minerali con spawn 0% fino alla prima stazione
**Idea:** nuovi minerali e asteroidi con spawn rate iniziale 0%; compaiono dopo l'arrivo alla prima stazione spaziale.
**Valutazione:** complementare alle idee 3 e 9 (rame, silicio, titanio, alluminio già citati lì). Di fatto è la regola di sblocco dei contenuti minerari: semplice e chiara.
**Stato:** ✅ in roadmap — con stazione + tier (tappa 5)

### 11. Stanze nuove costruibili all'hangar (es. sala mappe)
**Idea:** all'hangar si possono costruire nuove stanze sull'astronave; la sala mappe individua zone con diverse concentrazioni di minerali.
**Valutazione:** estende la nave oltre le 4 stanze del GDD e dà profondità alla mappa (idea 7). La sala mappe è anche il candidato citato nell'idea 1 per estendere l'offline. Richiede di ripensare la piantina della nave per far posto a stanze aggiuntive.
**Stato:** ✅ in roadmap — con la stazione (tappa 5)

---

## Importate il 2026-07-23, sera (direttive dopo il playtest della v0.3)

### 12. Passo indietro sull'energia: via deuterio e reattore, il grezzo pesa e vive nel raggio traente
**Idea (direttiva):** togliere energia, deuterio e reattore (si reinseriranno più avanti se serviranno); via anche lo spawn degli asteroidi di deuterio. Il magazzino non ha limite ma **non può contenere minerale grezzo**: il grezzo è trattenuto in un **deposito interno al raggio traente con limite in peso**. Ogni minerale grezzo ha un suo peso (i minerali futuri peseranno sempre di più) e il peso massimo trasportabile è potenziabile. Upgrade nella sala comandi: velocità di fuoco del laser, danni del laser, forza di trazione, peso trasportabile — tutti con **cap a Lv 5 per il tier 1**. I tier si sbloccheranno con missioni alla stazione spaziale che danno "Cookie"; per ora niente stazione né valuta, quindi si resta cappati a Lv 5 tier 1.
**Valutazione:** rollback ragionato della v0.3: l'economia energetica (due minerali, reattore, batterie, consumi al minuto) aggiungeva troppa gestione troppo presto. Il limite in peso sul grezzo ricrea il ritmo "torna in fonderia e svuota" con una sola valuta, e dà finalmente un corpo al deposito che il sistema forza/massa del raggio traente già prevedeva. Chiude anche la **decisione aperta sulla grammatica UI**: gli upgrade restano nel pannello della sala comandi. Effetti collaterali registrati: l'idea 1 perde le batterie come cap offline (restano il tetto delle 24 h e il deposito pieno), l'idea 2 (quadro elettrico) è congelata finché l'energia non tornerà, l'idea 3 (deuterio) esce di scena per ora.
**Stato:** ✅ in roadmap — subito (v0.4)

### 13. Dinamismo della schermata mining
**Idea (direttiva):** far pulsare un po' le stelle e dare l'effetto che la nave avanzi; asteroidi che compaiono random dalla cima della mappa, con piccolo margine di movimento laterale e discesa lenta e costante, e che scompaiono in fondo se non distrutti; il laser automatico può colpire solo asteroidi interamente dentro l'area di gioco e comunque non prima di un margine d'ingresso in alto; i minerali droppati più piccoli, che "scappano" dall'esplosione dell'asteroide di nascita — non troppo veloci, altrimenti il raggio traente non li prende.
**Valutazione:** anticipa la parte visiva dell'**evoluzione 1b** (asteroidi in movimento) senza barra velocità né mappa: supera la decisione 1a del 22/07 (ondate statiche). Il despawn in fondo introduce la prima pressione temporale dolce del gioco; il margine d'ingresso del laser automatico evita che la torretta "rubi" bersagli appena visibili.
**Stato:** ✅ in roadmap — subito (v0.4)

---

## Importate il 2026-07-23, notte (direttive dopo la v0.4)

### 14. Interno nave a lista di moduli, con costruzione e upgrade per modulo
**Idea (direttiva):** refactoring dell'interno: per ora un **interno a "lista"** con i moduli costruiti elencati e in fondo un tasto **"Costruisci modulo"** che apre la lista dei moduli nuovi; ogni modulo ha un suo **tempo di costruzione**. Moduli di default a inizio gioco: **sala comandi, magazzino, motore a impulso, laser minerario**. Upgrade per modulo: la sala comandi per ora non ha upgrade; il **magazzino** ha 5 livelli per i **minerali grezzi** con lo stesso sistema attuale (il deposito esce dal dominio del raggio traente); il **laser minerario** ha velocità e danno (5 livelli ciascuno) più **un solo livello di "IA"** per automatizzarlo; il **motore a impulso** ha 5 livelli e aumenta la velocità di avanzamento della nave, che influisce sul **numero di asteroidi che entrano in mappa**. Moduli costruibili: **fonderia** (stesse funzionalità, nessun upgrade) e **raggio traente** (stessa funzionalità, upgrade forza di trazione).
**Valutazione:** riorganizzazione pulita: ogni potenziamento vive nel suo modulo (la "grammatica UI" del pannello unico in sala comandi, decisa poche ore prima, viene superata), la torretta automatica diventa l'upgrade "IA" del laser, il deposito del grezzo passa al magazzino (più intuitivo), e il motore a impulso dà finalmente una leva sul ritmo del mining — primo assaggio della velocità di viaggio della 1b. La lista prepara anche le stanze costruibili future (idea 11).
**Stato:** ✅ in roadmap — subito (v0.5)

### 15. Fix: il mining automatico non deve fermarsi nei menù
**Idea (segnalazione):** il mining automatico funziona solo nella schermata principale; navigando nei menù dell'interno nave (o altri) si blocca, e non dovrebbe.
**Valutazione:** era un effetto dell'architettura (le schermate si disattivavano a vicenda). Fix strutturale: la schermata mining resta **sempre attiva** e i menù diventano **overlay a canvas opaco** sopra di lei — laser, raggio e spawner continuano a lavorare mentre si naviga, e la barra risorse dei menù si aggiorna in diretta.
**Stato:** ✅ in roadmap — subito (v0.5)

### 16. Musica leggermente più alta
**Idea (direttiva):** aumentare leggermente il volume della musica.
**Valutazione:** ritocco da 0,13 a 0,18 (laser ed effetti restano in primo piano); dal menù impostazioni (direttiva 17) il volume è comunque regolabile.
**Stato:** ✅ fatta (v0.5)

### 17. Icona impostazioni e menù con volumi e reset
**Idea (direttiva):** nella pagina iniziale spostare la X rossa **tutta a destra** e farla diventare un'**icona impostazioni**; il menù impostazioni ha lo slider del volume musica col tasto per mutarla e lo slider degli effetti col tasto per mutarli (le due opzioni allineate in alto), e in fondo un **tasto rosso per resettare il gioco** da capo. Fatte le modifiche, creare un file in `Docs/` con i **prompt per Gemini** per generare le schermate: interno nave, esempio di modulo con upgrade, menù impostazioni.
**Valutazione:** il reset esce dalla schermata di gioco (dov'era pericolosamente a portata di pollice) e finisce dietro le impostazioni + conferma; i volumi separati musica/effetti sono lo standard mobile. Preferenze salvate in PlayerPrefs: sopravvivono anche al reset del salvataggio. I prompt sono in [prompt-gemini.md](prompt-gemini.md).
**Stato:** ✅ fatta (v0.5)

---

## Idee scartate

- **Idea 3 — Asteroide di deuterio** (scartata *per ora* il 23/07 sera, direttiva 12): implementata nella v0.3 e rimossa lo stesso giorno insieme a reattore ed energia; se il sistema energetico tornerà, tornerà con lui.
