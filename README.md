# check-stack-updates

Aggiorna dipendenze e strumenti dopo un controllo di compatibilità e l'approvazione dell'utente.

Fa parte della [Prompt Library di CH Labs](https://www.cryptohelvetia.com/ch-labs/prompt-library), dove la pagina di riferimento è [Check stack updates](https://www.cryptohelvetia.com/ch-labs/prompt-library/skills/check-stack-updates). Il repo contiene una copia della skill, rigenerata a ogni aggiornamento della libreria.

## Installare

In Claude Code aggiungi una volta il marketplace e installa il plugin.

```
/plugin marketplace add MicheDada21/ch-labs-skills
/plugin install check-stack-updates@ch-labs-skills
```

Da shell i comandi sono `claude plugin marketplace add MicheDada21/ch-labs-skills` e `claude plugin install check-stack-updates@ch-labs-skills`. Nell'app desktop premi + accanto al campo del prompt, apri Plugins, aggiungi il marketplace `MicheDada21/ch-labs-skills` con Add marketplace e poi il plugin con Add plugin.

Con altri agenti copia la cartella `skills/check-stack-updates` tra le skill dell'agente, per esempio in `~/.codex/skills/check-stack-updates`.

## Requisiti

La skill è scritta per il setup dell'autore e legge file in `~/.agents` che questo repo non contiene, quindi va adattata prima dell'uso.

## Licenza

MIT, testo completo in [LICENSE](LICENSE).
