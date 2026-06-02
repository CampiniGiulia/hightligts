# Database CHINOOK
### Un classico modello che rappresenta un negozio di musica digitale (stile iTunes)

Diamo un'occhiata alla struttura, dividendola in tre grandi macro-aree:
- Clienti e Vendite
- Catalogo Musicale
- Gestione Interna

## Schema grafico:
```
mermaid
erDiagram
    employee {
        int EmployeeId PK
        string LastName
        string FirstName
        string Title
        int ReportsTo FK
        datetime BirthDate
        datetime HireDate
        string Address
        string City
        string State
        string Country
        string PostalCode
        string Phone
        string Fax
        string Email
    }

    customer {
        int CustomerId PK
        string FirstName
        string LastName
        string Company
        string Address
        string City
        string State
        string Country
        string PostalCode
        string Phone
        string Fax
        string Email
        int SupportRepId FK
    }

    invoice {
        int InvoiceId PK
        int CustomerId FK
        datetime InvoiceDate
        string BillingAddress
        string BillingCity
        string BillingState
        string BillingCountry
        string BillingPostalCode
        numeric Total
    }

    invoiceline {
        int InvoiceLineId PK
        int InvoiceId FK
        int TrackId FK
        numeric UnitPrice
        int Quantity
    }

    track {
        int TrackId PK
        string Name
        int AlbumId FK
        int MediaTypeId FK
        int GenreId FK
        string Composer
        int Milliseconds
        int Bytes
        numeric UnitPrice
    }

    album {
        int AlbumId PK
        string Title
        int ArtistId FK
    }

    artist {
        int ArtistId PK
        string Name
    }

    genre {
        int GenreId PK
        string Name
    }

    mediatype {
        int MediaTypeId PK
        string Name
    }

    playlist {
        int PlaylistId PK
        string Name
    }

    playlisttrack {
        int PlaylistId PK
        int TrackId PK
    }

    %% Relazioni grafiche dello schema
    employee ||--o{ employee : "ReportsTo (Ricorsiva)"
    employee ||--o{ customer : "SupportRepId"
    customer ||--o{ invoice : "effettua"
    invoice ||--|{ invoiceline : "contiene"
    track ||--o{ invoiceline : "inclusa_in"
    album ||--|{ track : "contiene"
    artist ||--o{ album : "pubblica"
    genre ||--o{ track : "classifica"
    mediatype ||--o{ track : "definisce_formato"
    playlist ||--o{ playlisttrack : "ha"
    track ||--o{ playlisttrack : "presente_in"
```


## Clienti e Vendite
Questa zona gestisce chi compra e cosa viene acquistato

### Le Tabelle:
- `Customer` (Cliente): Contiene i dati personali dei clienti:
  - CustomerId: int #Chiave Primaria (PK)
  - FirstName: str
  - LastName: str
  - Company: str #Nullable
  - Address: str
  - City: str
  - State: str #Nullable
  - Country: str
  - PostalCode: str #Nullable
  - Phone: str #Nullable
  - Fax: str #Nullable
  - Email: str
  - SupportRepId: int #si collega alla tabella degli impiegati (indica quale dipendente fornisce assistenza a quel client
- `Invoice` (Fattura/Ordine): Rappresenta la "testata" di un ordine d'acquisto. Contiene:
  - InvoiceId: int #Chiave Primaria (PK)
  - CustomerId: int #Chiave Esterna (FK) (chi ha fatto l'acquisto)
  - InvoiceDate: datetime
  - BillingAddress: str
  - BillingCity: str
  - BillingState: str #Nullable
  - BillingCountry: str
  - BillingPostalCode: str #nullable
  - Total: float
- `InvoiceLine` (Riga della fattura): Poiché una fattura può contenere più canzoni, questa tabella fa da "dettaglio". Ogni riga rappresenta una specifica canzone acquistata, con il suo prezzo unitario e la quantità:
  - InvoiceLineId: int #Chiave Primaria (PK)
  - InvoiceId: int #Chiave Esterna (FK)
  - TrackId: int #Chiave Esterna (FK)
  - UnitPrice: float
  - Quantity: int
### Le relazioni:
- `Customer` 1 ─── N `Invoice`: Un cliente può effettuare molte fatture (molti acquisti), ma una fattura appartiene a un solo cliente.
- `Invoice` 1 ─── N `InvoiceLine``: Una fattura può avere molte righe di dettaglio (più canzoni comprate insieme), ma quella specifica riga appartiene a una sola fattura.
- `Track` 1 ─── N `InvoiceLine`: Una stessa canzone (Traccia) può comparire in molte righe di fatture diverse (comprata da utenti diversi).

## Area Catalogo Musicale
Questa è la struttura che organizza la musica presente nel negozio

### Le tabelle:
- `Track` (Canzone/Traccia): Il cuore del catalogo. Contiene:
  - TrackId: int #Chiave Primaria (PK)
  - Name: str
  - AlbumId: int #Chiave Esterna (FK)
  - MediaTypeId: int #Chiave Esterna (FK)
  - GenreId: int #Chiave Esterna (FK)
  - Composer: str #Nullable
  - Milliseconds: int
  - Bytes: int
  - UnitPrice: float
- `Album`: Contiene:
  - AlbumId: int #Chiave Primaria (PK)
  - Title: str
  - ArtistId: int #Chiave Esterna (FK)
- `Artist` (Artista): Contiene semplicemente il nome dell'artista o della band:
  - ArtistId: int #Chiave Primaria (PK)
  - Name: str   
- `Genre` (Genere): I generi musicali:
  - GenreId: int #Chiave Primaria (PK)
  - Name: str
- `MediaType` (Tipo di file): Il formato fisico/digitale del file:
  - MediaTypeId: int #Chiave Primaria (PK)
  - Name: str
- `Playlist`: Il nome della playlist creata (es. "I preferiti degli anni '90"):
  - PlaylistId: int #Chiave Primaria (PK)
  - Name: str
- `PlaylistTrack` (Tabella di giunzione): Questa è una tabella intermedia. Serve a creare una relazione Molti-a-Molti tra Playlist e Tracce (una playlist ha tante canzoni, e una canzone può stare in tante playlist):
  - PlaylistId: int #Chiave Primaria (PK), Chiave Esterna (FK)
  - TrackId: int #Chiave Primaria (PK), Chiave Esterna (FK)

### Le relazioni:
- `Artist` 1 ─── N `Album`: Un artista può pubblicare molti album, ma un album ha un solo artista associato
- `Album` 1 ─── N `Track`: Un album contiene molte canzoni, ma una canzone appartiene a un solo album
- `Genre` 1 ─── N `Track`: Un genere (es. Rock) può essere assegnato a molte canzoni, ma ogni canzone ha un solo genere principale
- `MediaType` 1 ─── N `Track`: Più canzoni possono condividere lo stesso formato multimediale
- `Playlist` 1 ─── N `PlaylistTrack` N ─── 1 `Track`: Come detto sopra, serve a legare tracce e playlist molti a molti

## Area Risorse Umane / Gestione Interna

### La tabella:
- `Employee` (Dipendente): Contiene i dati dello staff del negozio:
  - EmployeeId: int #Chiave Primaria (PK)
  - LastName: str  
  - FirstName: str
  - Title: str
  - ReportsTo: int #Nullable, chiave esterna ricorsiva, significa chi è il superiore
  - BirthDate: datetime
  - HireDate: datetime #data di assunzione
  - Address: str
  - City: str
  - State: str
  - Country: str
  - PostalCode: str
  - Phone: str
  - Fax: str
  - Email: str
    
### Le relazioni:
- `Employee` (Ricorsiva) 1 ─── N `Employee`: Nota la freccia che esce da employee e rientra in employee stessa tramite la colonna ReportsTo. Significa che un dipendente ha come capo un altro dipendente (il manager). È una relazione riflessiva (o gerarchica)
- `Employee` 1 ─── N `Customer`: Un dipendente (nel ruolo di supporto) può assistere molti clienti diversi.



















