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
    - SELECT SUM(CAST(REPLACE(worlwide_gross_income, '$ ', '') AS UNSIGNED))
    - SUM(CAST(REPLACE(REPLACE(worlwide_gross_income, '$ ', ''), 'INR ', '') AS UNSIGNED))
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
   
## Funzioni UTILI:
- REPLACE() — Il "Trova e Sostituisci":
  - ``` REPLACE(testo_originale, testo_da_cercare, testo_sostitutivo) ```
  - ``` REPLACE('$ 150', '$ ', '')  -- Risultato: '150' ```
  - ```REPLACE(REPLACE('Prezzo: $ 50', '$ ', ''), 'Prezzo: ', '') -- Risultato: '50' ```
- CAST() — Il "Trasformatore di Tipi":
  - ``` CAST(valore AS nuovo_tipo_di_dato) ```
  1. Da Testo a Numero Intero:
  - ``` CAST('123' AS UNSIGNED) -- In MySQL diventa il numero 123 (positivo) ```
  - ``` CAST('123' AS INT)      -- In Postgres/SQL Server diventa il numero 123 ```
  2. Da Testo a Numero con Decimali (Frazionario):
  - ``` CAST('99.90' AS DECIMAL(10,2)) -- Risultato: il numero decimale 99.90 ```
- Insieme:
  - ``` SUM(CAST(REPLACE(worlwide_gross_income, '$ ', '') AS UNSIGNED)) ```
  - ``` SUM(CAST(REPLACE(REPLACE(worlwide_gross_income, '$ ', ''), 'INR ', '') AS UNSIGNED)) ```


## QUERY
ESEMPI:
- Esiste un arco tra due attori se hanno recitato almeno in uno stesso film. Il peso è pari alla somma degli incassi dei film in comune.
#### Soluzione mia:
```
SELECT t1.aid, t2.aid, sum(t1.inc) as peso
FROM (SELECT DISTINCT n.id as aid, m.id as fid, CAST(REPLACE(REPLACE(worlwide_gross_income, '$ ', ''), 'INR ', '') AS UNSIGNED) as inc
FROM ratings r, movie m, role_mapping rm, names n 
WHERE r.movie_id = m.id and rm.movie_id = m.id and rm.name_id = n.id and m.worlwide_gross_income is not null
and r.avg_rating between 1.2 and 2.7 and n.date_of_birth is not null) t1,
(SELECT DISTINCT n.id as aid, m.id as fid, CAST(REPLACE(REPLACE(worlwide_gross_income, '$ ', ''), 'INR ', '') AS UNSIGNED) as inc
FROM ratings r, movie m, role_mapping rm, names n 
WHERE r.movie_id = m.id and rm.movie_id = m.id and rm.name_id = n.id and m.worlwide_gross_income is not null
and r.avg_rating between 1.2 and 2.7 and n.date_of_birth is not null) t2
where t1.aid < t2.aid and t1.fid=t2.fid 
group by t1.aid, t2.aid
order by peso desc
```

#### Soluzione prof:
```
select rm1.name_id as Actor1, rm2.name_id as Actor2, sum( cast(replace(replace(m.worlwide_gross_income, '$', ''),',', '') as unsigned)) as Weight
from movie m, role_mapping rm1, role_mapping rm2, ratings r, names n1, names n2
where m.id = rm1.movie_id
and m.id = rm2.movie_id
and m.id = r.movie_id
and rm1.name_id = n1.id
and rm2.name_id = n2.id
and n1.date_of_birth IS NOT NULL
and n2.date_of_birth IS NOT NULL
and rm1.name_id < rm2.name_id
and r.avg_rating >= %s
and r.avg_rating <= %s
and m.worlwide_gross_income is not null
and m.worlwide_gross_income like '$%'
group by rm1.name_id, rm2.name_id
```
-  I vertici sono gli attori che hanno recitato nei film che hanno ricevuto una valutazione nel range definito dall’utente (data di nascita valida.
#### Soluzione mia:
```
SELECT DISTINCT n.id, n.name, n.date_of_birth 
FROM ratings r, movie m, role_mapping rm, names n 
WHERE r.movie_id = m.id and rm.movie_id = m.id and rm.name_id = n.id 
and r.avg_rating between %s and %s and n.date_of_birth is not null
```
#### soluzione prof:
```
select distinct rm.name_id as ActorID, n.name as Name, n.date_of_birth as birth_date
from movie m, role_mapping rm, ratings r, names n 
where n.id = rm.name_id 
and n.date_of_birth IS NOT NULL
and m.id = rm.movie_id 
and m.id = r.movie_id 
and r.avg_rating >= %s
and r.avg_rating <= %s
```



    




