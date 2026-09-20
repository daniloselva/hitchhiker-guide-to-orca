# 4. Parallelismo e orchestrazione supervisionata

Più agenti sono utili quando possono produrre lavoro separato o ipotesi confrontabili. Orca crea contesti isolati, ma non elimina il costo di integrazione. Dividere il task solo per “avere più agenti” tende a produrre sovrapposizioni e più codice da revisionare.

## Scegli il flusso

| Situazione | Strumento |
|---|---|
| Un task concreto, una sessione | Un agente in un worktree |
| Due implementazioni possibili della stessa correzione | Worktree separati, prompt comune e confronto dei diff |
| Attività indipendenti, per esempio test e documentazione | Un worktree per task, con ownership dei file dichiarata |
| Più fasi dipendenti, report richiesti, domande e stato di completamento tracciato | Orchestration supervisionata |
| Trasferire un task senza supervisione coordinata | Handoff con worktree/agente |

## Prima di dividere il lavoro

1. Scomponi l’obiettivo in risultati verificabili.
2. Per ogni unità, nomina file, directory o componente proprietario.
3. Identifica dipendenze reali: il test d’integrazione può iniziare prima che l’API sia definita? La documentazione dipende dalla UI finale?
4. Tieni coordinatore e integrazione a carico umano o assegnali esplicitamente.
5. Limita il numero di agenti alla capacità reale di revisione e integrazione.

Buone divisioni: “audit statico delle dipendenze”, “aggiungi test per il parser”, “implementa la UI usando il contratto X”. Divisioni fragili: “modifica `App.tsx` in due worktree con obiettivi diversi” oppure “fai il frontend” e “fai la API” senza concordare prima il contratto.

## Confrontare approcci

Per una bugfix incerta, crea worktree indipendenti dallo stesso base ref, assegna lo stesso problema e criteri di accettazione, poi confronta:

- riproducibilità e causa individuata;
- dimensione e coerenza del diff;
- test aggiunti e regressioni considerate;
- complessità e compatibilità;
- facilità di integrazione.

Non unire automaticamente entrambe le soluzioni: seleziona quella migliore e trasferisci solo le idee utili. La “gara” ha senso per problemi incerti o di alto impatto; per piccole modifiche il costo raramente vale la pena.

## Quando serve Orchestration

Orchestration è il livello strutturato di Orca per coordinare Run, Task, Dispatch, worker supervisionati, inbox e decision gate. È progettata per tracciare ownership e completamento. Va abilitata nelle impostazioni sperimentali prima dell’uso; `orca status --json` deve raggiungere il runtime.

Il flusso canonico è:

```powershell
orca status --json
orca orchestration run-create --objective "Verificare regressione checkout e produrre esito con evidenze" --json
orca orchestration worker-start --spec "Riprodurre il problema, correggere solo src/checkout e aggiungere test. Criterio: test checkout verdi." --worktree current --agent codex --json
orca orchestration check --wait --types "worker_done,escalation,question" --timeout-ms 900000 --json
```

Per attività su un worktree nuovo, la CLI supporta forme come `worker-start --task <taskId> --worktree new-child --name <nome> --agent codex`; controlla `orca orchestration worker-start --help` e la guida inclusa nella tua versione prima di comporre workflow più avanzati.

Ogni task orchestrato dovrebbe specificare:

- **Obiettivo:** risultato da produrre.
- **Target:** componenti, file o ambiente interessati.
- **Vincoli e ownership:** cosa può cambiare e cosa è assegnato ad altri.
- **Criteri osservabili:** test, output o evidenze che provano il completamento.

Tratta il risultato di ogni worker come un’ipotesi da verificare. Un messaggio di completamento non sostituisce diff, test e review. Per DAG ampi, errori, retry, release o gestione di run esistenti, segui l’esatta versione della guida `orca skills get orchestration --full`; non ricostruire argomenti o ID a memoria. Una perdita di contatto non dimostra che il processo si sia fermato.

## Gestire le dipendenze

Avvia prima la wave di task indipendenti. Aggiungi dipendenze soltanto quando un task non può davvero partire prima del risultato precedente. Se la catena è lunga, rivedi la scomposizione: passaggi troppo fini aumentano overhead e punti di blocco.

Stabilisci un contratto d’integrazione prima di distribuire task che toccano componenti collegati: nomi di API, tipi, file condivisi, formati e strategia di test. Un worktree figlio può rappresentare una relazione organizzativa; usa il branch di partenza appropriato per ottenere anche la relazione Git desiderata.

## Dopo il completamento

Verifica che ogni task atteso abbia esito, controlla i diff e decidi come integrare. Nell’orchestrazione supervisionata, processa i messaggi ricevuti prima di confermare il batch e gestisci esplicitamente ogni worker terminato. Segui la guida live della CLI per release o recovery: sono azioni sullo stato del runtime, non semplici alias di chiusura terminale.

## Riferimenti

- [Orchestrazione ufficiale](https://www.onorca.dev/docs/cli/orchestration)
- [Prima sessione con più agenti](https://www.onorca.dev/docs/first-session)
- [Guida della CLI installata](https://www.onorca.dev/docs/cli/overview) e `orca skills get orchestration`
