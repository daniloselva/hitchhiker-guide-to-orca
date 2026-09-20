# 7. Ricette e ottimizzazioni per use case

Le ottimizzazioni più utili riducono setup, ambiguità, attese o lavoro di review. Parti dal collo di bottiglia osservato; non aggiungere agenti, configurazioni o automazioni per principio.

## Bug urgente e ben riproducibile

**Flusso:** un worktree, un agente, test mirato.

1. Crea un worktree dal base ref corretto.
2. Includi passi per riprodurre, log o test, comportamento atteso e vincoli.
3. Chiedi un test di regressione prima o insieme alla correzione.
4. Esegui prima i test del componente, poi la suite più ampia necessaria.
5. Rivedi il diff e integra.

**Ottimizzazione:** prepara comandi rapidi per riprodurre e testare. Tre agenti per un bug con causa già chiara aumentano il rumore più della velocità.

## Bug difficile o causa incerta

**Flusso:** più worktree sullo stesso base, approcci distinti, confronto finale.

Chiedi a ogni agente una diagnosi con evidenze e una soluzione minima. Mantieni uguali prompt e criteri quando vuoi confrontare modelli; rendili differenti solo quando confronti ipotesi intenzionalmente. Scegli un candidato, scarta gli altri workspace dopo aver salvato eventuali idee utili.

**Ottimizzazione:** confronta il ragionamento sul riproduttore e sulla causa, non solo la dimensione del codice prodotto.

## Feature che attraversa più componenti

**Flusso:** definisci prima i contratti, poi crea task autonomi per area.

Un agente può chiarire API e dati mentre un altro prepara i test d’integrazione; avvia l’implementazione parallela solo dopo che interfacce, nomi e ownership dei file sono stabili. Se le fasi dipendono davvero una dall’altra o richiedono report formali, usa un Run supervisionato.

**Ottimizzazione:** consegna ai worker una specifica concreta e un criterio osservabile. Evita task “esplora e poi fai tutto” senza limiti: sono difficili da assegnare, integrare e verificare.

## Refactor o aggiornamento dipendenze

**Flusso:** parti con inventario e piano verificabile; modifica per sezioni e suite di test.

Non distribuire file interdipendenti a più agenti senza assegnare ownership. Fai eseguire prima un’analisi read-only delle dipendenze/compatibilità, poi approva un piano e applicalo per tranche. Controlla lockfile, changelog delle dipendenze, build e runtime.

**Ottimizzazione:** separa “analizza” da “modifica” quando l’impatto è incerto. Evita worktree che condividono directory di dipendenze se versioni diverse devono coesistere.

## Bug di interfaccia o flusso browser

**Flusso:** workspace con browser associato, dati di prova deterministici, agent e verifiche UI.

Fornisci route, passi, account/dati di test appropriati e risultato visivo o funzionale atteso. Usa snapshot e controlli ripetibili per il browser integrato; separa profili quando serve isolare login o cookie. Per revisionare HTML, apri l’anteprima accanto al diff.

**Ottimizzazione:** includi screenshot o descrizioni precise degli stati, ma non affidarti alla sola immagine: verifica anche gli stati errore, caricamento, accessibilità e test automatici.

## Molti task piccoli in un progetto attivo

**Flusso:** worktree nominati per task, commenti di stato brevi, dashboard/agenti e Jump Palette per navigare.

Stabilisci convenzione nomi, base ref, link issue e status card. Mantieni visibili i workspace attivi; usa commenti per il prossimo passo o il blocco. Archivia periodicamente i task chiusi.

**Ottimizzazione:** annota avanzamenti quando cambia la fase, non a ogni comando. Un riepilogo leggibile evita di aprire ogni terminale solo per capire lo stato.

## Build lunga, macchina potente o lavoro remoto

**Flusso:** seleziona la modalità in base a dove sono repository, tool e credenziali.

Local desktop è adatto al ciclo breve. SSH usa un host che controlli già; il codice e gli agenti lavorano in remoto mentre editor e diff restano nell’interfaccia. Un server Orca persistente è adatto a runtime sempre disponibile. Una VM per workspace è utile quando vuoi isolamento effimero e hai già configurato il provider.

**Ottimizzazione:** misura il tempo speso in compile/test prima di spostare tutto in remoto. Controlla toolchain e credenziali sull’host, latenza, modalità di sync, persistenza, costi e cleanup. Usa la guida Orca [Ways to run](https://www.onorca.dev/docs/ways-to-run) per scegliere il setup adatto.

## Triage, report o review ricorrenti

**Flusso:** automazione con scope e prompt stretti, inizialmente disabilitata.

Esempi utili: classificare issue aperte, riassumere cambiamenti della giornata, identificare PR in attesa. Per prima cosa produci un report senza modifiche. Aggiungi precheck, fuso orario, destinazione e verifica delle run prima di abilitare.

**Ottimizzazione:** usa prompt che distinguono fatti da ipotesi, citano issue/PR pertinenti e si fermano quando input o permessi mancano. Riduci schedule frequenti se il volume è basso.

## Tenere sotto controllo il costo del parallelismo

Prima di aggiungere agenti, chiediti:

- Il lavoro è davvero indipendente o confrontabile?
- Posso dare a ciascuno un’area e un risultato separati?
- Ho tempo per leggere ogni diff e rilanciare i test d’integrazione?
- Il task è abbastanza incerto/importante da giustificare tentativi concorrenti?

Se le risposte sono no, usa una sessione. Come ottimizzazione del contesto, dai all’agente i file e i comandi più pertinenti, fai partire i test mirati e amplia la verifica solo quando il cambiamento lo richiede. Mantieni prompt e report leggibili e non chiedere aggiornamenti continui privi di risultati.

## Checklist di fine giornata

- [ ] I workspace attivi hanno nome e prossimo passo leggibili.
- [ ] Blocchi e richieste di input sono visibili.
- [ ] Le run automatiche hanno un esito ispezionabile.
- [ ] Diff e test dei task pronti sono stati rivisti.
- [ ] I worktree completati o abbandonati sono stati archiviati/puliti in sicurezza.
