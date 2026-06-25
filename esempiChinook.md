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
