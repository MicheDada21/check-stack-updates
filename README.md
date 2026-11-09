# stack-update

Aggiorna dipendenze e strumenti dopo un controllo di compatibilità e l'approvazione dell'utente.

Fa parte della [Prompt Library di CH Labs](https://www.cryptohelvetia.com/ch-labs/prompt-library), dove la pagina di riferimento è [Update project dependencies and toolchain](https://www.cryptohelvetia.com/ch-labs/prompt-library/skills/stack-update). Il repo contiene una copia della skill, rigenerata a ogni aggiornamento della libreria.

## Installare

In Claude Code aggiungi una volta il marketplace e installa il plugin.

```
/plugin marketplace add MicheDada21/ch-labs-skills
/plugin install stack-update@ch-labs-skills
```

Da shell i comandi sono `claude plugin marketplace add MicheDada21/ch-labs-skills` e `claude plugin install stack-update@ch-labs-skills`. Nell'app desktop premi + accanto al campo del prompt, apri Plugins, aggiungi il marketplace `MicheDada21/ch-labs-skills` con Add marketplace e poi il plugin con Add plugin.

Per avere tutte le skill della libreria installa `tutte-le-skill@ch-labs-skills`. Con altri agenti copia la cartella `skills/stack-update` tra le skill dell'agente, per esempio in `~/.codex/skills/stack-update`.

## Requisiti

La skill è scritta per il setup dell'autore e legge file in `~/.agents` che questo repo non contiene, quindi va adattata prima dell'uso.

## Licenza

MIT, testo completo in [LICENSE](LICENSE).
