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

### 18. Fix: l'offline non funzionava su smartphone (app ripresa, non riavviata)
**Idea (segnalazione, 24/07):** su telefono il lavoro offline sembra non funzionare.
**Valutazione:** vero, ed era strutturale: il calcolo dell'offline girava solo all'avvio (`GameBootstrap.Awake`), ma su Android l'app in genere **non viene riavviata** — viene sospesa e ripresa, e alla ripresa nessuno conteggiava il tempo passato in background. In editor non si vede mai (lì il riavvio c'è sempre). Fix: **catch-up alla ripresa** in `SaveManager` — alla pausa si salva (già così), al rientro si ricarica lo stato salvato (che riapplica costruzioni e mining offline) e si mostra il Rapporto di bordo; una guardia evita il doppio conteggio al primo avvio (Android chiama anche un "resume" iniziale).
**Stato:** ✅ fatta (24/07, entrerà nella v0.6)

---

## Importate il 2026-07-24 (dopo la v0.9) — 19 voci in coda al documento

> Valutate tutte insieme: sono un pacchetto coerente (economia dei Cookie + missioni + fonderia + rifiniture), più una feature grossa a sé (i relitti). Le decisioni relative sono in [decisioni.md](decisioni.md).

### 19. Relitti di stazione esplorabili
**Idea:** incontro casuale (~1% al minuto di gioco online) con un relitto che scivola in schermata con luci d'emergenza; tap → `[ESPLORA RELITTO]` / `[IGNORA E PROSEGUI]`; minigioco a 3-7 stanze procedurali (casse, trappole, droni/alieni, stanza di controllo col bottino principale), navigazione con frecce ai lati e stanza a tutto schermo; **barra HP della tuta** (a 0 si perde tutto o parte del bottino) e **4 slot di equipaggiamento** (arma, corazza, utensile, visore) gestiti dalla nave.
**Valutazione:** ottima sul piano della lore (il gioco nasce da una stazione distrutta) e del design — è contenuto **solo-online**, quindi rinforza la regola "il gioco attivo batte il passivo" senza toccare i numeri del mining, e dà finalmente una destinazione agli oggetti. È però la feature più grande mai messa in cantiere: stanze procedurali, combattimento, inventario, arte a schermo pieno (oggi la grafica è pixel art procedurale o ritagliata dai mockup). Tre rischi da chiudere quando toccherà: alla frequenza proposta si vede **un relitto ogni ~100 minuti di gioco** (settimane di lavoro per contenuto raro: serve un pity timer); una spedizione in corso deve **sopravvivere alla sospensione dell'app** su Android (la trappola della segnalazione 18); e perdere tutto il bottino a 0 HP è duro per il genere (meglio la percentuale già citata). Nota di design: se il relitto vive **nel campo**, diventa il contraltare naturale dei mercantili, che vivono **nel viaggio** — e riequilibra il fatto che i Cookie stiano quasi tutti dalla parte in cui non si mina.
**Stato:** ✅ in roadmap — **milestone nominata, dopo tutto il resto**

### 20. Mercante: quantità multiple e "+" a ripetizione
**Idea:** comprare/vendere più pezzi in un colpo solo; tenendo premuto il "+" la quantità sale in fretta.
**Valutazione:** QoL puro, il banco ha già stepper e tetto: manca la pressione prolungata (e un "MAX"). Con l'eliminazione della vendita (idea 33) riguarderà solo gli acquisti.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 21. Barra risorse: via i lingotti, restano Cookie e carico
**Idea:** togliere l'indicatore dei lingotti in alto, lasciare solo Cookie e carico.
**Valutazione:** giusto — il carico è l'unico numero *azionabile* mentre si mina (a magazzino pieno si smette di raccogliere), i lingotti si guardano dove si spendono. Da verificare che i pannelli mostrino anche i **lingotti di rame**, che dal Mk II in poi sono la valuta dei livelli.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 22. La disponibilità del materiale si vede dove si costruisce
**Idea:** quando si vuole costruire qualcosa, la disponibilità di quel materiale compare direttamente lì, in chiaro.
**Valutazione:** complementare alla 21: se il totale sparisce dalla barra deve comparire nel punto di spesa. In buona parte già così (pannelli modulo e fonderia): è più una verifica che un lavoro, estesa a rame e grezzo.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 23. Magazzino: indicatore dei lingotti a sinistra
**Idea:** nella schermata magazzino l'indicatore dei lingotti va a sinistra, non al centro.
**Valutazione:** allineamento, nessun effetto sul gioco.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 24. Il tap punta anche l'IA
**Idea:** se si mina a mano mentre la torretta automatica è attiva, il tap su un asteroide deve far agganciare **quello** anche all'IA.
**Valutazione:** trasforma il tap da "colpo in più" a **comando di puntamento**, che è il ruolo giusto per il giocatore attivo una volta che l'automazione esiste. Banale da implementare (l'IA ha già una funzione di scelta bersaglio).
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 25. Cadenza unica del laser (manuale e automatico)
**Idea:** la velocità d'attacco dev'essere una sola e valere per entrambi; oggi appena finisce la laserata manuale ne parte una automatica su un asteroide a caso.
**Valutazione:** il difetto è reale e strutturale — manuale e IA hanno due orologi separati e quello dell'IA avanza solo quando spara lei. Ma "cadenza unica" ha tre letture con effetti opposti sul bilanciamento; scelta quella che non rompe l'early game né la regola "attivo batte passivo": **timer unico dettato dal fascio** (l'upgrade accorcia il fascio, 1,00 → 0,55 s, e si vede su entrambi), **tap con prelazione** che può ridirigere il fascio acceso, **IA con 0,4 s di tempo di reazione**. Dettagli e conseguenze in [decisioni.md](decisioni.md); la resa offline va ricalcolata.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 26. Più velocità percepita in viaggio
**Idea:** aumentare la velocità che si percepisce mentre la nave viaggia; la nave parte a razzo e lascia gli asteroidi indietro, e alla partenza smette di sparare e disabilita il raggio traente.
**Valutazione:** una manciata di numeri (il mondo passa da ×4 a ×8, stelle in scia) più lo stacco di laser e raggio, che rende il viaggio coerente con "in rotta non si mina" e regala una piccola scelta al giocatore: partire subito o finire di raccogliere il campo.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 27. Tetto agli asteroidi grossi (1 in Vena Ricca, 2 nel campo denso)
**Idea:** nei campi ricchi un solo asteroide grosso in campo visivo alla volta (se il random ne vuole un altro, lo genera piccolo); nel campo denso al massimo due.
**Valutazione:** il principio è quello giusto (la densità non deve affollare schermo e pollice), ma nella **Vena Ricca tutti** gli asteroidi sono grossi e ce ne sono ~3 in campo: un tetto a 1 taglierebbe la resa di circa il 40% e il campo diventerebbe peggiore della Cintura Densa, perdendo la sua identità. Andrebbe compensato (grosso più ricco o densità più alta). Nel campo denso l'impatto sarebbe lieve (~1,75 densi attesi contro un tetto di 2).
**Stato:** ⏸️ **rinviata** su richiesta — numeri invariati per ora

### 28. Limitare quanti lingotti compra un mercante
**Idea:** ridurre a qualche centinaio i lingotti che un mercante acquista, per non svuotare di senso le missioni in Cookie.
**Valutazione:** diagnosi giusta (i mercantili erano un rubinetto di Cookie proporzionale al tempo, non al contenuto), ma **superata dall'idea 33**: se la vendita dei lingotti sparisce del tutto, il tetto non ha più oggetto.
**Stato:** ➡️ superata dalla 33

### 29. Impostazioni: X più grande e staccata dal reset
**Idea:** rimpicciolire "RESETTA IL GIOCO", ingrandire la X di chiusura e spostarla più in basso, che ora è attaccata al pulsante di reset.
**Valutazione:** oltre all'estetica toglie un rischio vero: due bersagli adiacenti di cui uno cancella la partita.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 30. Zoom sulla mappa stellare
**Idea:** poter zoomare e de-zoomare la mappa, con un po' più di spazio ai lati anche dove non c'è nulla.
**Valutazione:** sensato adesso che i campi passano da 5 a 7 (idea 37) e cresceranno ancora. Unica accortezza: il pinch non deve essere mangiato dalla soglia di trascinamento introdotta con la mappa trascinabile.
**Stato:** ✅ in roadmap — pacchetto rifiniture

### 31. A cosa servono i Cookie
**Idea:** i Cookie servono a evolvere i tier delle strutture, a comprare altre tipologie di lingotti (in abbondanza alle stazioni, in minor quantità dai mercanti) liberandosi di quelli che non servono, e in futuro a comprare oggetti per il crafting.
**Valutazione:** dà finalmente tre sbocchi alla valuta forte. Attenzione a uno: **comprare lingotti coi Cookie riapre dalla finestra il baratto verso l'alto** chiuso il 24/07 (ferro → missione → Cookie → rame). Non è fatale se il prezzo è alto: fissato a **10 Cookie per lingotto di rame**, cioè il doppio del suo valore in "equivalenti asteroide", con la regola permanente che il prezzo non scenda mai sotto il valore equo. Gli oggetti per il crafting arrivano coi relitti (idea 19).
**Stato:** ✅ in roadmap — con l'economia; oggetti rinviati alla milestone dei relitti

### 32. Fonderia a livelli, niente calibrazioni
**Idea:** la fonderia si potenzia come gli altri moduli — costruirla la prima volta costa **ferro grezzo** (Lv 1, fonde il ferro), portarla a Lv 2 costa **rame grezzo** (fonde il rame). Niente calibrazioni. Quando si scopre un minerale nuovo, la sua ricetta compare **subito** nella lista anche se non è fondibile, avvisando che serve un upgrade.
**Valutazione:** semplificazione netta — un concetto solo (il livello del modulo) al posto di due, e la fonderia entra nella stessa grammatica degli altri moduli. Il ciclo fondativo "ogni minerale nuovo si mina prima a mano" **si rafforza**: il rame grezzo del Lv 2 va estratto a mano per forza (l'IA lo ignora finché non è Lv 2, e l'IA Lv 2 si paga in lingotti di rame che senza fonderia Lv 2 non esistono). Mostrare le ricette bloccate rende visibile il prossimo obiettivo. Nota obbligata: la prima fonderia si paga in grezzo perché senza fonderia i lingotti non esistono.
**Stato:** ✅ in roadmap — subito

### 33. Eliminare la vendita dei lingotti
**Idea:** togliere la possibilità di vendere lingotti, limitarla allo scambio verso quelli di tier più basso.
**Valutazione:** coerente con la decisione del 24/07 (baratto solo verso il basso) e chiude il rubinetto "tempo di mining → Cookie". Due conseguenze da gestire: muore la missione **"Rotta commerciale — vendi 20 lingotti ai mercantili"** (da sostituire, con migrazione per chi ce l'ha in corso) e il mercantile resta senza il suo banco principale — il suo nuovo valore diventa **offrire incarichi** (idea 34) e vendere lingotti in cambio di Cookie (idea 31).
**Stato:** ✅ in roadmap — con l'economia

### 34. Missioni migliori, più numerose, ripetibili e anche dai mercanti
**Idea:** migliorare le ricompense delle missioni di stazione, aggiungerne di nuove, renderle ripetibili con richieste e ricompense leggermente diverse ogni volta, e permettere anche ai mercanti di darne.
**Valutazione:** è il pezzo che tiene in piedi tutto il resto: senza un rubinetto **ripetibile** la catena chiusa di 4 missioni (70 Cookie totali) non paga nemmeno un tier da 500. Impostate come missioni generate con valore in **equivalenti asteroide**, ricompensa 0,75-1,00 Cookie per equivalente e tetto di 200 (idea 36); il freno del rubinetto è il **viaggio ad Argo** (bacheca che si rinnova a ogni attracco) più un incarico per mercantile abbordato — nessun limite giornaliero, come da decisione del 24/07.
**Stato:** ✅ in roadmap — con l'economia

### 35. Sala comunicazioni
**Idea:** nuova stanza dell'interno nave con le missioni in corso e il loro avanzamento; da qui si concludono le missioni completate e si riscuotono le ricompense. Potenziandola aumentano le ricompense.
**Valutazione:** incastra nella lista moduli (è la "stanza costruibile" che l'idea 11 prevedeva) e toglie un fastidio vero (oggi le missioni si vedono solo attraccando). Due avvertenze: la stazione perde uno dei suoi tre servizi — si tiene il motivo di tornarci facendo sì che gli **incarichi si prendano** ad Argo (o dai mercanti) e **si consegnino** da bordo; e il bonus alle ricompense è un moltiplicatore sulla valuta forte, quindi resta piccolo e cappato (+3% per livello). In compenso le si dà un secondo ruolo che serviva all'economia: **quante missioni puoi tenere attive** (2 → 5 coi livelli).
**Stato:** ✅ in roadmap — con l'economia

### 36. Costi e ricompense in Cookie
**Idea:** il passaggio da tier 1 a tier 2 delle strutture costa **500 Cookie**; missioni di esempio: consegna 200 lingotti di ferro → 150, abbatti 30 asteroidi → 200, consegna 50 lingotti di rame → 200.
**Valutazione:** il prezzo dei tier sale da 210 a 1500 Cookie complessivi ed è la scelta giusta (210 erano un pomeriggio), ma richiede il rubinetto ripetibile dell'idea 34. Sulle tre missioni di esempio c'era un'inversione sforzo/premio: 200 lingotti di ferro sono ~200 asteroidi per 150 Cookie (0,75 a testa), 30 asteroidi ne pagavano 200 (6,7 a testa, **9 volte tanto**, senza consumare nulla). Risolta con la banda fissa **0,75-1,00 Cookie per equivalente asteroide** e il tetto di 200 a missione, con cifre tonde (asteroidi a multipli di 5, lingotti di 10, grezzo di 30); il rame vale 5 equivalenti perché ha 80 HP contro 15 — con questa scala la "consegna 50 lingotti di rame → 200" torna esatta.
**Stato:** ✅ in roadmap — con l'economia

### 37. Campi misti ferro + rame
**Idea:** dopo il campo del rame, un campo misto ferro/rame; più altri due campi con rapporti 50/50 e 20/80.
**Valutazione:** coerente con i campi discreti e con la regola "il minerale vecchio non sparisce mai del tutto" (anche il 20/80 conserva un quinto di ferro). Dà una progressione geografica al secondo metallo e un motivo per rimettersi in viaggio dopo la stazione. Non serve un gate esplicito: senza laser Mk II il rame non si scalfisce, quindi un campo 20/80 si auto-esclude da solo per chi non è pronto. Da tenere a mente per il futuro: la variante **densa** oggi esiste solo sul ferro.
**Stato:** ✅ in roadmap — con l'economia

---

## Importata il 2026-07-24 (dopo la roadmap)

### 38. Mappa stellare a sistemi solari: nodi, archi e rotte dei mercanti
**Idea (direttiva):** rifare la mappa stellare come una **rete di sistemi solari** — i sistemi sono **nodi**, collegati tra loro da **archi**. Il viaggio avviene **da sistema a sistema** lungo gli archi; ogni sistema contiene i propri **campi di asteroidi, stazioni spaziali e altro**. I **mercanti non sono più incontri casuali**: le loro **rotte sono visibili sugli archi** e si possono **intercettare**. *(Dettagli quando si arriverà a quel punto.)*
**Valutazione:** è il salto che la mappa attuale chiedeva. Oggi i punti di interesse stanno su un piano euclideo con distanze in linea d'aria: funziona con 5-7 campi, diventa una nuvola indistinta quando arriveranno silicio, titanio, alluminio e le altre stazioni. Il grafo dà **struttura e gerarchia** (dentro il sistema / tra i sistemi), rende leggibile la distanza come numero di salti invece che come pixel, e scala all'infinito senza affollare lo schermo. Due guadagni di design che non erano cercati ma arrivano gratis: i **gate di teletrasporto** già previsti per le stazioni-checkpoint diventano semplicemente **archi speciali**, e "il sistema di partenza" prende un senso narrativo che oggi non ha.

Il pezzo più prezioso è però il **mercante intercettabile**: trasforma l'incontro da lotteria (~10% al minuto di rotta) in **decisione informata** — lo vedi muoversi, calcoli se ti conviene deviare, scegli. È coerente con la direzione presa da tutto il resto del gioco (il tap che punta, la partenza che è una scelta) e risolve alla radice il difetto strutturale dei mercanti: erano l'unica fonte di Cookie e dipendevano dal caso.

**Punti da chiudere quando toccherà:**
1. **Supera due decisioni del 24/07**: "mercantili come incontri casuali in rotta" e il banner di avvistamento con deviazione. Va deciso cosa sopravvive — il primo mercantile garantito che rivela la stazione ha ancora senso in un mondo dove le rotte si vedono?
2. **I mercanti si muovono anche a gioco chiuso?** Se sì serve una simulazione delle loro posizioni nel tempo (deterministica, altrimenti il salvataggio non regge); se no, la mappa "riparte" a ogni apertura e l'intercettazione perde credibilità.
3. **Rotte multi-salto**: il viaggio offline oggi gestisce una tratta sola. Con un grafo servono percorsi a più archi, con arrivo e attracco intermedi calcolati a gioco chiuso.
4. **Cosa rivela cosa**: la sala mappe oggi svela la composizione dei campi; nel grafo può svelare il contenuto dei sistemi non visitati, e le rotte dei mercanti possono chiedere un modulo dedicato (sensori/radar) — un nuovo scopo per l'interno nave.
5. **Salvataggio**: i campi attuali diventano i campi del sistema di partenza; serve una migrazione `campo` → `sistema + campo`.
6. **Ricadute sul pacing dell'economia**: il rubinetto dei Cookie è tarato sul viaggio ad Argo (~7-10 minuti); se i tempi cambiano col grafo, la banda 0,75-1,00 Cookie per equivalente va rivalidata.
**Stato:** ✅ in roadmap — **tappa 3**, subito prima dei nuovi minerali

---

## Idee scartate

- **Idea 3 — Asteroide di deuterio** (scartata *per ora* il 23/07 sera, direttiva 12): implementata nella v0.3 e rimossa lo stesso giorno insieme a reattore ed energia; se il sistema energetico tornerà, tornerà con lui.
