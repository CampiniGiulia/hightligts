# Esempi ricorsione
### Dato un valore K intero fornito dall’utente, l’obiettivo del programma è di identificare il range di età anagrafica più piccolo per il quale sia possibile identificare un set di K piloti distinti tale per cui:
1. ciascun pilota faccia parte di una componente connessa distinta, ovvero i piloti non possono essere stati compagni di squadra;
2. la differenza fra la data di nascita del pilota più “anziano” e quello più “giovane” della coorte selezionata sia
minimo;
### Soluzione:
``` 
def getListaPilotiOttima(self, k):
        self._optListPiloti = []
        self._minDistGiorni = 100*365

        components = list(nx.connected_components(self._graph))

        if len(components) < k:
                # allora non ho abbastanza componenti connesse da cui pescare, e non posso trovare una sol
                return None, 0

        parziale = []
        self._ricorsione(components, k, parziale,0 )
        return self._optListPiloti, self._minDistGiorni

def _ricorsione(self, components, k, parziale, indexComponente):
        # condizione di ottimalità
        if len(parziale) == k:
                # ho una soluzione accettabile.
                dateDiNascita = [p.dob for p in parziale]
                diffEtaPiloti = (max(dateDiNascita) - min(dateDiNascita)).days
                if diffEtaPiloti < self._minDistGiorni:
                        self._optListPiloti = copy.deepcopy(parziale)
                        self._minDistGiorni = diffEtaPiloti
                return

        #condizione di terminazione
        #1) esco se l'indice che indica quale comp connessa sto considerando a questa iterazione è diventato maggiore o
        #uguale al numero di componenti connesse totali, perchè vuol dire che non ho altre componenti connesse da cui pescare
        #2) l'altro motivo è se non ho abbastanza componenti rimanenti per arrivare a k piloti in parziale
        if indexComponente >= len(components) or (len(components) - indexComponente) < (k-len(parziale)):
                return

        # se non sono uscito, allora posso aggiungere ancora piloti. Per questa componente, di indice indexComponente,
        # provo ad ingaggiare un pilota oppure a non ingaggiare nessuno.

        #caso 1, inserisco un pilota appartenente a questa comp connessa. In questo branch provo tutti i piloti che
        # fanno parte della componente connessa in esame.
        componente = components[indexComponente]
        for pilota in componente:
                parziale.append(pilota)
                self._ricorsione(components, k, parziale, indexComponente+1)
                parziale.pop()

        #caso 2, mi tengo un brach di esplorazione in cui io non ho preso proprio nessuno da questa comonente.
        self._ricorsione(components, k, parziale, indexComponente+1)
```
------------------------------------------------------------------------------------------------------------------------------
### Si inseriscano nei dropdown “Start Product” ed “End Product” tutti i nodi presenti nel grafo, e si implementi un algoritmo ricorsivo che identifichi un cammino ottimo tale per cui:
- Il cammino parti dal nodo identificato come Start Product e termini nel nodo identificato come End Product;
- La lunghezza del cammino sia pari a Lun, valore numerico fornito dall’utente nel campo “Lunghezza
cammino”;
- Il cammino attraversi gli archi rispettando i versi;
- Un nodo non può essere attraversato più volte;
- La somma dei pesi degli archi deve essere massima.
### Soluzione:
```
def getBestPath(self, lun, start, end):
        self._bestPath = []
        self._bestScore = 0
        parziale = [start]
        self._ricorsione(parziale, lun, end)
        return self._bestPath, self._bestScore

def _ricorsione(self, parziale, lun, end):
        if len(parziale) == lun:
                if parziale[-1] == end and self._getScore(parziale) > self._bestScore:
                        self._bestScore = self._getScore(parziale)
                        self._bestPath = copy.deepcopy(parziale)
                return

        for n in self._graph.successors(parziale[-1]):
                if n not in parziale:
                        parziale.append(n)
                        self._ricorsione(parziale, lun, end)
                        parziale.pop()

def _getScore(self, parziale):
        score = 0
        for i in range(0, len(parziale)-1):
                score += self._graph[parziale[i]][parziale[i+1]]["weight"]
        return score
```
--------------------------------------------------------------------------------------------------------------------------------------
### Selezionare dal corrispondente menu a tendina un artista. Facendo click sul pulsante “Cerca percorso”, individuare il percorso più lungo: Trovare un cammino semplice di lunghezza massima tale che ogni arco successivo abbia peso strettamente crescente.
### Soluzione:
```
# QUESTA è una mia (professore) soluzione verbosa.
    def getTopLista1(self, source):
         self._bestListaArt = [source]
         self._ricorsione1([source], 0)
         return self._bestListaArt

    def _ricorsione1(self, parziale, pesoCorr):
         if len(parziale) > len(self._bestListaArt):
             self._bestListaArt = copy.deepcopy(parziale)

         last = parziale[-1]
         print("NODE:", last, "PESO CORRENTE:", pesoCorr)

         for _, succ, data in self._grafo.out_edges(last, data=True):
             pesoArco = data["weight"]
             print("  CANDIDATO:", succ, "PESO:", pesoArco)

             if succ not in parziale and pesoArco > pesoCorr:
                 print("  -> SCENDO")
                 parziale.append(succ)
                 self._ricorsione1(parziale, pesoArco)
                 parziale.pop()
             else:
                 print("  -> SCARTO")

# Soluzione mia
   def calcolaPercorso(self, source):
        self._bestSoluzione = []
        parziale = [source]
        self._ricorsione(parziale)
        return self._bestSoluzione

    def _ricorsione(self, parziale):
        #condizione ottimale
        if len(parziale) > len(self._bestSoluzione):
                self._bestSoluzione = copy.deepcopy(parziale)

        #condizione ricorsiva
        for n in self._graph.successors(parziale[-1]):
            if n not in parziale:
                if len(parziale)<2 or self._graph[parziale[-1]][n]["weight"] > self._graph[parziale[-2]][parziale[-1]]["weight"]:
                    parziale.append(n)
                    self._ricorsione(parziale)
                    parziale.pop()
```
-------------------------------------------------------------------------------------------------------------------------
### Trovare un cammino semplice di lunghezza massima tale che ogni nodo successivo abbia un età strettamente decrescente.
### Soluzione prof:
```
def getBestPath(self):

        self._bestPath = []

        for start in self._graph.nodes():
            partial = [start]
            self._ricorsione(partial)

        return self._bestPath

    def _ricorsione(self, partial):

        if len(partial) > len(self._bestPath):
            self._bestPath = list(partial)

        current = partial[-1]

        for _, successor in self._graph.edges(current):

            # vincolo: età decrescente
            if successor not in partial and successor.birth_date > current.birth_date:
                partial.append(successor)

                self._ricorsione(partial)

                partial.pop()
```
### Soluzione mia:
```
        def getPercorso(self):
        self._bestSoluzione = []
        parziale = []
        for n in self._graph.nodes():
            parziale.append(n)
            self._ricorsione(parziale)
            parziale.pop()
        return self._bestSoluzione

    def _ricorsione(self, parziale):
        print("entrato")
        #cond. ottimalità
        if len(parziale) > len(self._bestSoluzione):
            self._bestSoluzione = copy.deepcopy(parziale)
            print("ottima")
        for n in self._graph.neighbors(parziale[-1]):
            if n.date_of_birth > parziale[-1].date_of_birth:
                if n not in parziale:
                    print("continua")
                    parziale.append(n)
                    self._ricorsione(parziale)
                    parziale.pop()
```

-------------------------------------------------------------------------------------------------------------------------
### Calcolare un percorso di peso massimo avente le seguenti caratteristiche:
- Il punto di partenza è il vertice selezionato al punto 1.e.
- Ogni vertice può comparire una sola volta
- Il peso degli archi nel percorso deve essere strettamente decrescente.

### Soluzione: CERCO DI CAPIRE (BASEBALL)

-------------------------------------------------------------------------------------------------------------------------
### Calcolare un percorso di peso massimo avente le seguenti caratteristiche:
- vertice di partenza è quello selezionato nel punto 1.c
- peso degli archi nel percorso deve essere strettamente decrescente
-  un vertice può entrare una volta sola nel percorso
  
### Soluzione (prof, ma metterei successor al posto di neighbours):
```
def getBestPath(self, startStr):
        self._bestPath = []
        self._bestScore = 0

        start = self._idMap[int(startStr)]

        parziale = [start]

        vicini = self._graph.neighbors(start)
        for v in vicini:
            parziale.append(v)
            self._ricorsione(parziale)
            parziale.pop()

        return self._bestPath, self._bestScore
    def _ricorsione(self, parziale):
        if self.getScore(parziale) > self._bestScore:
            self._bestScore = self.getScore(parziale)
            self._bestPath = copy.deepcopy(parziale)

        for v in self._graph.neighbors(parziale[-1]):
            if (v not in parziale and #check if not in parziale
                    self._graph[parziale[-2]][parziale[-1]]["weight"] >
                    self._graph[parziale[-1]][v]["weight"]): #check if peso nuovo arco è minore del precedente
                parziale.append(v)
                self._ricorsione(parziale)
                parziale.pop()
        def getScore(self, listOfNodes):
                tot = 0
                for i in range(len(listOfNodes) - 1):
                    tot += self._graph[listOfNodes[i]][listOfNodes[i + 1]]["weight"]
```
-------------------------------------------------------------------------------------------------------------------------
### Partendo dal grafo costruito nel punto precedente, si vuole identificare il “sotto-campionato più emozionante” di K gare che statisticamente presentano il maggior numero di “imprevisti”. Ai fini di questo punto, si considerino come circuiti validi solamente i circuiti che appartengono alla componente connessa identificata nel punto precedente e nei quali si è corso almeno M volte nel range di anni selezionato. 
- indice di “imprevedibilità” I di una specifica gara il seguente valore numerico:
I = 1- nP/nPtot
dove nP rappresenta il numero di piloti che hanno correttamente tagliato il traguardo a pari giri con il leader in tutte
le edizioni corse sul circuito selezionato (ovvero lo stesso valore calcolato al punto precedente come componente del peso degli archi), mentre nPtot rappresenta la totalità dei piloti che risultano iscritti alle varie gare (ovvero, la somma delle lunghezze delle liste indipendentemente dalla presenza del valore Null come tempo di percorrenza totale).
Per valutare l’indice complessivo di imprevedibilità di una lista di circuiti si utilizzi la somma degli indici I delle varie gare inserite nella lista.
### Soluzione:
```
def getCampionatoIdeale(self, soglia, numAnni):

        tic = datetime.now()
        compConnessa = list(nx.connected_components(self._graph))[0]
        self._bestScore = 0
        self._optListCircuiti = []
        parziale = []
        rimanenti = copy.deepcopy(compConnessa)

        for c in compConnessa:
            if len(list(c.results.keys())) >= numAnni:
                parziale.append(c)
                rimanenti.remove(c)
                self._ricorsione(parziale, rimanenti, soglia, numAnni)
                parziale.pop()
                rimanenti.add(c)

        listOfScores = []
        for c in self._optListCircuiti:
            listOfScores.append(self._calcolaIndiceCircuito(c))

        toc = datetime.now()
        print(f"Tempo di esecuzione: {toc-tic}")
        return self._optListCircuiti, self._bestScore, listOfScores
def _ricorsione(self, parziale, rimanenti, soglia, numAnni):
        if len(parziale) == soglia:
            #questa fa sia da condizione di ottimalità sia da condizione di terminazione
            if self._getScoreSoluzione(parziale) > self._bestScore:
                self._bestScore = self._getScoreSoluzione(parziale)
                self._optListCircuiti = copy.deepcopy(parziale)
            return

        for c in rimanenti:
            if len(list(c.results.keys())) >= numAnni:
                parziale.append(c)
                rimanenti.remove(c)
                self._ricorsione(parziale, rimanenti, soglia, numAnni)
                parziale.pop()
                rimanenti.add(c)

    def _getScoreSoluzione(self, listOfCircuits):
        listOfScores = []
        for c in listOfCircuits:
            listOfScores.append(self._calcolaIndiceCircuito(c))

        return sum(listOfScores)
def _calcolaIndiceCircuito(self, circuito):
        nP = 0
        nPtot = 0

        if len(circuito.results.values()) == 0:
            return 0 #questo caso non dovrebbe mai accadere perchè questa funzione viene chiamata solo su nodi appartenenti alla componente connessa.

        for r in circuito.results.values():  # per ogni anno prendo tutti i risultati della gara
            nPtot += len(r)
            for p in r:  # per ogni pilota
                if p.time is not None:
                    nP += 1

        return 1-nP/nPtot
```


