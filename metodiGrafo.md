# METODI PIù COMUNI per i GRAFI 

-   **Stampare il numero di componenti connesse:**
  ```
def getNumCompConnesse(self):  
        compConn = nx.number_connected_components(self._graph)  
        return compConn
```

-  **Identificare la componente connessa di dimensione maggiore:**
 ```
def getBestCompConnessa(self):
            largest_cc = max(nx.connected_components(self._graph), key=len)
            return largest_cc
```

- **... in senso decrescente secondo il grado dei nodi:**
```
def getBestDecresc(self):
        largest_cc = max(nx.connected_components(self._graph), key=len)
        pilTuple = []
        for n in largest_cc:
            grado = self._graph.degree(n)
            pilTuple.append((n, grado))
        pilTuple.sort(key=lambda x: x[1], reverse=True)
        return pilTuple
```

- **Stampare gli n archi con peso maggiore:**
```
def getBestEdges(self):
        archi = list(self._graph.edges(data=True))
        archi.sort(key=lambda x: x[2]["weight"], reverse=True)
        return archi[0:3]
```
- **stampare per tale squadra l’elenco delle squadre adiacenti, ed il peso degli archi corrispondenti, in ordine decrescente di peso:**
```
 def getDetails(self, team):
        vicini = self._graph.neighbors(team)
        viciniTuple = []
        for v in vicini:
            viciniTuple.append((v, self._graph[team][v]["weight"]))
        viciniTuple.sort(key=lambda x: x[1], reverse=True)
        return viciniTuple
```

- **stampare i nodi la cui somma dei pesi degli archi entranti meno la somma dei pesi degli archi uscenti è massima**:
```
def getTopProdotti(self):
        tupleProdotti = []
        for nodi in self._graph.nodes:
            vend = 0
            for n in self._graph.in_edges(nodi, data=True):
                vend += n[2]['weight']
            for n in self._graph.out_edges(nodi, data=True):
                vend -= n[2]['weight']
            tupleProdotti.append((nodi, vend))
        tupleProdotti.sort(key=lambda x: x[1], reverse=True)
        return tupleProdotti[:5]
```
- **Identificare la componente connessa di dimensione maggiore, e stamparne tutti i nodi, ordinati in senso decrescente di peso massimo degli archi incidenti**: (per il minimo inizializzo a -1 e nell'if faccio entrare con -1)
```
    def getMaxComponente(self):
        maxComp = max(nx.connected_components(self._graph), key=len)
        archi = []
        for n in maxComp:
            pesoMax = 0
            for i in (self._graph.neighbors(n)):
                if self._graph[n][i]['weight'] > pesoMax:
                    pesoMax = self._graph[n][i]['weight']
            archi.append((n, pesoMax))
        archi.sort(key=lambda x: x[1], reverse=True)
        return archi
```
- **determinare la componente connessa del grafo che contiene il vertice selezionato precedentemente e stampare il numero di vertici che la compongono**:
```
def getInfoCompConnessa(self, id_oggetto):
        #cercare la componente connessa che contiene id_oggetto

        if not self.hasNode(id_oggetto):
            return None

        source = self._idMapAO[id_oggetto]

        #Strategia 1
        dfsTree = nx.dfs_tree(self._graph, source)
        print("size connessa con dfs_tree", len(dfsTree.nodes()))

        #Strategia 2
        dfsPred = nx.dfs_predecessors(self._graph, source)
        print("size connessa con dfs_predecessors", len(dfsPred.values()))

        #Strategia 3
        conn = nx.node_connected_component(self._graph, source)
        print("size connessa con node_connected_component", len(conn))

        return len(conn)

    def hasNode(self, id_oggetto):
        return id_oggetto in self._idMapAO
```
- **Stampare il numero di componenti debolmente connesse**:
```
def get_num_connesse(self):
        return nx.number_weakly_connected_components(self._grafo)
```
- **identificare la componente debolmente connessa di dimensione maggiore, e stamparne i nodi**:
```
def get_largest_connessa(self):
        conn = list(nx.weakly_connected_components(self._grafo))
        conn.sort(key=lambda x: len(x), reverse=True)
        return conn[0]
```
- **I 5 nodi col maggiore numero di archi uscenti col numero di archi uscenti ed il peso complessivo di
questi archi (la somma dei loro pesi). I nodi devono essere stampati in ordine decrescente per numero di archi uscenti.**:
```
    def get5BestNodi(self):
        listaTuple = []
        for n in self._graph.nodes:
            nArchi = 0
            totPeso = 0
            for i in self._graph.out_edges(n, data=True):
                nArchi += 1
                totPeso += i[2]['weight']
            listaTuple.append((n, nArchi, totPeso))
        listaTuple.sort(key=lambda x: x[1], reverse=True)
        return listaTuple[:5]
```


  
# Visite
1. Da un nodo **source** a tutti gli altri, senza obiettivi di minimizzazione:
   - DFS:
     - dfs_edges(G[, source, depth_limit, ...]) = Iterate over edges in a depth-first-search (DFS).
     - dfs_tree(G[, source, depth_limit, ...]) = Returns oriented tree constructed from a depth-first-search from source.
     - dfs_predecessors(G[, source, depth_limit, ...]) = Returns dictionary of predecessors in depth-first-search from source.
     - dfs_successors(G[, source, depth_limit, ...]) = Returns dictionary of successors in depth-first-search from source.
     - dfs_preorder_nodes(G[, source, depth_limit, ...]) = Generate nodes in a depth-first-search pre-ordering starting at source.
     - dfs_postorder_nodes(G[, source, ...]) = Generate nodes in a depth-first-search post-ordering starting at source.
     - dfs_labeled_edges(G[, source, depth_limit, ...]) = Iterate over edges in a depth-first-search (DFS) labeled by type.
   - BFS (dal punto di vista del numero di archi restituitsce i cammini minimi):
     - bfs_edges(G, source[, reverse, depth_limit, ...]) = Iterate over edges in a breadth-first-search starting at source.
     - bfs_layers(G, sources) = Returns an iterator of all the layers in breadth-first search traversal.
     - bfs_tree(G, source[, reverse, depth_limit, ...]) = Returns an oriented tree constructed from of a breadth-first-search starting at source.
     - bfs_predecessors(G, source[, depth_limit, ...]) = Returns an iterator of predecessors in breadth-first-search from source.
     - bfs_successors(G, source[, depth_limit, ...]) = Returns an iterator of successors in breadth-first-search from source.
     - descendants_at_distance(G, source, distance) = Returns all nodes at a fixed distance from source in G.
     - bfs_labeled_edges(G, sources) = Iterate over edges in a breadth-first search (BFS) labeled by type.
     - generic_bfs_edges(G, source[, neighbors, ...]) = Iterate over edges in a breadth-first search.
2. All-source shortest path (AS-SP)
   - ```floyd_warshall(G, weight='weight')```
3. Single-source shortest path (SS-SP)
   - all_pairs_bellman_ford_path_length(G, weight='weight')
   - single_source_bellman_ford_path(G, source, weight='weight')
   - dijkstra_path(G, source, target, weight='weight')
4. Cicli: guardo ultimo pacco di slide

# Libreria
- nx.Graph()
- nx.DiGraph()
- nx.MultiGraph()
- nx.MultiDiGraph()
- graph[u][v] per ottenre gli attributi dell'arco
- g.nodes()
- g.edges()

# TEORIA GRAFI
- **undirected** graph:
  - degree: è il numero degli archi incidenti a un nodo (```Graph.degree(nodo) ```)
  - è **connesso** se per ogni coppia di vertici che un percorso che li unisce
  - le componenti connesse sono i sottografi connessi di dimensione massima
  - hanno dei vicini ```Graph.neighbors(n)[source]```
- **directed** graph:
  - in-degree: numero archi entranti nel nodo (```DiGraph.in_degree(nodo)```)
  - out-degree: numero archi uscenti dal nodo (```DiGraph.out_degree(nodo)```)
  - degree: somma di in-degree e out-degree (```DiGraph.in_degree(nodo)```)
  - è fortemente **connesso** se per ogni coppia di vertici che un percorso che li unisce
  - i vicini si dividono in successori e predecessori (```DiGraph.successors(n) DiGraph.predecessors(n)```)
- Lunghezza di un cammino = numero di archi -1
- un grafo è **completo** se per ogni coppia di vertici esiste un arco che li unisce simbolo K_n
- la **densità** di un grafo è tra il numeor degli archi e il numero totale di archi possibili (completo)
- **forest**: un grafo non diretto aciclico
- **tree**: un grafo connesso aciclico
- **rooted tree**: un albero in cui si individua una radice che conferisce una direzione all'alberp
