# Guida pratica a Orca

Questa guida accompagna dall’apertura di un repository al lavoro quotidiano con uno o più agenti, fino alla revisione, alla consegna e all’automazione. È pensata per chi sviluppa software e vuole usare Orca come ambiente di lavoro, non come sostituto del giudizio tecnico.

## Percorso consigliato

1. [Cos’è Orca e come ragionare sul lavoro](01-orca-e-principi.md)
2. [Preparare un progetto: scaffolding e worktree](02-progetto-e-scaffolding.md)
3. [Il ciclo quotidiano con un agente](03-workflow-singolo.md)
4. [Parallelismo e orchestrazione supervisionata](04-parallelismo-e-orchestrazione.md)
5. [Revisionare, verificare e consegnare](05-review-e-consegna.md)
6. [CLI, browser e automazioni](06-cli-browser-automazioni.md)
7. [Ricette e ottimizzazioni per use case](07-ricette-e-ottimizzazioni.md)

## Flusso essenziale

```text
repository pronto
    → worktree dedicato al task
    → agente con obiettivo e criteri chiari
    → verifica e revisione del diff
    → commit / push / PR
    → archiviazione o rimozione del workspace
```

Orca è centrata su repository e worktree Git. In pratica, il punto di partenza è un repository registrato in Orca; il punto di arrivo è una modifica verificata che puoi spiegare e integrare. Per un task piccolo basta un worktree e una sessione. Usa più agenti quando puoi isolare attività o confrontare approcci senza creare più lavoro di coordinamento.

## Convenzioni della guida

- Negli esempi, `orca` è il comando CLI. Se la tua installazione espone un eseguibile diverso o hai una build di sviluppo, usa quello associato alla sessione.
- Gli identificativi come `<repoId>` e `<taskId>` sono segnaposto: recuperali con il comando di discovery prima di riutilizzarli.
- Per controllare i comandi e le opzioni supportati dalla tua versione: `orca skills get orca-cli`, `orca skills get orchestration` e `orca <comando> --help`.
- L’interfaccia e le opzioni cambiano nel tempo. La documentazione ufficiale e la guida servita dalla CLI installata sono i riferimenti aggiornati.

## Indice per obiettivo

| Obiettivo | Vai a |
|---|---|
| Capire repository, worktree, agenti e isolamento | [Principi](01-orca-e-principi.md) |
| Preparare un repo perché i workspace partano pronti | [Scaffolding](02-progetto-e-scaffolding.md) |
| Delegare un task a un agente e seguirlo | [Workflow singolo](03-workflow-singolo.md) |
| Dividere, confrontare o coordinare più agenti | [Orchestrazione](04-parallelismo-e-orchestrazione.md) |
| Controllare codice, test, commit e pull request | [Revisione e consegna](05-review-e-consegna.md) |
| Pilotare Orca da shell, usare browser e pianificare prompt | [CLI e automazioni](06-cli-browser-automazioni.md) |
| Scegliere il flusso adatto al caso concreto | [Ricette](07-ricette-e-ottimizzazioni.md) |

## Riferimenti ufficiali

- [Che cos’è Orca](https://www.onorca.dev/docs)
- [Il modello dei worktree](https://www.onorca.dev/docs/model/worktrees)
- [Prima sessione con tre agenti](https://www.onorca.dev/docs/first-session)
- [Orca CLI](https://www.onorca.dev/docs/cli/overview)
- [Orchestrazione](https://www.onorca.dev/docs/cli/orchestration)
- [Automazioni pianificate](https://www.onorca.dev/docs/cli/automations)
- [Modalità locali e remote](https://www.onorca.dev/docs/ways-to-run)
