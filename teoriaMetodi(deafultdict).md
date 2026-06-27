# Strumenti/metodi utili
## defaultdict
- si importa così: from collections import defaultdict
- In Python, se provi ad accedere a una chiave che non esiste ancora in un dizionario normale ({}, dict), il programma va in crash lanciando un errore.
- Il defaultdict risolve questo problema: quando cerchi una chiave che non c'è, la crea automaticamente inizializzandola con un "valore di default" che decidi tu:
  - defaultdict(set): Il valore di default è un insieme vuoto (set()), con il set si eliminano i duplicati (come distinct).Se scrivi playlist_map[12].add("ArtistaA") e la playlist 12 non è ancora mai stata registrata, Python: Crea la chiave 12. Le assegna un set() vuoto. Ci aggiunge "ArtistaA"
  - defaultdict(int): Il valore di default è lo zero (0). Se scrivi coppie_peso[("A", "B")] += 1 e quella coppia di artisti non si è mai incontrata prima, Python: Crea la chiave ("A", "B"). Inizializza il valore a 0. Somma 1 (il valore diventa 1).
## dict.fromkeys(iterabile, valore_iniziale)
- Questo metodo è fantastico se usi valori immutabili come lo zero (0), stringhe o None. Non usarlo mai se vuoi inizializzare il dizionario con delle liste vuote, tipo dict.fromkeys(range(1, 5), []) --> usa la Dict Comprehension:
```
# Questo è sicuro al 100% perché crea una lista nuova a ogni ciclo
mio_dizionario = {mese: [] for mese in range(1, 13)}
```
## Iterare sui dizionari:
- playlist_map.items(): Un dizionario memorizza i dati in coppie formate da una chiave e un valore, il metodo .items() serve a estrarre contemporaneamente sia la chiave che il valore per poterci fare un ciclo for
- popolarita_artisti.keys(): cicla solo sulle chiavi del dizionario
- popolarita_artisti.values(): cicla solo sui valori del dizionario
## itertools.combinations(lista, 2)
- si importa così: import itertools
- Questa è una funzione magica per i grafi non orientati. Prende una lista di elementi e genera tutte le combinazioni uniche possibili di coppie (gruppi di 2 elementi), senza ripetizioni e senza calcolare elementi accoppiati con se stessi.
- Non genera coppie con lo stesso elemento (es. ("U2", "U2")), evitando i self-loop sui nodi.
- Ricordati di fare sempre un sorted() della lista prima di passarla a combinations: artisti_ordinati = sorted(list(artisti_in_playlist))
- Somma i valori del dizionario: sum(popolarita_artisti.values())
- massimo tra i valori del dizionario: max(popolarita_artisti.values())
- per iterare cambiando i valori devo usare list() nel for
## itertools.permutations(lista, 2)
- le permutazioni tengono conto dell'ordine degli elementi. Generano tutte le coppie possibili estraendo gli elementi in ogni ordine immaginabile, ma senza accoppiare un elemento con se stesso.
- In questo caso non devi fare il sorted() della lista prima, altrimenti annulleresti l'effetto della permutazione!

## itertools minori
- itertools.product(lista, repeat=2): per grafi orientati con selfloop
- itertools.combinations_with_replacement(lista, 2): per grafi non orientati con selfloop

## Insiemi (set)
- si inizializza con set() o {valore1, valore2}
- si aggiunge con add()
- si rimuove con remove()
- Intersezione: & oppure .intersection()
- Unione: | oppure .union()
- Differenza: - oppure .difference()
- Sottoinsieme: .issubset()
- Sovrainsieme: .issuperset()
- Insiemi Disgiunti: .isdisjoint()
  
## Ordinamenti
- sorted()-> genera una NUOVA lista: Prende una qualsiasi struttura iterabile (lista, tupla, set, dizionario), ne copia gli elementi e restituisce una nuova lista perfettamente ordinata. La struttura originale non viene toccat:
```
# Ordinare una tupla (restituisce una lista)
tupla_disordinata = (5, 1, 8, 3)
lista_ordinata = sorted(tupla_disordinata)

print(lista_ordinata)      # Output: [1, 3, 5, 8]
print(tupla_disordinata)  # Output: (5, 1, 8, 3) <-- Salva e immutata!
```
```
numeri = [10, 50, 20]
numeri_decrescenti = sorted(numeri, reverse=True) # [50, 20, 10]
```
```
popolarita_artisti = {1: 4093, 2: 1500, 3: 5000}

# x è la tupla (artista_id, punteggio). Ordiniamo per punteggio (x[1]) decrescente
artisti_top = sorted(popolarita.items(), key=lambda x: x[1], reverse=True)

print(artisti_top) # Output: [(3, 5000), (1, 4093), (2, 1500)] [cite: 99]
```


- .sort() -> Ordina SUL POSTO: È un metodo esclusivo delle liste. Modifica direttamente la lista originale e restituisce None.
```
voti = [18, 30, 24]
voti.sort() # Modifica 'voti' direttamente in memoria

print(voti) # Output: [18, 24, 30]

# ERRORE DA EVITARE:
# x = voti.sort() --> x conterrà None!
```
## List Comprehension
- [ COSA_VOGLIO_SALVARE  for  ELEMENTO  in  COLLEZIONE  if  CONDIZIONE ]
```
id_brani = [t.track_id for t in lista_tracce if t.milliseconds > 180000]
```
## Dict Comprehension
```
popolarita_iniziale = {artista: 0 for artista in lista_artisti}
```
## Set Comprehension
```
nazioni_uniche = {f.billing_country for f in lista_fatture if f.billing_country is not None}
```
## if-else (Operatore Ternario)
```
# Se il brano costa più di 1$, scrivi "Premium", altrimenti "Standard"
tag_prezzi = ["Premium" if t.unit_price > 1.00 else "Standard" for t in lista_tracce]
```


