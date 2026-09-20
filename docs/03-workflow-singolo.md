# 3. Il ciclo quotidiano con un agente

Il ciclo più affidabile è: scegli il workspace giusto, dai un task circoscritto, chiarisci i criteri di completamento, controlla il risultato e lascia una traccia utile dello stato.

## 1. Scopri il contesto prima di modificarlo

Dentro un terminale gestito da Orca, verifica quale worktree e branch hai aperto:

```powershell
orca worktree current --json
git status --short --branch
```

Questo è importante anche per gli agenti: un terminale in un workspace inatteso può indirizzare il lavoro al progetto sbagliato. Se vuoi un nuovo checkout, crealo dal repository invece di cambiare branch nel workspace di un altro task.

## 2. Scrivi un prompt operativo

Struttura il prompt così:

```text
Obiettivo: <risultato richiesto>
Contesto: <bug, issue, file o comportamento osservato>
Vincoli: <compatibilità, API, file da non modificare, dipendenze>
Accettazione: <test o risultato osservabile>
Verifica: <comandi da eseguire e da riportare>
```

Esempio:

```text
Obiettivo: correggere il caso in cui il filtro data del report si azzera al refresh.
Contesto: il valore iniziale arriva dai parametri URL; il difetto è in src/report/filters.
Vincoli: mantenere compatibili URL esistenti e non cambiare il formato dell'API.
Accettazione: aggiungere un test che riproduca il refresh e mantenere gli altri test report verdi.
Verifica: eseguire i test report e il type-check; riportare eventuali test non eseguibili.
```

Rendi esplicito se l’agente può cambiare schema, dipendenze, snapshot o interfacce pubbliche. Se il task tocca credenziali, dati reali o sistemi esterni, specifica quali azioni sono ammesse e quali devono restare manuali.

## 3. Segui la sessione dal contesto di Orca

Orca mostra lo stato degli agenti riconosciuti e consente di tornare al terminale del workspace. Se usi la CLI, scopri gli handle con `orca terminal list --worktree active --json`; leggi l’output prima di inviare input:

```powershell
orca terminal list --worktree active --json
orca terminal read --terminal <handle> --json
orca terminal wait --terminal <handle> --for tui-idle --timeout-ms 300000 --json
```

Un agente inattivo può essere in attesa di una tua decisione. Leggi l’output, rispondi nel terminale corretto e lascia che riprenda. `tui-idle` indica una condizione della sessione, non un’approvazione del risultato.

## 4. Lascia checkpoint leggibili

Aggiorna il commento del workspace quando cambia la fase: ipotesi confermata, implementazione conclusa, test in corso, blocco o attesa di review.

```powershell
orca worktree set --worktree active --comment "fix implementato in src/report/filters; test report verdi; pronto per review" --workspace-status in-review --json
```

Se il commento contiene obiettivi o vincoli dell’utente, leggilo prima di sostituirlo (`orca worktree current --json`) e conserva le informazioni ancora valide. Il commento è un riepilogo corrente, non un changelog dettagliato.

## 5. Chiudi il task in modo esplicito

Prima di dichiararlo completato:

- controlla `git status` e il diff;
- verifica che i test importanti siano stati eseguiti;
- annota i limiti o i test saltati;
- aggiorna stato e commento;
- passa alla revisione e consegna descritte in [Revisione e consegna](05-review-e-consegna.md).

## Invio diretto o coordinamento?

`orca terminal send` è adatto per passare un prompt occasionale a un terminale. Leggi il terminale prima di scriverci, a meno che l’input successivo sia ovvio. Per un’attività con tentativi supervisionati, responsabilità e completamento tracciati usa il livello Orchestration, descritto nel [capitolo successivo](04-parallelismo-e-orchestrazione.md).

## Per un handoff

Un handoff trasferisce la proprietà del task a un nuovo agente o worktree. Fornisci file interessati, vincoli e risultato atteso nel prompt iniziale. È adatto quando non devi coordinare un gruppo né aspettare un esito da più worker.

## Riferimenti

- [Stati di agenti e sessioni](https://www.onorca.dev/docs/model/agents-sessions)
- [Checkpoint del worktree](https://www.onorca.dev/docs/cli/worktree-checkpoints)
- [Orca CLI: terminali e worktree](https://www.onorca.dev/docs/cli/overview)
