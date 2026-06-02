# Database IMDB
### Un database dedicato al mondo del cinema e dei film
Diamo un'occhiata alla struttura, dividendola in tre grandi macro-aree:
- L'Area Film
- L'Anagrafica delle Persone
- Le Tabelle di Giunzione (Relazioni Molti-a-Molti)

## L'Area Film
### Le tabelle:
- `movie`(Film): È la tabella centrale di tutto il database. Ogni riga rappresenta un film unico. Contiene informazioni fondamentali:
  - id: str #Chiave Primaria (PK)
  - title: str
  - year: int #anno di uscita
  - date_published: datetime.date #data di pubblicazione
  - duration: int #durata in minuti
  - country: str #Nullable, paese di produzione
  - worlwide_gross_income: #Nullable, str --> float, incassi mondiali
  - languages: str #Nullable
  - production_company: str #Nullable
  #### Metodi particolari per gestire numeri con le stringhe:
    - SELECT SUM(CAST(REPLACE(worlwide_gross_income, '$ ', '') AS UNSIGNED)) +++
    - Where worlwide_gross_income like '$%'
- `genre` (Genere): Questa tabella associa i film ai loro generi cinematografici (es. Action, Drama, Comedy):
  - movie_id: str #Chiave Primaria (PK), chiave esterna (FK)
  - genre: str #Chiave Primaria (PK)
- `ratings` (Valutazioni/Recensioni): Contiene i dati relativi al gradimento del pubblico e della critica per ogni film:
  - movie_id: str #Chiave Primaria (PK), chiave esterna (FK)
  - avg_rating: float
  - total_votes: int
  - median_rating: int
### Le relazioni:
- `movie` 1 ─── N `genre`: Un film può appartenere a più generi contemporaneamente (es. un film può essere sia "Azione" che "Fantascienza"), ma ogni riga di questa tabella associa un singolo genere a un singolo film.
- `movie` 1 ─── 1 `ratings`: Graficamente è indicata con una linea diretta. Tipicamente, ogni film ha una sola riga corrispondente nella tabella dei ratings che riassume tutti i voti ricevuti. Separare i voti dalla tabella movie principale è una scelta di ottimizzazione (evita di appesantire la tabella dei film con dati che cambiano continuamente).

## L'Anagrafica delle Persone
### La tabella:
- `names` (Persone/Artisti): Questa tabella è l'anagrafe di chiunque lavori nel cinema (attori, registi, sceneggiatori, ecc.). Contiene:
  - id: str #Chiave Primaria (PK)
  - name: str
  - height: int #Nullable
  - date_of_birth: datetime.date #Nullable
  - known_for_movies: str #Nullable

## Le Tabelle di Giunzione (Relazioni Molti-a-Molti)
Nel cinema, le relazioni tra le persone (`names`) e i film (`movie`) sono intrinsecamente Molti-a-Molti: un film ha molti attori e registi, e un attore/regista lavora a molti film. Per evitare il caos nei database, queste relazioni si risolvono sempre con delle tabelle "ponte" (o tabelle di giunzione). 

### Nello schema ce ne sono due:
1. `director_mapping`(Mappatura dei Registi): Questa tabella serve unicamente a stabilire chi ha diretto cosa. Contiene solo coppie di ID:
    - movie_id: str #Chiave Primaria (PK) ed esterna, punta al film
    - name_id: str #Chiave Primaria (PK) ed esterna, punta alla persona
  - Relazioni: Unendo movie e names attraverso questo ponte, puoi scoprire tutti i registi di un film o tutti i film diretti da un regista.
    - `movie` 1 ─── N `director_mapping`
    - `names` 1 ─── N `director_mapping`
      
2. `role_mapping` (Mappatura dei Ruoli/Attori): Questa tabella mappa le persone che hanno recitato o partecipato al film, con un dettaglio in più: la colonna category (che specifica se la persona era un attore, un'attrice, una controfigura, ecc.):
    - movie_id: str #Chiave Primaria (PK) ed esterna, punta al film
    - name_id: str #Chiave Primaria (PK) ed esterna, punta alla persona
    - category: str #Specifica il tipo di ruolo
  - Relazioni: Funziona esattamente come la tabella dei registi, ma permette di filtrare per categoria
    - `movie` 1 ─── N `role_mapping`
    - `names` 1 ─── N `role_mapping`   
  
    




