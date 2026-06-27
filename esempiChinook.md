# Esempi di query CHINOOK
- Tipologia: Grafo Non Orientato, Pesato
  1. Un utente seleziona un Genere Musicale (es. "Rock") da un menu a tendina.
    - I Vertici (Nodi): Tutti gli artisti (Artist) che hanno pubblicato almeno un brano (Track) del genere selezionato:
    ```
    SELECT distinct a.* 
    FROM Artist a, Album a2, Track t   
    WHERE a.ArtistId = a2.ArtistId and a2.AlbumId = t.albumID and t.GenreId = 1
    ```
    - Gli Archi: Esiste un arco non orientato tra l'artista A e l'artista B se hanno almeno un brano (del genere selezionato) inserito nella stessa Playlist (Playlist).Il Peso dell'arco: Il numero di playlist distinte in cui compaiono brani di entrambi gli artisti (sempre limitatamente a quel genere):
    ```
    SELECT t1.ArtistId, t2.ArtistId, count(distinct t1.PlaylistId ) as peso
    FROM (SELECT distinct a.ArtistId, pt.PlaylistId 
    FROM Artist a, Album a2, Track t, PlaylistTrack pt    
    WHERE a.ArtistId = a2.ArtistId and a2.AlbumId = t.albumID and t.GenreId = 1
    and pt.TrackId = t.TrackId) t1,
    (SELECT distinct a.ArtistId, pt.PlaylistId 
    FROM Artist a, Album a2, Track t, PlaylistTrack pt    
    WHERE a.ArtistId = a2.ArtistId and a2.AlbumId = t.albumID and t.GenreId = 1
    and pt.TrackId = t.TrackId) t2
    WHERE t1.ArtistId < t2.ArtistId and t1.PlaylistId = t2.PlaylistId
    group by t1.ArtistId, t2.ArtistId
    ```
- Tipologia: Grafo Orientato, Non Pesato
    1. Vogliamo mappare la struttura organizzativa interna dell'azienda.
    - I Vertici (Nodi): Tutti i dipendenti (Employee) presenti nel database:
        ```
        SELECT distinct e.*
        FROM Employee e 
        ```

    - Gli Archi: Esiste un arco orientato che va dal dipendente A al dipendente B (A $\rightarrow$ B) se il dipendente A è il capo diretto (superiore) del dipendente B.Il Peso dell'arco: Non pesato (semplice connessione gerarchica):
        ```
        SELECT distinct e2.EmployeeId, e.EmployeeId 
        FROM Employee e, Employee e2 
        WHERE e.ReportsTo = e2.EmployeeId 
        ```

- Tipologia: Grafo Orientato, Pesato
    1. Vogliamo capire quali generi musicali portano all'acquisto di altri generi.
    - I Vertici (Nodi): Tutti i generi musicali (Genre) che hanno registrato almeno una vendita.
    ```
    SELECT distinct g.*
    FROM Genre g, Track t, InvoiceLine il 
    WHERE g.GenreId = t.GenreId  and t.TrackId = il.TrackId
    ```      
    - Gli Archi: Esiste un arco orientato dal genere X al genere Y (X $\rightarrow$ Y) se lo stesso cliente ha acquistato prima un brano di genere X e, in una fattura successiva (con data maggiore), ha acquistato un brano di genere Y.Il Peso dell'arco: Il numero di volte in cui si è verificata questa sequenza temporale (X poi Y) tra tutti i clienti.
    ```
    SELECT t1.GenreId, t2.GenreId, count(*) as peso
    FROM (SELECT g.GenreId, i.CustomerId, i.InvoiceDate 
    FROM Genre g, Track t, InvoiceLine il, Invoice i  
    WHERE g.GenreId = t.GenreId  and t.TrackId = il.TrackId and i.InvoiceId = il.InvoiceId) t1,
    (SELECT g.GenreId, i.CustomerId, i.InvoiceDate 
    FROM Genre g, Track t, InvoiceLine il, Invoice i  
    WHERE g.GenreId = t.GenreId  and t.TrackId = il.TrackId and i.InvoiceId = il.InvoiceId ) t2
    WHERE t1.CustomerId = t2.CustomerId and t1.GenreId <> t2.GenreId and t1.InvoiceDate < t2.InvoiceDate 
    GROUP BY t1.GenreId, t2.GenreId
    ```
- Tipologia: Grafo Non Orientato, Pesato
    1. Un utente seleziona una Nazione (es. "USA" o "Brazil") da un menu a tendina.
    - I Vertici (Nodi): Tutti i clienti (Customer) residenti in quella specifica nazione:
    ```
    SELECT distinct c.*
    FROM Customer c 
    WHERE c.Country = "Brazil"
    ```

    - Gli Archi: Esiste un arco non orientato tra il cliente A e il cliente B se hanno acquistato almeno un brano dello stesso artista (Artist). Il Peso dell'arco: Il numero totale di brani in comune acquistati dai due clienti (la somma delle canzoni dell'artista/i condiviso/i comprate da entrambi: (caso 1: non conto due volte le tracce uguali comprate da entrambi)

    ```
    SELECT t3.id1, t3.id2, (t3.numCanzTot - COALESCE(t4.numCanzComune, 0)) as peso 
    FROM (SELECT t1.CustomerId as id1, t2.CustomerId as id2, (count( distinct t1.TrackId ) + count(distinct t2.TrackId )) as numCanzTot
    fROM (SELECT c.CustomerId, a.ArtistId, t.TrackId 
    FROM Customer c, Invoice i, InvoiceLine il, Track t, Album a 
    WHERE c.CustomerId = i.CustomerId and i.InvoiceId = il.InvoiceId and il.TrackId = t.TrackId and t.AlbumId = a.AlbumId 
    and c.Country = "Brazil") t1,
    (SELECT c.CustomerId, a.ArtistId, t.TrackId 
    FROM Customer c, Invoice i, InvoiceLine il, Track t, Album a 
    WHERE c.CustomerId = i.CustomerId and i.InvoiceId = il.InvoiceId and il.TrackId = t.TrackId and t.AlbumId = a.AlbumId 
    and c.Country = "Brazil") t2
    WHERE t1.ArtistId = t2.ArtistId and t1.CustomerId < t2.CustomerId 
    GROUP BY t1.CustomerId, t2.CustomerId) t3
    LEFT JOIN (SELECT t1.CustomerId as id1, t2.CustomerId as id2, (count(distinct t1.TrackId )) as numCanzComune
    fROM (SELECT c.CustomerId, a.ArtistId, t.TrackId 
    FROM Customer c, Invoice i, InvoiceLine il, Track t, Album a 
    WHERE c.CustomerId = i.CustomerId and i.InvoiceId = il.InvoiceId and il.TrackId = t.TrackId and t.AlbumId = a.AlbumId 
    and c.Country = "Brazil") t1,
    (SELECT c.CustomerId, a.ArtistId, t.TrackId 
    FROM Customer c, Invoice i, InvoiceLine il, Track t, Album a 
    WHERE c.CustomerId = i.CustomerId and i.InvoiceId = il.InvoiceId and il.TrackId = t.TrackId and t.AlbumId = a.AlbumId 
    and c.Country = "Brazil") t2
    WHERE t1.ArtistId = t2.ArtistId and t1.CustomerId < t2.CustomerId and t1.TrackId = t2.TrackId 
    GROUP BY t1.CustomerId, t2.CustomerId) t4 on t3.id1 =t4.id1 and t3.id2 = t4.id2 
    ```
    (caso 2: conto due volte le tracce uguali comprate da entrambi)
    ```
    SELECT t1.CustomerId, t2.CustomerId, (count(distinct t1.TrackId ) + count( distinct t2.TrackId )) as numCanzTot
    fROM (SELECT c.CustomerId, a.ArtistId, t.TrackId 
    FROM Customer c, Invoice i, InvoiceLine il, Track t, Album a 
    WHERE c.CustomerId = i.CustomerId and i.InvoiceId = il.InvoiceId and il.TrackId = t.TrackId and t.AlbumId = a.AlbumId 
    and c.Country = "Brazil") t1,
    (SELECT c.CustomerId, a.ArtistId, t.TrackId 
    FROM Customer c, Invoice i, InvoiceLine il, Track t, Album a 
    WHERE c.CustomerId = i.CustomerId and i.InvoiceId = il.InvoiceId and il.TrackId = t.TrackId and t.AlbumId = a.AlbumId 
    and c.Country = "Brazil") t2
    WHERE t1.ArtistId = t2.ArtistId and t1.CustomerId < t2.CustomerId 
    GROUP BY t1.CustomerId, t2.CustomerId
    ```



1. Creare un grafo in cui i nodi sono gli Artisti (Artist) e un arco collega due artisti se compaiono entrambi all'interno di almeno una stessa Playlist. Il peso dell'arco è dato dal numero di playlist distinte che hanno in comune:
```
SELECT t1.ArtistId, t2.ArtistId, count(distinct t1.PlaylistId ) as peso
FROM (SELECT distinct a.ArtistId, pt.PlaylistId  
FROM Album a, Track t, PlaylistTrack pt   
where a.AlbumId = t.AlbumId and pt.TrackId = t.TrackId ) t1, 
(SELECT distinct a.ArtistId, pt.PlaylistId  
FROM Album a, Track t, PlaylistTrack pt   
where a.AlbumId = t.AlbumId and pt.TrackId = t.TrackId ) t2
WHERE t1.PlaylistId  = t2.PlaylistId  and t1.ArtistId < t2.ArtistId 
group by t1.ArtistId, t2.ArtistId
```
2. I nodi sono i Clienti (Customer). Esiste un arco orientato tra il Customer 1 e il Customer 2 se il Cliente 1 ha acquistato almeno una traccia del genere musicale preferito (quello più acquistato in assoluto) dal Cliente :
```
SELECT DISTINCT c1.customerId, c2.customerId
FROM (select DISTINCT i.CustomerId, t.GenreId  
from Invoice i, InvoiceLine il, Track t 
where i.InvoiceId = il.InvoiceId and t.TrackId = il.TrackId) c1,
(SELECT A.CustomerId, A.GenreId
FROM 
    -- Tabella A: Tutti i conteggi per genere
    (SELECT c.CustomerId, t.GenreId, COUNT(*) AS NumeroAcquisti
     FROM Customer c, Invoice i, InvoiceLine il, Track t
     WHERE c.CustomerId = i.CustomerId AND i.InvoiceId = il.InvoiceId AND il.TrackId = t.TrackId
     GROUP BY c.CustomerId, t.GenreId) A,
    
    -- Tabella B: Solo il massimo per ogni cliente
    (SELECT CustomerId, MAX(NumeroAcquisti) AS MaxAcquisti
     FROM (SELECT c.CustomerId, t.GenreId, COUNT(*) AS NumeroAcquisti
           FROM Customer c, Invoice i, InvoiceLine il, Track t
           WHERE c.CustomerId = i.CustomerId AND i.InvoiceId = il.InvoiceId AND il.TrackId = t.TrackId
           GROUP BY c.CustomerId, t.GenreId) c
     GROUP BY CustomerId) B
WHERE A.CustomerId = B.CustomerId 
  AND A.NumeroAcquisti = B.MaxAcquisti) c2
WHERE c1.customerId <> c2.customerId and c1.GenreId = c2.GenreID
```
3. Vogliamo un grafo dove i nodi sono i Compositori (Track.Composer) di un determinato Genere (es. 'Rock'). Un arco non orientato collega due compositori se hanno tracce che appartengono allo stesso Album:
```
SELECT distinct t1.Composer, t2.Composer 
FROM (select distinct t.Composer, t.AlbumId 
from Track t
where t.GenreId = 1 and t.Composer is not null) t1, 
(select distinct t.Composer, t.AlbumId 
from Track t
where t.GenreId = 1 and t.Composer is not null) t2
where t1.AlbumId = t2.AlbumId and t1.Composer < t2.Composer 
```
4. I nodi sono gli Impiegati (Employee). C'è un arco orientato che va dall'impiegato A all'impiegato B se l'impiegato A fa da supervisore all'impiegato B:
```
select distinct e2.EmployeeId as capo, e.EmployeeId as imp
from Employee e, Employee e2 
where e.ReportsTo = e2.EmployeeId
```
5. L'utente seleziona un Anno tramite un menu a tendina. L'applicazione costruisce un grafo orientato e pesato in cui i nodi sono le Tracce (Track) che sono state acquistate almeno una volta nell'anno selezionato.
Esiste un arco tra la Traccia A e la Traccia B se almeno un cliente le ha acquistate entrambe (anche in momenti diversi).
Il verso va da A a B se la traccia A è stata acquistata prima (data della fattura InvoiceDate) rispetto alla traccia B (considerando il primo acquisto in assoluto di ciascuna traccia da parte di quel cliente). Se sono state acquistate nello stesso identico momento, si inseriscano entrambi gli archi.
Il peso dell'arco è il numero totale di clienti distinti che hanno acquistato entrambe le tracce:(in python controllo che esistano i nodi)
```
SELECT t1.TrackId, t2.TrackId, count(distinct t1.CustomerId ) as peso
FROM (SELECT t.TrackId, i.CustomerId , min(i.InvoiceDate) as datainiz
FROM  Track t, InvoiceLine il, Invoice i 
WHERE t.TrackId = il.TrackId and il.InvoiceId = i.InvoiceId 
group by t.TrackId, i.CustomerId) t1, 
(SELECT t.TrackId, i.CustomerId, min(i.InvoiceDate) as datainiz 
FROM  Track t, InvoiceLine il, Invoice i 
WHERE t.TrackId = il.TrackId and il.InvoiceId = i.InvoiceId 
group by t.TrackId, i.CustomerId) t2
where t1.TrackId <> t2.TrackId and t1.CustomerId = t2.CustomerId and t1.datainiz <= t2.datainiz 
group by t1.TrackId, t2.TrackId
order by peso desc
```
6. L'utente seleziona un valore minimo di canzoni $N$ tramite un campo di testo. Il grafo è non orientato e pesato, e i nodi sono gli Artisti (Artist) che hanno pubblicato almeno un album contenente più di $N$ tracce.
Esiste un arco tra l'Artista A e l'Artista B se esiste almeno una Playlist che contiene sia una canzone di A sia una canzone di B.
Il peso dell'arco si calcola in base alla "coesione" nel codice: è la somma del numero di tracce di A e del numero di tracce di B presenti in quella playlist:
nodi:
```
SELECT distinct a2.ArtistId, a2.Name 
FROM Artist a2, Album a, Track t 
WHERE a.ArtistId = a2.ArtistId and t.AlbumId =a.AlbumId 
group by a2.ArtistId, a2.Name, a.AlbumId 
having count(*) >4
```
archi: (controllo che ci siano i nodi in python)
```
SELECT t1.ArtistId, t2.ArtistId, sum(t1.numT +t2.numT )
FROM (SELECT a.ArtistId, pt.PlaylistId, count(pt.TrackId ) as numT
from album a, Track t, PlaylistTrack pt 
where a.AlbumId = t.albumID and t.TrackId = pt.TrackId 
group by a.ArtistId, pt.PlaylistId) t1,
(SELECT a.ArtistId, pt.PlaylistId, count(pt.TrackId ) as numT
from album a, Track t, PlaylistTrack pt 
where a.AlbumId = t.albumID and t.TrackId = pt.TrackId 
group by a.ArtistId, pt.PlaylistId) t2
where t1.ArtistId < t2.ArtistId and t1.PlaylistId = t2.PlaylistId 
group by t1.ArtistId, t2.ArtistId
```
# Esempi archi in python:
1. artisti e playslist:
  - I Vertici (Nodi): Tutti gli Artisti (Artist) che hanno almeno un brano inserito in una qualsiasi Playlist:
  ```
  SELECT  distinct a.*
  FROM Artist a, Album a2, Track t, PlaylistTrack pt   
  WHERE a.ArtistId = a2.ArtistId and a2.AlbumId = t.AlbumId  and t.TrackId = pt.TrackId 
  ```
  - Gli Archi: Esiste un arco non orientato tra l'Artista A e l'Artista B se compaiono all'interno della stessa identica Playlist.  Il Peso dell'arco: Il numero di playlist distinte in cui i due artisti co-esistono. (La query è poco efficiente quindi uso python):
  Nel db:
  ```
  SELECT  a.ArtistId, pt.PlaylistId 
  FROM Artist a, Album a2, Track t, PlaylistTrack pt   
  WHERE a.ArtistId = a2.ArtistId and a2.AlbumId = t.AlbumId  and t.TrackId = pt.TrackId 
  
  ```
  In python:
  ```
  # In model.py
from collections import defaultdict
import itertools
from database.DAO import DAO

class Model:
    def __init__(self):
        self._graph = nx.Graph() # Il nostro grafo non orientato

    def build_graph(self):
        # 1. Recuperiamo la lista di tuple dal DAO
        lista_connessioni = DAO.get_playlist_artists()
        
        # 2. RAGGRUPPAMENTO: Creiamo una mappa dove la chiave è la playlist 
        # e il valore è un INSIEME (set) di artisti presenti in quella playlist.
        # Usiamo il set() perché se un artista ha più canzoni nella stessa playlist,
        # inserendolo nel set salvaguardiamo l'unicità (add fa l'effetto del DISTINCT).
        playlist_map = defaultdict(set)
        for playlist_id, artist_id in lista_connessioni:
            playlist_map[playlist_id].add(artist_id)
            
        # 3. CALCOLO COMBINATORIO DEL PESO:
        # Creiamo un dizionario di frequenza per contare quante playlist condividono le coppie.
        # Chiave: (id_artista1, id_artista2) -> Valore: quante playlist hanno in comune
        coppie_peso = defaultdict(int)
        
        # Iteriamo su ogni singola playlist memorizzata nella nostra mappa
        for playlist_id, artisti_in_playlist in playlist_map.items():
            
            # Se nella playlist c'è solo 1 artista (o nessuno), non può esserci un arco
            if len(artisti_in_playlist) > 1:
                
                # ORDINA GLI ID: Trasformiamo il set in una lista e la ordiniamo numericamente.
                # Questo è CRUCIALE per i grafi non orientati: l'artista 4 e l'artista 10 
                # compariranno sempre come coppia ordinata (4, 10) e MAI come (10, 4).
                # Questo simula perfettamente il filtro SQL "t1.ArtistId < t2.ArtistId".
                artisti_ordinati = sorted(list(artisti_in_playlist))
                
                # GENERAZIONE COPPIE: itertools.combinations prende la lista ordinata e genera
                # tutte le combinazioni uniche possibili a coppie di 2 senza ripetizioni.
                # Es: se la lista è [2, 5, 8], genererà: (2,5), (2,8), (5,8)
                for a1, a2 in itertools.combinations(artisti_ordinati, 2):
                    # Incrementiamo il contatore di quante volte questa specifica coppia si incontra
                    coppie_peso[(a1, a2)] += 1
                    
        # 4. AGGIUNTA DEGLI ARCHI NEL GRAFO:
        # Ora che abbiamo scansionato tutte le playlist, inseriamo gli archi definitivi
        for (a1, a2), peso in coppie_peso.items():
            # Aggiungiamo l'arco con il peso accumulato
            self._graph.add_edge(a1, a2, weight=peso)
            
        print(#_nodes()}")
        print(#_edges()}")
  ```





