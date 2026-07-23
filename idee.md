# EvolvingSpace — Registro delle idee

> Le idee nascono nel [documento Google condiviso](https://docs.google.com/document/d/1T0WU_tlMBqnkFqK1EBIKvT2eLAm7uZf5NNEjZJ3do3M/edit), vengono valutate qui e finiscono **in roadmap** oppure tra le **scartate** (sempre con motivazione). Niente si perde.

Stati possibili: `🔎 in valutazione` · `✅ in roadmap` · `❌ scartata`

---

## Importate il 2026-07-23 (verdetti confermati il 23/07)

### 1. Progresso offline con cap a 24 ore
**Idea:** offline progress con limite bloccato a 24 h (aumentabile con potenziamenti); al riavvio popup con tempo trascorso e recap risorse, chiudibile con una X. Il consumo energetico offline va bilanciato perché la dotazione iniziale copra 12–24 h (potenziabile poi con una nuova stanza o col magazzino).
**Valutazione:** il mining offline esiste già (popup "Rapporto di bordo") ma copre solo ~30 minuti: l'idea è di fatto un ribilanciamento del cap offline. Assorbita dalla decisione sul **sistema energetico** del 23/07 (vedi [decisioni.md](decisioni.md)): il cap offline diventa la carica delle **batterie**, con capacità iniziale tarata su 12–24 h; l'upgrade batterie sostituisce il vecchio "upgrade serbatoio" della roadmap.
**Stato:** ✅ in roadmap — dentro il sistema energetico (tappa 2)

### 2. Schermata di configurazione delle automazioni offline
**Idea:** schermata per scegliere quali funzioni automatiche restano attive offline, con velocità di consumo regolabile per azione, così da ottimizzare il tempo offline.
**Valutazione:** col sistema energetico del 23/07 diventa il **"quadro elettrico" della nave**: quali sistemi lasciare attaccati alle batterie, ognuno col suo consumo di energia al minuto. Ha senso quando esisteranno **più** azioni offline (oggi c'è solo il mining): farla ora produrrebbe una schermata con una sola voce.
**Stato:** ✅ in roadmap — fase avanzata (prerequisito: ≥ 2 automazioni offline)

### 3. Asteroide di deuterio (spawn 25%)
**Idea:** nuovo asteroide, il deuterio, che spawna al posto di uno di ferro con rate del 25%.
**Valutazione:** primo secondo minerale del gioco, costo implementativo basso (sprite + tipo risorsa + spawner). La domanda "a cosa serve il deuterio?" è chiusa dalla decisione del 23/07: è **il combustibile del reattore** (deuterio → reattore → energia → batterie). Va implementato insieme al sistema energetico.
**Stato:** ✅ in roadmap — dentro il sistema energetico (tappa 2)

### 4. Fonderia più comoda (X di chiusura, produzione in blocco, "Crafta tutto")
**Idea:** X in alto a destra per tornare alla nave; pulsanti per produrre tutti i lingotti possibili con le risorse disponibili; tasto "Crafta tutto" per tutte le risorse in un colpo solo.
**Valutazione:** quality-of-life a basso costo e alto valore, standard del genere idle. Il "Crafta tutto" globale diventerà utile con più materiali (vedi idea 3); X e "produci tutti" si possono fare subito.
**Stato:** ✅ in roadmap — subito (tappa 1)

### 5. Più raggi traenti, potenziabili fisicamente dall'interno nave (stanza "Armeria/torrette")
**Idea:** upgrade per aumentare il **numero** di raggi traenti; vederli fisicamente nella schermata interno nave e potenziarli singolarmente cliccandoci sopra, invece del pannello generico; eventualmente una stanza "Armeria/torrette" dedicata.
**Valutazione:** due cose in una: (a) raggi multipli = upgrade di gameplay pulito e coerente col sistema forza/massa già predisposto → in roadmap; (b) spostare i potenziamenti dal pannello sala comandi agli oggetti fisici è un cambio di grammatica UI da decidere una volta per tutte (vale anche per torretta e reattore) → **decisione aperta** in [decisioni.md](decisioni.md).
**Stato:** ✅ in roadmap (raggi multipli, tappa 3); grammatica UI tra le decisioni aperte

### 6. Mercantili e valuta galattica "Cookie"
**Idea:** incontri casuali con mercantili (al posto degli asteroidi) con cui commerciare; valuta "Cookie" ottenibile inizialmente solo dal commercio; comprare/vendere ferro e deuterio con disponibilità e prezzi casuali a ogni incontro.
**Valutazione:** apre l'economia del gioco e dà un secondo uso ai minerali; presuppone il deuterio (idea 3) e sta in piedi meglio con l'evoluzione 1b (asteroidi in avvicinamento → il mercantile "arriva" allo stesso modo). Da progettare con calma il bilanciamento dei prezzi.
**Stato:** ✅ in roadmap — dopo evoluzione 1b e deuterio (tappa 4)

### 7. Mappa stellare
**Idea:** mappa che mostra la posizione attuale e i punti di interesse; più avanti basi spaziali come checkpoint con gate di teletrasporto.
**Valutazione:** è la spina dorsale del "viaggio" già previsto dal GDD (evoluzione 1b = viaggiare consumando energia). Il teletrasporto tra checkpoint è un buon anti-frustrazione per il late game.
**Stato:** ✅ in roadmap — insieme al viaggio 1b (tappa 3)

### 8. Prima stazione spaziale (hub dell'universo 1)
**Idea:** stazione di riferimento dell'universo 1 (ricco di ferro e deuterio) con servizi: rifornimento (acquisto deuterio, dopo la decisione energetica del 23/07), zona commercio, hangar per potenziare l'astronave, altri.
**Valutazione:** destinazione naturale del primo viaggio: dà uno scopo alla mappa (idea 7) e una casa a commercio (idea 6), tier (idea 9) e stanze nuove (idea 11). Grosso blocco di contenuto: va dopo che il viaggio funziona.
**Stato:** ✅ in roadmap — milestone dopo il viaggio (tappa 5)

### 9. Tier della strumentazione (potenziabili solo agli hangar)
**Idea:** tier per fonderia, torretta, attrattore, reattore, magazzino, potenziabili solo negli hangar delle stazioni; ogni tier aumenta esponenzialmente gli effetti e fa evolvere lo strumento (es. torretta che sblocca rame/silicio, poi titanio/alluminio).
**Valutazione:** ottima struttura di progressione a lungo termine: i livelli comprati a bordo restano la crescita "piccola", i tier agli hangar quella "grande". Il gating dei minerali per tier della torretta è elegante e si sposa con l'idea 10.
**Stato:** ✅ in roadmap — con la stazione (tappa 5)

### 10. Nuovi minerali con spawn 0% fino alla prima stazione
**Idea:** nuovi minerali e asteroidi con spawn rate iniziale 0%; compaiono dopo l'arrivo alla prima stazione spaziale.
**Valutazione:** complementare alle idee 3 e 9 (rame, silicio, titanio, alluminio già citati lì). Di fatto è la regola di sblocco dei contenuti minerari: semplice e chiara.
**Stato:** ✅ in roadmap — con stazione + tier (tappa 5)

### 11. Stanze nuove costruibili all'hangar (es. sala mappe)
**Idea:** all'hangar si possono costruire nuove stanze sull'astronave; la sala mappe individua zone con diverse concentrazioni di minerali.
**Valutazione:** estende la nave oltre le 4 stanze del GDD e dà profondità alla mappa (idea 7). La sala mappe è anche il candidato citato nell'idea 1 per estendere l'offline. Richiede di ripensare la piantina della nave per far posto a stanze aggiuntive.
**Stato:** ✅ in roadmap — con la stazione (tappa 5)

---

## Idee scartate

*(ancora nessuna — le idee scartate resteranno qui con la motivazione)*
