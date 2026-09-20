# 5. Revisionare, verificare e consegnare

Il diff è il prodotto da revisionare. Leggi il risultato come faresti per una patch di un collega: controlla che il comportamento richiesto sia cambiato, che la soluzione sia coerente con il progetto e che la verifica sia proporzionata al rischio.

## Revisione pratica

1. **Guarda lo stato Git.** Individua file nuovi, modificati e cancellati, file generati e modifiche accidentali.
2. **Apri il diff rispetto al base corretto.** Orca confronta per default con il riferimento di partenza del worktree, ma puoi cambiare confronto dalla barra del diff.
3. **Segui il percorso di ogni cambiamento.** Parti dal comportamento richiesto e verifica chiamanti, dati, errori e compatibilità.
4. **Controlla test e copertura del caso.** Un test di regressione deve fallire prima della correzione o esercitare in modo significativo il comportamento guasto.
5. **Valuta dipendenze, configurazioni, migrazioni e segreti.** Questi cambiamenti spesso hanno effetti che un test unitario non rivela.
6. **Riprova i controlli pertinenti nel workspace finale.** Riporta quelli saltati, falliti o non eseguibili.

Per diff voluminosi, procedi file per file. Orca permette staging per file, hunk o linea. Lascia commenti inline sui punti che vuoi correggere e assegna all’agente una richiesta precisa. Verifica la patch aggiornata dopo ogni giro.

## Comandi utili

```powershell
git status --short --branch
git diff --stat
git diff
orca file open-changed --mode diff
```

Usa i comandi di test definiti nel repo, per esempio:

```powershell
npm test -- --runInBand
npm run lint
npm run typecheck
```

I nomi sono esempi: esegui solo script realmente presenti nel progetto.

## Cosa chiedere nel riepilogo dell’agente

Nel prompt chiedi una risposta breve che specifichi:

- comportamento modificato e file principali;
- test e verifiche eseguite, con esito;
- limiti, rischi o verifiche non svolte;
- eventuali passi manuali ancora necessari.

Confronta il riepilogo con il diff e l’output dei test. La risposta è un indice per la review, non una prova autonoma.

## Commit, push e review hosted

Quando il diff è pronto:

1. Metti in stage soltanto i cambiamenti che appartengono al task.
2. Usa un messaggio commit che descriva il cambiamento osservabile.
3. Esegui i pre-commit hook e risolvi gli errori alla fonte.
4. Fai push sul branch del worktree.
5. Crea la pull request o merge request con base, titolo, descrizione e stato draft corretti.
6. Associa l’issue appropriata e controlla CI/review.

Orca include un flusso grafico per diff, staging, commit, push e review. Puoi anche usare Git dalla shell: il worktree è un checkout Git vero. Dopo rebase, amend o squash, il push ordinario non deve diventare un force push automatico; se serve riscrivere la storia remota, verifica che la modifica sia intenzionale e usa il meccanismo esplicito con lease offerto dal flusso Git.

## Integrare i risultati di agenti concorrenti

Per worktree che propongono la stessa soluzione, scegli un branch candidato e porta dentro soltanto il codice selezionato. Per task indipendenti, integra in sequenza e rilancia le verifiche d’integrazione dopo ogni merge/cherry-pick. Non copiare file alla cieca se nel frattempo sono cambiati i contratti o la base.

## Chiusura del workspace

Aggiorna lo stato a `in-review` o `completed` quando corrisponde davvero. Dopo l’integrazione, archivia o rimuovi i workspace inutilizzati. Prima di eliminare verifica che commit non integrati e cambiamenti locali siano stati conservati. La rimozione del worktree e del branch è una decisione di cleanup distinta dalla review.

## Riferimenti

- [Diff viewer](https://www.onorca.dev/docs/review/diff-viewer)
- [Commit e push](https://www.onorca.dev/docs/review/commit-push)
- [Worktree e lifecycle](https://www.onorca.dev/docs/model/worktrees)
