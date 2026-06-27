# Esempi per la costruzione particolare degli archo
###  gli archi colleghino tutte le coppie distinte di squadre
``` myedges = list(itertools.combinations(self._teams, 2)) ```
#### Spiegazione: 
- generare tutte le possibili coppie uniche di elementi a partire da una lista (o un insieme) chiamata self._teams.
- itertools.combinations(..., 2): La funzione combinations del modulo itertools prende la lista e genera tutte le combinazioni possibili di lunghezza 2 (coppie). La caratteristica fondamentale delle combinazioni è che l'ordine non conta e non ci sono ripetizioni

### per controllare se esistono dei nodi nel grafo prima di inserirli:
uso **self._graph.has_node(self._idMap[i])** nelle parentesi inserisco l'oggeto nodo
