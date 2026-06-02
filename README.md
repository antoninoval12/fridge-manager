# Fridge Manager

Sistema informativo per la gestione intelligente degli alimenti del frigo e non solo.  
Progetto per l'esame di **Basi di Dati**.

## Stack tecnologico

- **Backend:** Django (Python)
- **Database:** SQLite possibilità di poi passarlo a MySQL in fase di produzione non di implementazione
- **Frontend:** template Django + Bootstrap 5
- JavaScript ridotto al minimo (solo il bundle di Bootstrap per navbar e conferme)

---

## Funzionalita'

1. Registrazione e autenticazione degli utenti (login / logout / registrazione).
2. Creazione e **condivisione** dei frigoriferi con ruolo proprietario / membro.
3. Gestione dei prodotti in frigorifero (inserimento, modifica quantita'/stato, rimozione) con vista per categoria.
4. Controllo automatico delle scadenze e generazione di **notifiche**.
5. Gestione delle **ricette** e suggerimento delle ricette realizzabili con gli ingredienti disponibili.

---

## Requisiti

- Python 3.12 (consigliato per non avere problemi nella parte di admin)
- pip
- venv

---

## Installazione e avvio

### 1. Clonare il repository

```bash
git clone <url-del-repository>
cd fridge-manager
```

### 2. Creare e attivare un ambiente virtuale

```bash
python -m venv venv
```

Windows:

```bash
venv\Scripts\activate
```

Linux/macOS:

```bash
source venv/bin/activate
```

### 3. Installare le dipendenze

```bash
pip install -r requirements.txt
```

### 4. Applicare le migrazioni

```bash
python manage.py makemigrations
python manage.py migrate
```

### 5. Caricare i dati di esempio

Sono disponibili due modalità.

#### Opzione 1 - Fixture JSON (consigliata)

Carica direttamente un dataset già pronto:


```bash
python manage.py loaddata dati_esempio2.json
```

Questa modalità importa i dati esattamente come sono stati salvati nella fixture (utenti, frigoriferi, prodotti, categorie, ricette e notifiche).

---

#### Opzione 2 - Popolamento automatico

```bash
python manage.py popola_db
python manage.py controlla_scadenze
```

Questa modalità genera automaticamente dati di esempio tramite il comando personalizzato del progetto.

Vengono creati:

- utenti demo
- frigoriferi
- categorie
- prodotti
- ricette
- notifiche di scadenza

È utile quando si vuole rigenerare rapidamente un database di test senza utilizzare una fixture salvata.

---

### Differenza tra le due modalità

| Metodo | Caratteristiche |
|----------|----------|
| `loaddata dati_esempio2.json` | Importa una fixture alternativa con dati già pronti |
| `popola_db` | Genera automaticamente nuovi dati di esempio |
| `controlla_scadenze` | Calcola e crea le notifiche in base alle scadenze presenti |

Se desideri avere sempre gli stessi dati per test e dimostrazioni, è consigliato usare **loaddata**.

Se invece vuoi ricreare un database di prova da zero, è consigliato usare **popola_db**.

### 6. Avviare il server

```bash
python manage.py runserver
```

Applicazione:

```text
http://127.0.0.1:8000/
```

Pannello di amministrazione:

```text
http://127.0.0.1:8000/admin/
```

---

## Avvio rapido (primo avvio)

Dopo aver clonato il progetto:

```bash
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py loaddata dati_esempio.json
python manage.py runserver
```

---

## Esecuzione dei test

```bash
python manage.py test
```

---

## Produrre il dump del database

Dopo aver popolato il database:

```bash
python manage.py dumpdata --indent 2 > dump_completo.json
```

---

## Note sulla sicurezza

- Tutte le query passano dal Django ORM (query parametrizzate): nessuna concatenazione di SQL grezzo, a protezione dalle **SQL injection**.
- Le password sono memorizzate solo come hash (PBKDF2) e sono soggette ai validatori di robustezza configurati in `settings.py`.
- La vista di login applica un semplice **throttling**: dopo 5 tentativi falliti con lo stesso nome utente l'accesso viene bloccato temporaneamente (contromisura agli attacchi **brute-force** e a **dizionario**).
- Protezione **CSRF** attiva su tutti i form ed escaping automatico nei template.

---

## Struttura del progetto

```text
fridge-manager/
├── manage.py
├── requirements.txt
├── dati_esempio2.json
├── db.sqlite3
├── fridgemanager/          # configurazione del progetto
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── fridge/                 # applicazione principale
    ├── models.py           # entita' e relazioni
    ├── views.py            # logica applicativa
    ├── forms.py            # form
    ├── urls.py
    ├── admin.py
    ├── context_processors.py
    ├── tests.py
    ├── management/commands/
    │   ├── popola_db.py
    │   └── controlla_scadenze.py
    └── templates/
```

---

## Utenti demo

Se caricata la fixture completa, sono disponibili utenti di esempio con password preconfigurate.

Accesso amministratore:

```text
/admin/
```

---

## Autore
Antonino Valese
Progetto sviluppato per l'esame di **Basi di Dati**, realizzato con Django e SQLite per la gestione intelligente degli alimenti domestici.
