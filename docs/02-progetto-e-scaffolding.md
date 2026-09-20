# 2. Preparare un progetto: scaffolding e worktree

Una buona esperienza in Orca si prepara nel repository. Ogni nuovo workspace dovrebbe essere facile da creare, inizializzare, testare e rimuovere. Se oggi un collaboratore deve ricordare una lunga sequenza di passaggi manuali, anche l’agente ripeterà quella fragilità a ogni worktree.

## Prima configurazione del repository

1. **Aggiungi o importa il repository in Orca.** Verifica che sia quello corretto e che il branch predefinito coincida con il ramo di sviluppo atteso.
2. **Imposta il base ref.** Controllalo prima di creare worktree. Se serve correggerlo da CLI:

   ```powershell
   orca repo list --json
   orca repo set-base-ref --repo id:<repoId> --ref origin/main --json
   ```

   Sostituisci `origin/main` con il vero branch di base del progetto. In un repository il cui branch di integrazione si chiama `master`, usa quello.

3. **Rendi ripetibile il bootstrap.** Metti nel repo gli script documentati per installare dipendenze, generare codice e preparare i test. Mantieni distinti bootstrap e verifiche: installazione, lint, test unitari e suite di integrazione hanno tempi e prerequisiti diversi.
4. **Scrivi le istruzioni per gli agenti.** Aggiungi o aggiorna il file di istruzioni usato dal tuo agente (per esempio `AGENTS.md`) con architettura, convenzioni, comandi validi e vincoli stabili. Mantieni queste istruzioni brevi, verificabili e versionate insieme al codice; inserisci i dettagli specifici del task nel prompt, non in regole permanenti.
5. **Configura gli hook di progetto.** Usa le impostazioni di setup/archive di Orca se il repo richiede comandi da eseguire alla creazione o rimozione dei workspace. Parti da uno script idempotente, con output leggibile e comportamento d’errore chiaro. Non duplicare uno script già affidabile in più punti.
6. **Verifica da un worktree nuovo.** Una configurazione che funziona solo nel checkout principale non è pronta per il lavoro concorrente.

## Cosa rendere ripetibile

- versione runtime e package manager, con lockfile aggiornato;
- installazione delle dipendenze e generazione di asset;
- servizi locali o fixture necessarie ai test;
- comandi standard di lint, type-check e test;
- dati finti sicuri e indipendenti dall’ambiente personale;
- istruzioni per i segreti richiesti, senza inserire credenziali nel repository.

Mantieni brevi e non interattivi i passaggi automatici. Un hook che aspetta input o si blocca su un servizio non disponibile rallenta ogni creazione di workspace. Se un controllo è lento ma non indispensabile per iniziare, documentalo come comando separato.

## File ignorati: condividere o copiare

Il worktree nuovo non eredita automaticamente file esclusi da Git. Orca supporta configurazioni differenti per due esigenze:

- **Directory condivise** per contenuti ricostruibili e voluminosi. In `orca.yaml` alla radice puoi indicare directory ignorate già esistenti nel checkout principale, per esempio:

  ```yaml
  worktree:
    sharedDirectories:
      - node_modules
      - .cache
  ```

  Le directory indicate vengono collegate/condivise; non sono copie indipendenti. Usale solo se la condivisione tra branch e agenti è compatibile con il progetto. Se branch diversi richiedono versioni di dipendenze incompatibili, ricostruiscile in ogni workspace.

- **Copie per-worktree** per file ignorati che devono esistere all’avvio ma poter divergere. Un file `.worktreeinclude` può elencare percorsi letterali, uno per riga:

  ```text
  .env.local
  .vscode/settings.json
  ```

  Questi file vengono copiati, così ogni worktree ha la propria versione. Includi configurazioni segrete solo se è appropriato copiarle in ogni nuova directory locale e controlla i permessi e il ciclo di vita di quei file. Non aggiungere segreti al versionamento.

Entrambe le tecniche riguardano percorsi ignorati e presenti. `.worktreeinclude` non supporta glob. Leggi la [documentazione Orca sui worktree](https://www.onorca.dev/docs/model/worktrees) prima di adottarle e prova il flusso su un workspace appena creato.

## Creare il primo worktree

Da interfaccia, crea un workspace dal repository, scegli un nome che descriva il task e seleziona il branch da cui partire. Se usi il comando CLI:

```powershell
orca worktree create --repo id:<repoId> --name fix-login-redirect --base-branch origin/main --agent codex --prompt "Riproduci e correggi il redirect loop descritto nel task. Aggiungi un test di regressione e riporta i comandi eseguiti." --json
```

`--agent` avvia l’agente nel primo terminale; `--prompt` passa il task iniziale. Evita di avviare nuovamente lo stesso agente con un secondo `terminal create`. Per un nuovo worktree il cui parent in Orca deve essere esplicito, usa `--parent-worktree active`; per un’attività indipendente dalla gerarchia corrente, `--no-parent`. La parentela visiva Orca non cambia la base Git.

Controlla il risultato e i workspace esistenti con:

```powershell
orca worktree current --json
orca worktree list --repo id:<repoId> --json
```

## Scaffolding di un repository nuovo

Per iniziare un progetto da zero:

1. Crea il repository e il branch di integrazione con il flusso Git adottato dal team.
2. Aggiungi il repo ad Orca e controlla il base ref.
3. Aggiungi README, lockfile, script di bootstrap e comandi di verifica prima di distribuire i task agli agenti.
4. Prova la creazione di un worktree pulito: installazione, avvio, test e cleanup.
5. Solo dopo questa prova avvia task paralleli o automazioni.

Orca gestisce i worktree e i terminali; il repository deve definire come si installa, testa e rilascia il software.

## Checklist di prontezza

- [ ] Repository e remote corretti.
- [ ] Base ref esplicito e aggiornato.
- [ ] Bootstrap eseguibile senza passaggi nascosti.
- [ ] Comandi di test e lint documentati.
- [ ] File ignorati e cache gestiti intenzionalmente.
- [ ] Nessun segreto tracciato o copiato senza necessità.
- [ ] Un worktree nuovo è stato provato dall’inizio alla fine.

## Riferimenti

- [Worktree e directory condivise](https://www.onorca.dev/docs/model/worktrees)
- [Prima sessione](https://www.onorca.dev/docs/first-session)
- [Orca CLI](https://www.onorca.dev/docs/cli/overview)
