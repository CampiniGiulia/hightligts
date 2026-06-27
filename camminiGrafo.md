# Trovare i cammini

### Trovare il cammino più lungo a partire da un nodo:
```
def getCammino(self, sourceStr):
        source = self._idMap[int(sourceStr)]
        lp = []

        #for source in self._graph.nodes:
        tree = nx.dfs_tree(self._graph, source) #restituisce un grafo 
        nodi = list(tree.nodes())

        for node in nodi:
            tmp = [node]

            while tmp[0] != source:
                pred = nx.predecessor(tree, source, tmp[0]) #restituisce i padri di temp[0] nell'albero che parte da source
                tmp.insert(0, pred[0]) #[0] perchè pred restituisce una lista con un elemento e io voglio solo l'elemento

            if len(tmp) > len(lp):
                lp = copy.deepcopy(tmp)

        return lp
```
### Calcola Connessione Minima”, si selezioni un nodo di partenza ("Start") e un nodo di arrivo ("End") dagli appositi menù a tendina.
- Si implementi un algoritmo che utilizzi la visita del grafo più opportuna per identificare il cammino minimo (in termini di numero di archi attraversati) che connette il nodo Start al nodo End.
- Se i nodi sono connessi, si mostri a schermo il cammino completo e il numero totale di tappe.
- Se i due nodi appartengono a componenti disconnesse, si informi l'utente con un messaggio di errore.
```
import networkx as nx

def getCamminoMinimoBFS(self, source, target):
    # 1. Controlliamo se esiste una strada tra i due nodi prima di iniziare
    if not nx.has_path(self._graph, source, target):
        return None # I nodi non sono connessi
        
    # 2. Creiamo l'albero BFS partendo dalla sorgente
    # La BFS garantisce il cammino minimo per numero di archi
    albero_bfs = nx.bfs_tree(self._graph, source)
    
    # 3. Generiamo il dizionario dei predecessori per questo albero
    predecessori = nx.predecessor(albero_bfs, source)
    
    # 4. Ricostruiamo il cammino partendo dal fondo (target) verso la sorgente
    cammino = [target]
    
    while cammino[0] != source:
        # Recuperiamo il padre del nodo attualmente in cima alla lista
        padre = predecessori[cammino[0]][0]
        # Lo inseriamo in testa
        cammino.insert(0, padre)
        
    return cammino
```

