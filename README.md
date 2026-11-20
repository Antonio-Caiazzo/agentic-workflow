# Agentic Workflow — laboratorio diagnostico NeighborCare

Repository personale di prova: `Antonio-Caiazzo/agentic-workflow`. Il progetto ufficiale rimane in `Team-C01/NeighborCare` e **non viene modificato** da questo laboratorio.

## Pianificatore attivo

Il workflow [Pianificatore NeighborCare — Copilot CLI diretto](.github/workflows/piano-direct.yml) si attiva quando un collaboratore autorizzato aggiunge la label `fase:piano` a una Issue aperta.

- **Engine:** Copilot CLI `1.0.114` eseguita direttamente in GitHub Actions (senza `gh-aw`).
- **Modello:** `auto`, verificato nei test diagnostici della CLI.
- **Autenticazione:** secret Actions `COPILOT_GITHUB_TOKEN` (mai inserire il PAT nei file).
- **Risultato:** un solo commento di pianificazione; nessuna modifica al codice, nessun commit o PR dell'agente.
- **Sicurezza:** job di generazione in sola lettura, strumenti Copilot limitati, validazione del testo e pubblicazione in un job separato. Nessuna approvazione automatica del TM.
- **Idempotenza:** se esiste già il commento con il piano, non avvia un'altra richiesta AI.

Guida al flusso, responsabilità e verifiche: [Pianificatore diretto](docs/Pianificatore-Diretto.md).

## Prova controllata

Issue dimostrativa: [#1 — frase di benvenuto nel README](https://github.com/Antonio-Caiazzo/agentic-workflow/issues/1).

La label `fase:piano` è il trigger. Il Secret deve essere configurato prima dell'esecuzione; se manca, l'inferenza fallisce senza pubblicare commenti.

La pubblicazione del piano richiede **sempre una successiva approvazione esplicita del TM** prima di qualsiasi implementazione.

## Diagnosi precedente

Il percorso `gh-aw` ha fallito con HTTP 502 e, nella successiva ricompilazione, con un modello non disponibile nel catalogo del workflow. La CLI diretta `1.0.103` e `1.0.114` ha invece risposto correttamente su GitHub Actions.

- [Report di diagnosi gh-aw](docs/diagnosi-gh-aw-2026-11-20.md)
- [Vecchio sorgente gh-aw archiviato](docs/archivio/piano-gh-aw.md)
- [Diagnostic CLI 1.0.103](.github/workflows/diagnostic-copilot-103.yml)
- [Diagnostic CLI 1.0.114](.github/workflows/diagnostic-copilot-direct.yml)

Il vecchio workflow compilato `piano.lock.yml` è stato disattivato rimuovendolo dalla directory Actions; resta nella cronologia Git. Non ricompilare il vecchio sorgente in `.github/workflows/`, altrimenti ricreeresti un secondo trigger `fase:piano`.
