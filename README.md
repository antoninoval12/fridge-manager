# Fridge Manager

Sistema informatico per la gestione intelligente degli alimenti domestici.
Progetto per l'esame di **Basi di Dati** **AUTORE:** Antonino Valese.

Stack tecnologico:

- **Backend:** Django (Python)
- **Database:** MySQL
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

- Python 3.12 (consigliato)
- MySQLlite 
- pip e venv

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
source venv/bin/activate        # su Windows: venv\Scripts\activate
```

### 3. Installare le dipendenze

```bash
pip install -r requirements.txt
```

> Nota: `mysqlclient` richiede i header di sviluppo di MySQL.
> Su Debian/Ubuntu: `sudo apt install default-libmysqlclient-dev build-essential pkg-config`.

```

### 4. Applicare le migrazioni

```bash
python manage.py makemigrations
python manage.py migrate
```

### 5. Caricare i dati di esempio

Sono disponibili due modalita'.

**Opzione consigliata** — comando completo (crea catalogo, utenti, frigoriferi
condivisi, elementi con scadenze realistiche):

```bash
python manage.py popola_db
python manage.py controlla_scadenze   # genera le notifiche di scadenza
```

**Opzione alternativa** — caricare la fixture completa (utenti, frigoriferi,
prodotti, ricette, elementi con scadenze e notifiche) gia' pronta:

```bash
python manage.py migrate          # crea le tabelle, se non l'hai gia' fatto
python manage.py loaddata dati_esempio2.json
```

La fixture contiene 132 record, inclusi gli utenti demo con password gia'
cifrata (`Password123!`). Dopo l'import puoi accedere subito con mario / giulia /
luca / admin. Nota: le date di scadenza sono relative al giorno in cui la
fixture e' stata generata; se vuoi rigenerarle aggiornate a oggi usa invece
`python manage.py popola_db --reset`, che ricalcola tutto dinamicamente.

Utenti di esempio creati da `popola_db` (password: `Password123!`):

| Username | Ruolo                                         |
|----------|-----------------------------------------------|
| admin    | superuser (accesso a /admin/)                 |
| mario    | proprietario "Frigo di casa"                  |
| giulia   | membro di casa + proprietaria "Frigo ufficio" |
| luca     | proprietario "Casa al mare"                   |

Il comando popola circa 30 prodotti distribuiti in 3 frigoriferi, con scadenze
realistiche (alcuni gia' scaduti, alcuni in scadenza, molti validi) e genera in
automatico le notifiche. Per svuotare e ricaricare i dati: `python manage.py popola_db --reset`.

### 6. Avviare il server

```bash
python manage.py runserver
```

Applicazione: http://127.0.0.1:8000/
Pannello di amministrazione: http://127.0.0.1:8000/admin/

---

## Esecuzione dei test

```bash
python manage.py test
```

## Note sulla sicurezza

- Tutte le query passano dal Django ORM (query parametrizzate): nessuna concatenazione di SQL grezzo, a protezione dalle **SQL injection**.
- Le password sono memorizzate solo come hash (PBKDF2) e sono soggette ai validatori di robustezza configurati in `settings.py`.
- La vista di login applica un semplice **throttling**: dopo 5 tentativi falliti l'accesso viene bloccato temporaneamente (contromisura agli attacchi **brute-force** e a **dizionario**).
- Protezione **CSRF** attiva su tutti i form ed escaping automatico nei template.

---

## Struttura del progetto

```
fridge-manager/
├── manage.py
├── requirements.txt
├── dati_esempio.json
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
