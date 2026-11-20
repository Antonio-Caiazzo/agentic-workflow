# Pianificatore Copilot CLI diretto — laboratorio personale

Questo documento descrive il workflow `.github/workflows/piano-direct.yml`, alternativa sperimentale al Pianificatore `gh-aw` fallito con HTTP 502/model catalog non disponibile. Il test si svolge esclusivamente in `Antonio-Caiazzo/agentic-workflow`; non modifica `Team-C01/NeighborCare`.

## Trigger e attivazione

1. Il PM prepara un'Issue con obiettivo, artefatti pertinenti, criteri di accettazione e test richiesti.
2. Un collaboratore con permesso GitHub **Write, Maintain o Admin** aggiunge la label `fase:piano` all'Issue **aperta**.
3. GitHub Actions esegue il job `generate` con permessi `contents:read` e `issues:read`.
4. Il controllo iniziale verifica il permesso dell'attore, lo stato dell'Issue e l'assenza di un precedente commento che inizi con `## Piano proposto dal Pianificatore AI`. Se esiste, **non consuma altri crediti AI**.
5. Uno script raccoglie l'Issue come dato non attendibile e include soltanto i quattro file fidati: `AGENTS.md`, `docs/processo-agentico.md`, `.github/agents/pianificatore.agent.md`, `README.md`. Non legge secret, né trasforma template di esempio in requisiti.
6. Copilot CLI `1.0.114` usa il secret personale `COPILOT_GITHUB_TOKEN`, il modello `auto`, MCP disabilitato e strumenti limitati alla lettura. Il risultato è testo Markdown, non modifiche o commit.
7. Un validatore controlla titolo, frase finale, lunghezza, argomenti obbligatori e pattern di credenziali; l'output non conforme **non viene pubblicato**.
8. Solo dopo una generazione valida, il job `publish` (con `issues:write`) ricontrolla Issue, label e commenti; se non esiste già un piano, pubblica **un solo commento**. Non modifica file, label o branch.

## Human-in-the-Loop

La pubblicazione del piano **non è un'approvazione**. Il TM deve esaminare lo scopo, i modelli, i rischi e i test, quindi scrivere nell'Issue un'approvazione esplicita (con versione/data). Un'altra fase, separata e deliberatamente non implementata in questo laboratorio, potrà autorizzare l'Implementatore.

La label `fase:piano` è il comando di avvio e la cronologia della Issue registra chi l'ha aggiunta. Rimuovere la label durante una generazione impedisce la pubblicazione nel controllo finale. Se il piano deve essere aggiornato, il TM decide come gestire quello esistente: il workflow **non sovrascrive né duplica** i commenti precedenti.

## Ruoli e limiti

- **PM/TM**: definisce l'Issue, aggiunge la label, approva o blocca il piano, decide quando passare all'implementazione.
- **Pianificatore AI**: formula una proposta documentale basata sugli input forniti; non implementa, non esegue test applicativi e non effettua revisioni umane.
- **Sviluppatore**: dopo approvazione, realizza la modifica su branch dedicato con Draft PR e test pertinenti.
- **Tester indipendente e Revisore**: verificano la PR sulla base di specifiche/Test Frame, non delle supposizioni del codice; un umano diverso dall'autore approva il merge.

Questo laboratorio prepara il solo **Pianificatore**. Le altre fasi restano manuali finché non vengono progettati e verificati workflow separati.

## Sicurezza, costi e verifiche

- `COPILOT_GITHUB_TOKEN` è configurato solo nei GitHub Actions Secrets e passato esclusivamente allo step di generazione. Non stampare mai il valore.
- Il job AI non riceve permessi di scrittura GitHub; l'output passa al job pubblicatore tramite un artifact con scadenza di un giorno.
- Non vengono avviati altri workflow AI, non si utilizzano API a pagamento esterne o fallback.
- La concorrenza per numero Issue e il controllo dei commenti prevengono duplicati ordinari.
- L'Issue è **input non attendibile**; il validatore limita il formato ma non garantisce la veridicità del piano. Serve approvazione umana.
- Il test automatizzato `check-planner.yml` verifica gli script, la sintassi dei workflow e i diff senza utilizzare AI.

**Test di accettazione:** sulla Issue #1, dopo aggiunta esplicita di `fase:piano`, il workflow deve risultare riuscito, creare un solo commento di piano, non modificare file e non avviare `gh-aw`. Una seconda aggiunta della stessa label deve saltare l'inferenza e non duplicare il commento.

L'archivio del workflow precedente è in `docs/archivio/piano-gh-aw.md`. Il file compilato `piano.lock.yml` precedente resta consultabile nella cronologia Git, ma non è più presente fra i workflow attivi.
