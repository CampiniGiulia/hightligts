# METODO DAO da copiare
- Generi
```
@staticmethod
    def getAllGeneri():
        conn = DBConnect.get_connection()

        results = []

        cursor = conn.cursor(dictionary=True)
        query = """SELECT distinct g.GenreId, g.Name
                    FROM Genre g"""

        cursor.execute(query)

        for row in cursor:
            results.append(Genere(**row))

        cursor.close()
        conn.close()
        return results
```
- Artisti
```
@staticmethod
    def getAllNodes(genere):
        conn = DBConnect.get_connection()

        results = []

        cursor = conn.cursor(dictionary=True)
        query = """SELECT distinct a.*
                    FROM Artist a, Album a2, Track t 
                    WHERE a.ArtistId = a2.ArtistId and a2.AlbumId = t.AlbumId 
                    and t.GenreId = %s"""

        cursor.execute(query, (genere,))

        for row in cursor:
            results.append(Artista(**row))

        cursor.close()
        conn.close()
        return results
```
- Archi Artisti
```
@staticmethod
    def getAllEdges(genere):
        conn = DBConnect.get_connection()

        results = []

        cursor = conn.cursor(dictionary=True)
        query = """SELECT distinct t1.ArtistId as id1, t2.ArtistId as id2, (t3.popolarit + t4.popolarit ) as peso
                    FROM (SELECT distinct a.ArtistId, i.CustomerId
                    FROM Album a, Track t, InvoiceLine il, Invoice i 
                    WHERE a.AlbumId = t.AlbumId and il.TrackId = t.TrackId and il.InvoiceId = i.InvoiceId 
                    and t.GenreId = %s) t1, 
                    (SELECT distinct a.ArtistId, i.CustomerId
                    FROM Album a, Track t, InvoiceLine il, Invoice i 
                    WHERE a.AlbumId = t.AlbumId and il.TrackId = t.TrackId and il.InvoiceId = i.InvoiceId 
                    and t.GenreId = %s) t2,
                    (SELECT a.ArtistId, count(* ) as popolarit 
                    FROM Album a, Track t, InvoiceLine il, Invoice i 
                    WHERE a.AlbumId = t.AlbumId and il.TrackId = t.TrackId and il.InvoiceId = i.InvoiceId 
                    and t.GenreId = %s
                    group by a.ArtistId) t3,
                    (SELECT a.ArtistId, count(*) as popolarit 
                    FROM Album a, Track t, InvoiceLine il, Invoice i 
                    WHERE a.AlbumId = t.AlbumId and il.TrackId = t.TrackId and il.InvoiceId = i.InvoiceId 
                    and t.GenreId = %s
                    group by a.ArtistId) t4
                    WHERE t1.ArtistId <> t2.ArtistId  and t1.CustomerId = t2.CustomerId and t3.ArtistId = t1.ArtistId and t4.ArtistId = t2.ArtistId 
                    and t3.popolarit >= t4.popolarit 
                    order by peso desc"""

        cursor.execute(query, (genere, genere, genere, genere))

        for row in cursor:
            results.append((row["id1"], row["id2"], row["peso"]))

        cursor.close()
        conn.close()
        return results
```
# Metodi DTO da copiare
- Genere
```
from dataclasses import dataclass


@dataclass
class Genere:
    GenreId: int
    Name: str

    def __hash__(self):
        return hash(self.GenreId)

    def __eq__(self, other):
        return self.GenreId == other.GenreId
```
- Artista
```
from dataclasses import dataclass


@dataclass
class Artista:
    ArtistId: int
    Name: str

    def __hash__(self):
        return hash(self.ArtistId)

    def __eq__(self, other):
        return self.ArtistId == other.ArtistId
```

