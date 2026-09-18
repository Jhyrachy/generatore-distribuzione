# Generatore di distribuzioni

Pagina statica: incolli una lista di valori, la pagina ne stima il profilo di
distribuzione e genera nuovi numeri che lo seguono.

Un solo file (`index.html`), nessuna libreria esterna, nessuna chiamata di rete:
i dati restano nel browser.

## Cosa fa

1. **Legge** i valori (separati da a capo, spazi, virgole o punti e virgola; la
   virgola decimale viene riconosciuta).
2. **Stima** per massima verosimiglianza sei modelli — normale, log-normale,
   gamma, esponenziale, uniforme e una densità empirica (KDE a nucleo gaussiano,
   banda di Silverman). I modelli non applicabili vengono esclusi: niente
   log-normale o gamma se compaiono valori ≤ 0.
3. **Confronta** i modelli con l'AIC e con la statistica di Kolmogorov-Smirnov,
   e sceglie quello con AIC minore.
4. **Genera** n nuovi numeri campionando dal modello scelto, con seed
   facoltativo per risultati riproducibili.

La densità empirica (KDE) è la scelta giusta quando nessuna distribuzione
teorica convince: segue il profilo dei dati senza assumerne la forma, quindi
riproduce anche campioni bimodali.

## Opzioni

- **Modello** — automatico (AIC minore) o scelto a mano.
- **Quanti numeri** — indipendente dalla numerosità di partenza.
- **Seed** — stesso seed, stessa sequenza.
- **Vincola al minimo/massimo osservati** — riprova il campionamento finché il
  valore cade nel range dei dati (utile per grandezze con limiti fisici).
- **Arrotonda a interi** — per conteggi.

Due grafici di controllo: istogramma con la densità stimata (e il profilo dei
numeri generati sovrapposto) e confronto tra le cumulative empiriche.

## Pubblicare su GitHub Pages

```bash
git init
git add index.html README.md
git commit -m "Generatore di distribuzioni"
git branch -M main
git remote add origin git@github.com:UTENTE/REPO.git
git push -u origin main
```

Poi su GitHub: **Settings → Pages → Source: Deploy from a branch**, ramo `main`,
cartella `/ (root)`. Dopo un minuto la pagina è su
`https://UTENTE.github.io/REPO/`.

Se il repository esiste già e vuoi tenere la pagina in una sottocartella, mettila
in `docs/` e scegli quella cartella come sorgente.

## Provare in locale

```bash
python3 -m http.server 8731
```

Poi apri <http://localhost:8731>. Va bene anche aprire `index.html` con un doppio
clic: non ci sono risorse esterne da caricare.

## Note sul metodo

- Le stime sono di massima verosimiglianza; per la gamma il parametro di forma
  si ottiene con iterazioni di Newton su `ln k − ψ(k) = ln x̄ − ln x`.
- L'AIC penalizza i modelli con più parametri: l'esponenziale è un caso
  particolare della gamma con k = 1, quindi la gamma la supera solo quando il
  guadagno di verosimiglianza giustifica il parametro in più.
- La statistica KS è la distanza massima tra cumulativa empirica e teorica:
  sotto 0.1 l'adattamento è buono, sopra 0.2 conviene passare alla KDE.
- Per la KDE la KS è calcolata su un sottoinsieme delle statistiche d'ordine,
  perché la sua cumulativa costa O(n) per ogni valutazione.
