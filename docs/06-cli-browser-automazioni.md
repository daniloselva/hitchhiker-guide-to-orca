# 6. CLI, browser e automazioni

La CLI consente di interrogare e pilotare il runtime Orca attivo da terminali o script. Per comandi che agiscono su Orca, usa prima `orca status --json`; per scoprire i dettagli della build, preferisci la guida corrispondente a quella CLI.

## Discovery di base

```powershell
orca status --json
orca repo list --json
orca worktree current --json
orca worktree ps --json
orca terminal list --worktree active --json
```

Se vuoi interrogare un workspace diverso dal current, passa un selettore a `--worktree`; gli ID worktree includono repo e percorso, quindi riusa il valore completo mostrato da Orca. Gli handle dei terminali sono assegnati dal runtime: scoprili con `terminal list` e riutilizzali solo mentre sono validi.

La famiglia `orca file` apre file e diff nell’editor di Orca. Per esempio:

```powershell
orca file open src/App.tsx
orca file diff src/App.tsx
orca file open-changed --mode both
```

## Interagire con un terminale

Prima di inviare input, leggi il terminale. Per gli agenti, un prompt seguito da Invio è una richiesta di input; la ricevuta di accettazione dimostra che il testo è arrivato, non sempre che il turno dell’agente sia iniziato.

```powershell
orca terminal read --terminal <handle> --json
orca terminal send --terminal <handle> --text "continua dopo aver sistemato il test" --enter --json
orca terminal wait --terminal <handle> --for tui-idle --timeout-ms 300000 --json
```

Usa `terminal create --worktree active --command "codex"` solo quando vuoi una nuova sessione nello stesso workspace. Per un agente in un worktree nuovo usa invece il flusso di creazione con `--agent`, che lo colloca nel primo terminale.

## Browser integrato

Il browser incorporato appartiene al workspace e può usare stato/sessioni specifici. La procedura pratica è **naviga → snapshot → interagisci → nuovo snapshot**:

```powershell
orca tab create --url https://example.test/login --json
orca snapshot --json
orca fill --element @e2 --value "utente-test" --json
orca click --element @e5 --json
orca snapshot --json
```

I riferimenti `@eN` provengono dallo snapshot corrente e scadono quando cambi pagina o scheda. Dopo navigazione, cambio tab o riferimento non più valido, acquisisci un nuovo snapshot. Per test indipendenti, usa profili browser separati così cookie e stato locale non si contaminano.

Il browser Orca è distinto dal browser esterno e dall’interfaccia desktop di Orca. Una pagina che dipende dall’host desktop non continua a funzionare se il desktop che la ospita è offline; per automazioni lunghe verifica la modalità di esecuzione supportata.

## Automazioni pianificate

Un’automazione esegue un prompt a orari ricorrenti. Per ogni esecuzione scegli l’ambito:

- `--repo <selettore>` quando una run deve creare/selezionare lavoro associato al repo;
- `--workspace <selettore>` quando deve usare un worktree esistente;
- setup di progetto/host per un ambiente remoto configurato.

Inizia disabilitando la schedulazione, verifica prompt e destinazione, ed esegui una prova manuale:

```powershell
orca automations create --name "Triage feriale" --trigger weekdays --time 09:00 --prompt "Riassumi nuovi bug aperti e segnala duplicati probabili; non modificare codice." --provider codex --repo id:<repoId> --disabled --json
orca automations list --json
orca automations run <automationId> --json
orca automations runs --id <automationId> --json
```

I trigger supportano preset come `hourly`, `daily`, `weekdays`, `weekly`, cron a cinque campi o RRULE. Con `--time` puoi specificare l’orario dei preset e con `--timezone` una zona IANA esplicita. Per un probe economico prima della run, la CLI supporta `--precheck`; fallo fallire rapidamente quando non c’è nulla da fare.

Usa `--reuse-session` soltanto per automazioni su workspace esistenti quando la persistenza della sessione è desiderata. Considera le credenziali disponibili, il costo delle run, cosa succede se l’agente modifica il repo e come vengono segnalati gli errori. Non schedulare azioni irreversibili senza un punto di approvazione adeguato.

## Skills Orca

Le skills spiegano a un agente quando e come usare capacità Orca; il runtime fornisce la guida associata alla versione della CLI. Le installazioni possono variare, perciò controlla i pacchetti disponibili con `orca skills list` e le istruzioni con `orca skills get <nome-skill>`. In particolare:

```powershell
orca skills get orca-cli
orca skills get orchestration
```

Usa la skill `orca-cli` per worktree, terminali, browser e automazioni; `orchestration` per Runs e worker supervisionati. Non dedurre opzioni mancanti dalla documentazione di una versione differente: consulta `--help` e la guida live.

## Errori frequenti

| Sintomo | Primo passo |
|---|---|
| Runtime non disponibile | `orca status --json`; apri Orca e riprova |
| Selettore workspace ambiguo | `orca worktree list --json`, poi usa l’ID completo o il path |
| Terminale non trovato/stale | Elenca i terminali del worktree e scegli l’handle attivo |
| Browser `stale_ref` | Acquisisci un nuovo snapshot e usa riferimenti aggiornati |
| Run automazione saltata | Controlla precheck e cronologia con `automations runs` |
| CLI non riconosce un’opzione | `<comando> --help` e skill live della build installata |

## Riferimenti

- [Orca CLI](https://www.onorca.dev/docs/cli/overview)
- [Automazioni](https://www.onorca.dev/docs/cli/automations)
- [Skills e MCP](https://www.onorca.dev/docs/cli/skills)
- La CLI installata: `orca skills get orca-cli --reference references/browser.md` e `.../references/automations.md`
