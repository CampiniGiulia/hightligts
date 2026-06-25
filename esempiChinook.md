# Esempi di query CHINOOK
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
``


