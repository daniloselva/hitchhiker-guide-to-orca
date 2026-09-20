# 1. Cos’è Orca e come ragionare sul lavoro

Orca è un ambiente di sviluppo per lavorare con agenti di coding affiancati. Riunisce repository, worktree Git, terminali degli agenti, editor, diff, browser per workspace e strumenti di coordinamento. Gli agenti sono programmi separati (per esempio Codex o Claude Code): Orca li avvia e li colloca nel contesto di lavoro, ma non sostituisce Git, i test o la revisione umana.

## Il modello mentale

```text
Progetto / repository
  └── workspace (worktree Git o contesto folder)
        ├── branch e file di lavoro
        ├── sessioni e terminali
        ├── diff e note di stato
        └── browser e altri strumenti legati al workspace
```

Un **repository** è il progetto Git registrato. Il **base ref** è il riferimento predefinito da cui partono nuovi worktree. Un **worktree** è un checkout Git reale, con directory e branch propri. Una **sessione agente** è un processo agente avviato in un terminale all’interno di un workspace. Un **workspace folder** può rappresentare cartelle o raggruppamenti che non sono un singolo worktree Git.

Ogni task dovrebbe avere un workspace facile da riconoscere e da revisionare. Questo riduce le collisioni tra file, rende visibile il diff di ogni iniziativa e consente di fermare o archiviare il lavoro in modo indipendente. La separazione è soprattutto organizzativa e Git: un worktree non rende automaticamente sicuri i segreti, i comandi distruttivi, i servizi condivisi o gli effetti esterni.

Controlla anche come Orca avvia gli agenti: alcune configurazioni predefinite concedono loro modalità operative molto autonome. Rivedi le impostazioni di lancio dell’agente nel pannello Agents e scegli il livello adatto al repo e ai dati accessibili. Un worktree aiuta a isolare il codice, ma non è una barriera di sicurezza per credenziali, rete, database o altri servizi.

## I principi pratici

### 1. Un task, un workspace

Usa un worktree dedicato per una feature, un bug o un’indagine. Evita di mescolare task non correlati nello stesso branch: complicano il diff, il commit e la pulizia.

### 2. Definisci il risultato prima di avviare l’agente

Un buon task dice che cosa cambiare, dove, quali vincoli rispettare e come riconoscere il completamento. “Sistema l’autenticazione” è vago; “riproduci il redirect loop in `src/auth`, correggi il caso dei token scaduti, aggiungi un test di regressione e lancia i test auth” è verificabile.

### 3. Mantieni una sola fonte autorevole per il codice

Gli agenti che lavorano in worktree separati non condividono automaticamente modifiche non committate. Per integrare, confronta i diff e seleziona o ricomponi i cambiamenti nel branch che deve arrivare a review. Non trattare due implementazioni concorrenti come se fossero già una patch unica.

### 4. Isola senza dimenticare l’ambiente

Un nuovo worktree parte da un checkout pulito. Dipendenze, cache, file locali e configurazioni ignorate possono mancare. Prepara bootstrap e dati di test ripetibili; scegli con cura se condividere o copiare i file ignorati.

### 5. La revisione è una fase del lavoro

Prima di integrare, controlla diff, test, configurazioni, dipendenze e possibili effetti collaterali. Gli indicatori di stato dell’agente dicono se sta lavorando o aspettando input; non dimostrano che il risultato sia corretto.

### 6. Scegli il livello minimo di coordinamento che serve

- Un prompt diretto a un agente basta per il normale task individuale.
- Worktree separati sono utili per lavoro parallelo indipendente o per confrontare soluzioni.
- L’orchestrazione supervisionata serve quando vuoi ownership esplicita, dipendenze, inbox, domande bloccanti, esito registrato e supervisione di dispatch.
- Un handoff semplice trasferisce il lavoro a un altro agente/worktree senza introdurre un ciclo di coordinamento.

## Quando Orca aiuta di più

- Più task possono avanzare in parallelo senza toccare gli stessi file.
- Vuoi confrontare strategie diverse sullo stesso problema.
- Devi passare spesso fra progetti o sessioni agenti e vuoi il contesto visibile.
- Vuoi ispezionare i diff e i test prima di integrare.
- Vuoi eseguire agenti o build su una macchina remota mantenendo l’interfaccia sul desktop.

## Quando tenere il flusso semplice

Un singolo agente è spesso la scelta migliore per una modifica piccola, una sequenza strettamente dipendente o un task che richiede contesto condiviso e molte decisioni interattive. Aggiungere agenti ha un costo di avvio, token, revisione e integrazione; parallelizza solo se il tempo risparmiato supera quel costo.

## Riferimenti

- [Che cos’è Orca](https://www.onorca.dev/docs)
- [Worktree](https://www.onorca.dev/docs/model/worktrees)
- [Sessioni degli agenti](https://www.onorca.dev/docs/model/agents-sessions)
