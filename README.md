# Scribe

### Generazione personalizzata di commenti al codice tramite AI per Visual Studio Code

**Scribe** è un'estensione per Visual Studio Code che utilizza i **Large Language Model (LLM)** per generare automaticamente commenti al codice, adattandoli allo stile personale dello sviluppatore.

A differenza dei tradizionali strumenti di generazione automatica, Scribe non si limita a produrre commenti generici: attraverso dei **profili utente (persona)** è in grado di tenere conto della lingua, del tono e degli esempi di scrittura dello sviluppatore, adattando progressivamente le generazioni in base al feedback ricevuto.

> 🎓 Progetto sviluppato nell'ambito della **tesi di laurea triennale in Informatica** presso l'Università degli Studi di Salerno, Anno Accademico 2025–2026.

---

## ✨ Funzionalità

* 🧠 **Generazione personalizzata**
  Genera commenti coerenti con la lingua, il tono e lo stile di documentazione preferiti dallo sviluppatore.

* 👤 **Profili utente (persona)**
  Permette di creare più profili con stili differenti e selezionare quello da utilizzare durante la generazione.

* 🔄 **Apprendimento incrementale**
  I commenti accettati o modificati dall'utente possono essere aggiunti al profilo, permettendo a Scribe di affinare progressivamente lo stile appreso.

* ⚡ **Suggerimenti inline**
  Scribe può rilevare automaticamente simboli privi di documentazione e proporre commenti direttamente nell'editor tramite ghost text.

* 🖱️ **Generazione esplicita**
  È possibile selezionare del codice e richiedere la generazione di un commento dal menu contestuale di Visual Studio Code.

* 🌍 **Supporto multi-linguaggio**
  Attualmente sono supportati **13 linguaggi di programmazione**.

* 🎯 **Nessun fine-tuning**
  La personalizzazione viene ottenuta tramite prompt engineering, esempi e feedback dell'utente, senza modificare o riaddestrare il modello.

* 💾 **Persistenza delle preferenze**
  I profili vengono salvati e mantenuti tra sessioni differenti di Visual Studio Code.

---

## 💡 Perché Scribe?

Gli assistenti di programmazione basati su LLM sono già in grado di generare commenti utili, ma tendono spesso a produrre output standardizzati che non riflettono lo stile personale dello sviluppatore.

Scribe parte da una domanda semplice:

> **E se un'AI potesse imparare il modo in cui scrivi i tuoi commenti?**

Ogni persona descrive lo stile di documentazione di uno sviluppatore attraverso:

```text
Lingua
Tono
Esempi
```

Ad esempio:

```text
Persona: Mario
Lingua: Italiano
Tono: Tecnico e conciso

Esempi:
- Inizializza il vettore
- Calcola la somma
```

Durante la generazione, questi esempi vengono utilizzati come riferimenti stilistici per il modello. L'output viene quindi condizionato non soltanto dal codice da documentare, ma anche dalle preferenze dello sviluppatore.

La personalizzazione viene ottenuta senza effettuare fine-tuning del modello.

---

## 🧩 Come funziona

L'architettura di Scribe è composta da diversi moduli che collaborano per rilevare il codice da documentare, costruire il prompt e generare il commento.

```text
┌──────────────────────────────────────────┐
│              Visual Studio Code          │
│                                          │
│  ┌──────────────┐     ┌───────────────┐  │
│  │ Profilo      │     │ Codice /      │  │
│  │ utente       │     │ Editor        │  │
│  └──────┬───────┘     └───────┬───────┘  │
│         │                      │          │
│         └──────────┬───────────┘          │
│                    ▼                      │
│             Symbol Detector               │
│                    │                      │
│                    ▼                      │
│             Prompt Builder                │
│                    │                      │
└────────────────────┼─────────────────────┘
                     │
                     ▼
             Hugging Face API
                     │
                     ▼
              Qwen2.5-Coder-7B
                     │
                     ▼
             Commento generato
                     │
                     ▼
              Feedback utente
                     │
                     └──────► Aggiornamento profilo
```

I principali componenti sono:

* **Gestione dei profili** — memorizza lingua, tono ed esempi dello sviluppatore.
* **Rilevamento dei simboli** — identifica funzioni, metodi, classi, costruttori e altri simboli supportati.
* **Costruzione del prompt** — combina il profilo utente con il contesto del codice.
* **Interfaccia con il modello** — comunica con il modello tramite Hugging Face.
* **Generazione esplicita** — genera commenti su codice selezionato dall'utente.
* **Generazione implicita** — propone automaticamente suggerimenti inline nell'editor.

---

## 🚀 Modalità di utilizzo

Scribe offre due modalità di interazione.

### 1. Modalità esplicita

Seleziona il blocco di codice da documentare e scegli:

```text
Tasto destro
    ↓
Scribe: Genera Commento
```

Scribe mostra quindi un selettore con i profili disponibili e utilizza quello scelto per generare il commento.

Dopo la generazione è possibile:

* **Sì, impara** — accettare il commento e aggiungerlo agli esempi del profilo;
* **Modifica e impara** — modificare il commento prima di salvarlo come nuovo esempio;
* **Scarta** — rifiutare il commento senza modificare il profilo.

Il processo può quindi essere rappresentato come:

```text
Generazione
     ↓
Valutazione
     ↓
Accetta / Modifica / Scarta
     ↓
Aggiornamento del profilo
     ↓
Generazioni successive più personalizzate
```

---

### 2. Modalità implicita

Scribe può anche lavorare automaticamente in background.

Quando il cursore rimane fermo su una riga contenente un simbolo riconosciuto, il sistema verifica se quest'ultimo dispone già di un commento.

Se non è presente, Scribe genera automaticamente un suggerimento e lo visualizza come **ghost text** direttamente nell'editor.

Ad esempio:

```python
def binary_search(arr, x):
    # Implementa la ricerca binaria su un array ordinato.
```

Il suggerimento può essere accettato premendo **Tab** oppure ignorato continuando a scrivere.

L'accettazione del suggerimento viene inoltre interpretata come feedback positivo e può contribuire all'aggiornamento del profilo attivo.

---

## 👤 Profili utente

Il concetto di **persona** è il cuore del meccanismo di personalizzazione di Scribe.

Ogni profilo contiene quattro informazioni principali:

| Attributo | Descrizione                                      |
| --------- | ------------------------------------------------ |
| `nome`    | Nome del profilo                                 |
| `lingua`  | Lingua utilizzata per i commenti                 |
| `tono`    | Tono comunicativo desiderato                     |
| `esempi`  | Commenti rappresentativi dello stile dell'utente |

Un esempio:

```text
Nome: Mario
Lingua: Italiano
Tono: Tecnico e conciso

Esempi:
- Inizializza il vettore
- Calcola la somma
```

Gli esempi vengono utilizzati come riferimento tramite **few-shot prompting**, permettendo al modello di apprendere la struttura e lo stile desiderati senza alcun fine-tuning.

I profili vengono salvati persistentemente nello storage globale di Visual Studio Code.

Per evitare una crescita indefinita del profilo, Scribe conserva al massimo **cinque esempi**, mantenendo i primi due esempi forniti dall'utente come riferimenti stilistici stabili.

---

## 🧠 Modello AI

Scribe utilizza attualmente:

**Qwen2.5-Coder-7B-Instruct**

tramite la **Hugging Face Inference API**.

La scelta di un modello specializzato nella comprensione del codice e con 7 miliardi di parametri è stata effettuata considerando il compromesso tra:

* capacità di comprensione del codice;
* qualità della generazione;
* latenza;
* requisiti computazionali.

La personalizzazione viene invece ottenuta attraverso:

```text
Prompt Engineering
       +
Esempi dello sviluppatore
       +
Feedback dell'utente
       =
Generazione personalizzata
```

La generazione utilizza inoltre parametri orientati alla produzione di commenti brevi e relativamente consistenti:

```text
temperature: 0.2
max_tokens: 80
```

---

## 🌍 Linguaggi supportati

Scribe supporta attualmente il rilevamento dei simboli nei seguenti **13 linguaggi**:

* TypeScript
* JavaScript
* Python
* C
* C++
* Rust
* Go
* Java
* C#
* PHP
* Ruby
* Swift
* Kotlin

Per i linguaggi non supportati viene utilizzato un fallback basato su pattern generici in stile C.

Il rilevamento dei simboli è attualmente realizzato tramite **espressioni regolari specifiche per linguaggio**.

---

## 🏗️ Architettura

Scribe è sviluppato in **TypeScript** come estensione di Visual Studio Code.

I principali componenti del sistema sono:

```text
src/
├── extension.ts
├── DatabaseManager
├── huggingface.ts
├── symbolDetector.ts
└── ...
```

### `extension.ts`

Gestisce l'attivazione dell'estensione e coordina i principali componenti del sistema.

### `DatabaseManager`

Gestisce il salvataggio e il caricamento dei profili utente.

### `huggingface.ts`

Gestisce la comunicazione con la Hugging Face Inference API e la costruzione delle richieste al modello.

### `symbolDetector.ts`

Si occupa di:

* riconoscere il tipo di simbolo;
* estrarre il blocco di codice da analizzare;
* determinare il prefisso del commento in base al linguaggio.

### Moduli di interazione

Implementano le due modalità di generazione:

* generazione esplicita tramite selezione;
* generazione implicita tramite ghost text.

---

## ⚙️ Requisiti

Per eseguire Scribe sono necessari:

* [Visual Studio Code](https://code.visualstudio.com/)
* Node.js
* npm
* una API key di Hugging Face
* una connessione Internet per l'inferenza del modello

---

## 🔧 Installazione

Clona il repository:

```bash
git clone https://github.com/xRubby/Scribe.git
cd Scribe
```

Installa le dipendenze:

```bash
npm install
```

Compila l'estensione:

```bash
npm run compile
```

Successivamente apri il progetto in Visual Studio Code e premi:

```text
F5
```

per avviare l'**Extension Development Host**.

---

## 🔑 Configurazione

Scribe richiede una API key di Hugging Face per effettuare le richieste al modello.

All'interno delle impostazioni di Visual Studio Code è possibile configurare:

```text
scribe.apiKey
```

È possibile cercare direttamente questa impostazione tramite la barra di ricerca delle impostazioni di VS Code.

> ⚠️ **Non inserire mai la tua API key direttamente nel codice o nel repository Git.**

---

## 🗺️ Sviluppi futuri

Tra le possibili evoluzioni del progetto:

* [ ] Selezione dinamice degli esempi più rilevanti in base al codice da commentare
* [ ] Studi con un numero maggiore di sviluppatori
* [ ] Possibilità di eseguire modelli localmente

---

## 🎓 Contesto accademico

Scribe è stato sviluppato come progetto della **tesi di laurea triennale in Informatica** presso l'Università degli Studi di Salerno.

**Titolo:**
*Scribe: un agente AI per la generazione personalizzata di commenti nel codice*

**Autore:** Ruben Gigante
**Relatore:** Prof. Fabio Palomba
**Anno Accademico:** 2025–2026

---

## 🤝 Contributi

Scribe nasce principalmente come progetto di tesi, ma idee, segnalazioni e contributi sono benvenuti.

Se trovi un problema o hai un'idea per migliorare l'estensione, puoi aprire una **Issue** oppure proporre una **Pull Request**.
