# Agentic Workflow — test diagnostico personale

Repository pubblica di prova per isolare eventuali differenze tra l'esecuzione del Pianificatore Copilot in un'organizzazione GitHub e in un account personale.

- Origine del confronto: `Team-C01/NeighborCare`.
- Engine: GitHub Copilot tramite GitHub Agentic Workflows (`gh-aw`).
- Modello del Pianificatore: `gpt-5-mini` (i test CLI diretti usano `auto`).
- Permesso AI: secret `COPILOT_GITHUB_TOKEN` da inserire tramite GitHub Settings; non mettere mai token nei file.
- Scopo: un solo commento con il piano su una Issue dimostrativa, senza modifiche al codice.

La label `fase:piano` attiva la prova. Non applicarla finché il secret non è stato configurato.

Diagnosi e ricompilazione stabile del 20 novembre 2026: [report gh-aw](docs/diagnosi-gh-aw-2026-11-20.md). Nessuna nuova prova AI è autorizzata da questa modifica; attendere l'approvazione esplicita del test.
