# Trovare i cammini

### Trovare il cammino più lungo a partire da un nodo:
```
def getCammino(self, sourceStr):
        source = self._idMap[int(sourceStr)]
        lp = []

        #for source in self._graph.nodes:
        tree = nx.dfs_tree(self._graph, source)
        nodi = list(tree.nodes())

        for node in nodi:
            tmp = [node]

            while tmp[0] != source:
                pred = nx.predecessor(tree, source, tmp[0])
                tmp.insert(0, pred[0])

            if len(tmp) > len(lp):
                lp = copy.deepcopy(tmp)

        return lp
```
