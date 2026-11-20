# Diagnosi gh-aw — 20 novembre 2026

## Esito

Non esiste un aggiornamento stabile del runtime rispetto alla baseline esaminata: la CLI installata e il lock erano già a **v1.2.2**, che l'API GitHub `releases/latest` indica come ultima stabile. **v1.2.3 è una prerelease** e non è stata installata. Questa PR ricompila il Pianificatore e documenta le incongruenze, senza dichiarare risolto l'HTTP 502 e senza eseguire nuove richieste AI.

Repository: `Antonio-Caiazzo/agentic-workflow`. Baseline remota: `7740a0a5e584bfe0910650bba5161cafadf24b5d`. Branch: `chore/upgrade-gh-aw-stable`.

## Evidenze precedenti

Le esecuzioni esistenti sono state consultate in sola lettura:

- [Copilot CLI 1.0.103 diretto: successo](https://github.com/Antonio-Caiazzo/agentic-workflow/actions/runs/44710984292).
- [Copilot CLI 1.0.114 diretto: successo](https://github.com/Antonio-Caiazzo/agentic-workflow/actions/runs/44710534954).
- [Pianificatore gh-aw: fallimento](https://github.com/Antonio-Caiazzo/agentic-workflow/actions/runs/44709745147).

L'HTTP 502 è il sintomo segnalato nel task. Questi risultati non isolano la causa fra proxy di inferenza, harness, configurazione del modello e servizio remoto; non giustificano cambiare il PAT o attribuire il problema all'organizzazione.

I test diretti usano `auto`; il sorgente del Pianificatore usa `gpt-5-mini`. Non costituiscono pertanto un confronto perfettamente equivalente del modello.

## Versioni controllate

| Componente | Prima | Dopo |
| --- | --- | --- |
| gh-aw CLI e compiler | v1.2.2 | v1.2.2 |
| gh-aw-actions/setup | v1.2.2, SHA 53d0749e19424a918acd82b4d140264ce48f2f14 | stesso SHA |
| Copilot CLI del Pianificatore | 1.0.103 | 1.0.103 |
| AWF e immagini firewall | 0.29.11 | 0.29.11 |
| MCP Gateway | v0.5.6 | v0.5.6 |
| GitHub MCP Server | v2.1.0 | v2.1.0 |
| Modello nel sorgente e nelle variabili COPILOT_MODEL | gpt-5-mini | gpt-5-mini |
| Modello nei metadati e configurazione generata del proxy | auto | gpt-5-mini |

I digest dei container e gli SHA delle altre Actions rimangono quelli esistenti. Il modello del sorgente non è stato modificato. Il lock originale conteneva indicazioni discordanti sul modello: la ricompilazione riallinea metadati, configurazione del proxy e variabili di esecuzione a `gpt-5-mini`. L'effetto su un'esecuzione reale resta da verificare.

Il manifest rigenerato descrive il solo agente `pianificatore` presente in questa repository. I vecchi riferimenti a tester, revisore e implementatore erano metadati residui: nessun file di agente è stato eliminato.

## Operazioni ed errori dell'upgrade

Il clone HTTPS via terminale fallisce con `Could not resolve host: github.com`. È stata recuperata tramite il connettore GitHub una copia esatta dei nove file della baseline, verificabile dai blob Git. La baseline Git locale è uno snapshot tecnico; il commit remoto della PR discende dalla baseline remota reale, non dalla cronologia sintetica locale.

`gh extension upgrade gh-aw` termina con codice 1: `unable to retrieve latest version for extension "aw"`. Il terminale non riesce a raggiungere GitHub; questo errore non dimostra che il PAT Copilot non funzioni. La versione già installata è stata confrontata con l'API ufficiale tramite il connettore: è già l'ultima stabile.

È stato eseguito:

```sh
gh aw upgrade --no-agent --no-mcp --no-skill
```

Le opzioni preservano i profili personalizzati ed evitano di aggiungere configurazioni Copilot e skill non necessarie. Nessun codemod necessario. Il comando termina con codice 0, ma segnala:

- impossibilità di aggiornare i pin dei container per errore di consultazione del registry;
- sei warning CTR-002, perché la mancata risoluzione remota lascia temporaneamente `gh-aw-actions/setup@v1.2.2` come tag.

Il primo risultato non è quello pubblicato. Il tag annotato `github/gh-aw-actions v1.2.2` è stato risolto tramite API GitHub al commit `53d0749e19424a918acd82b4d140264ce48f2f14`, identico a quello della baseline, e il lock è stato ricompilato dal Markdown:

```sh
gh aw compile piano \
  --action-mode action \
  --action-tag 53d0749e19424a918acd82b4d140264ce48f2f14 \
  --no-check-update
```

Esito: **un workflow compilato, zero warning**, codice 0. Il compilatore registra lo SHA esplicito anche nel campo descrittivo `version` dell'Action; non è un cambio di commit del runtime. Nessuna modifica manuale al lock.

`.gitattributes` è il file generato dall'upgrade per riconoscere i lock come file generati. README aggiornato per distinguere il modello del Pianificatore da quello dei test diretti.

## Verifiche deterministiche

- Compilazione e lint AST integrato di gh-aw: riusciti, zero warning.
- Ricompilazione ripetuta: identico hash SHA-256 del lock.
- Sintassi YAML dei quattro workflow, frontmatter e JSON dei manifest: valida.
- Tutte le Actions del lock fissate a SHA completi; immagini del manifest fissate a digest SHA-256 e invariate.
- Trigger, permessi dei job, riferimenti ai Secret e budget del sorgente preservati.
- AGENTS.md, agente personalizzato, documentazione preesistente e diagnostici CLI preservati byte per byte, salvo l'aggiornamento dichiarato del README.
- `git diff --check`: superato.
- Nessuna CI di PR è presente nella baseline; i trigger esistenti non avviano AI alla creazione di questa PR.
- actionlint, shellcheck, scanner Docker e controlli online dei registry non eseguiti: strumenti non disponibili o rete del terminale non accessibile. Il lint AST integrato non sostituisce questi strumenti.
- UC, Test Frame e test applicativi: N/A, manutenzione del workflow diagnostico.

## Test AI in attesa di approvazione

Non sono stati avviati workflow, applicate label, rilanciate esecuzioni, richieste review Copilot o inviate richieste di inferenza. Nessun Secret è stato letto o modificato. Nessuna modifica a `Team-C01/NeighborCare`, nessun merge e nessun servizio a pagamento introdotto.

L'esito dell'HTTP 502 resta **non verificato**. Una nuova prova richiede approvazione esplicita dell'utente e deve considerare che il Pianificatore può invocare anche la fase di threat detection. Questa PR non cambia il runtime stabile: per provare un runtime più recente occorre autorizzare separatamente una prerelease oppure attendere una nuova release stabile.

## Fonti ufficiali

- [Ultima release stabile, API](https://api.github.com/repos/github/gh-aw/releases/latest).
- [Release v1.2.2](https://github.com/github/gh-aw/releases/tag/v1.2.2).
- [Prerelease v1.2.3](https://github.com/github/gh-aw/releases/tag/v1.2.3).
- [Tag annotato gh-aw-actions v1.2.2](https://api.github.com/repos/github/gh-aw-actions/git/tags/5c7541d098b1fd0b039865a2e69849b0fe3c679c).
- [Versionamento e upgrade](https://github.github.com/gh-aw/reference/releases/).

