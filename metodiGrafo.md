# METODI PIù COMUNI PARTE 1

-   **Stampare il numero di componenti connesse:**  ```def getNumCompConnesse(self):  
        compConn = nx.number_connected_components(self._graph)  
        return compConn```

-  **Identificare la componente connessa di dimensione maggiore:** ```def getBestCompConnessa(self):
            largest_cc = max(nx.connected_components(self._graph), key=len)
            return largest_cc```

- **... in senso decrescente secondo il grado dei nodi:** ```def getBestDecresc(self):
        largest_cc = max(nx.connected_components(self._graph), key=len)
        pilTuple = []
        for n in largest_cc:
            grado = self._graph.degree(n)
            pilTuple.append((n, grado))
        pilTuple.sort(key=lambda x: x[1], reverse=True)
        return pilTuple```

- **Stampare gli n archi con peso maggiore:** ```def getBestEdges(self):
        archi = list(self._graph.edges(data=True))
        archi.sort(key=lambda x: x[2]["weight"], reverse=True)
        return archi[0:3]```

    
