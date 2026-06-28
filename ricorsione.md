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

#### Nel controllore per trovare il pilota più giovane: youngest = min(best_path, key=lambda x: x.dob)

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

### Soluzione: 
```
    def getPercorso(self, team):
        source = team
        self.bestPercorso = []
        self.bestScore = 0

        # Inizializziamo il cammino solo con la sorgente
        parziale = [source]

        # Avviamo la ricorsione.
        # Passiamo float('inf') come peso dell'ultimo arco,
        # così il primo arco incontrato sarà sicuramente minore di "infinito".
        self._ricorsione(parziale, peso_precedente=float('inf'), score_attuale=0)

        return self.bestPercorso, self.bestScore

    def _ricorsione(self, parziale, peso_precedente, score_attuale):

        # Condizione di ottimalità: valutiamo ad ogni passo se abbiamo fatto meglio
        if score_attuale > self.bestScore:
            self.bestPercorso = copy.deepcopy(parziale)
            print(score_attuale)
            self.bestScore = score_attuale

        # Esploriamo i vicini dell'ultimo nodo inserito
        ultimo_nodo = parziale[-1]
        for v in self._graph.neighbors(ultimo_nodo):
            peso_arco = self._graph[ultimo_nodo][v]["weight"]

            # Vincolo 1: Il peso dell'arco deve essere strettamente decrescente
            # Vincolo 2: Il nodo non deve essere già stato visitato
            if peso_arco < peso_precedente and v not in parziale:
                # Backtracking
                print("provo")
                parziale.append(v)

                # Ricorsione: aggiorniamo il peso precedente e sommiamo il peso al volo
                self._ricorsione(parziale, peso_arco, score_attuale + peso_arco)

                # Rimuoviamo il nodo per testare le altre strade
                parziale.pop()
    def score(self, parziale):
        score = 0
        for i in range(0, len(parziale)-1):
            score += self._graph[parziale[i]][parziale[i+1]]["weight"]
        return score

    def getPathV2(self, v0):
        self._bestPath = []
        self._bestObjVal = 0

        parziale = [v0]

        listaVicini = self.getDetails(parziale[-1])
        parziale.append(listaVicini[0][0])
        self._ricorsioneV2(parziale)

        return self._bestPath, self._bestObjVal

    def _ricorsioneV2(self, parziale):
        # 1 condizione di ottimalità, verifico se la parziale è migliore del best
        if self.score(parziale) > self._bestObjVal:
            self._bestPath = copy.deepcopy(parziale)
            self._bestObjVal = self.score(parziale)

        # 2 condizione di terminazione, verifico se posso continuare

        # 3 faccio la mia ricorsione
        # listaVicini = []
        # for v in self._grafo.neighbors(parziale[-1]):
        #     edgeV = self._grafo[parziale[-1]][v]["weight"]
        #     listaVicini.append((v, edgeV))
        #
        # listaVicini.sort(key= lambda x: x[1], reverse=True)

        listaVicini = self.getDetails(parziale[-1])

        for v in listaVicini:
            if v[0] not in parziale and self._graph[parziale[-2]][parziale[-1]]["weight"] > v[1]:
                parziale.append(v[0])
                self._ricorsioneV2(parziale)
                parziale.pop()
                return
```


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


-------------------------------------------------------------------------------------------------------------------------
### Dato il grafo costruito al punto precedente, si vuole identificare un cammino sul grafo costituito da avvistamenti di durata sempre crescente (strettamente crescente) che massimizzi un punteggio composto dai seguenti termini:
- +100 punti per ogni avvistamento nel cammino
- +200 punti per ogni avvistamento del cammino che è occorso nello stesso mese dell’avvistamento
precedente (ovviamente non applicabile al primo avvistamento del cammino, dato che non ha un
avvistamento che lo precede).
Inoltre, il cammino può contenere al massimo 3 avvistamenti dello stesso mese.
### Soluzione (mia):
```
       def getAvvistamenti(self):
        self._bestSoluzione = []
        self._bestScore = 0
        parziale = []
        self._mesiVisitati = dict.fromkeys(range(1,13), 0)
        for n in self._graph.nodes:
            parziale.append(n)
            self._ricorsione(parziale, n.duration)
            parziale.pop()
        return self._bestSoluzione, self._bestScore

    def _ricorsione(self, parziale, durataPrec):
        #cond ottimale
        if self.score(parziale) > self._bestScore:
            print("ottimo")
            self._bestSoluzione = copy.deepcopy(parziale)
            self._bestScore = self.score(parziale)

        for n in self._graph.neighbors(parziale[-1]):
            print("continua")
            if n not in parziale and self._mesiVisitati[n.datetime.month] <= 2 and n.duration > durataPrec:
                print("aggiunge")
                parziale.append(n)
                self._mesiVisitati[n.datetime.month] += 1
                self._ricorsione(parziale, n.duration)
                self._mesiVisitati[n.datetime.month] -= 1
                parziale.pop()

    #sostituito con dict.fromkeys(range(1,13),0)
    def amm(self, n, parziale):
        mese = n.datetime.month
        tot = 0
        for m in parziale:
            if m.datetime.month == mese:
                tot += 1
        if tot <= 2:
            return True
        return False

    def score(self, parziale):
        tot = 0
        for n in range(0, len(parziale)-1):
            if parziale[n].datetime.month == parziale[n+1].datetime.month:
                tot+=200
        tot += 100 * len(parziale)
        return tot

```
-------------------------------------------------------------------------------------------------------------------------
### Dato il grafo costruito al punto precedente, si vuole identificare un cammino che ottimizzi gli avvistamenti fatti in relazione alla distanza percorsa. Questo viene modellato come un punteggio da massimizzare definito come segue:
- Il punteggio è dato dalla somma totale dei pesi degli archi DIVISO la distanza totale percorsa nel cammino. La distanza nel percorrere un arco può essere calcolata usando la funzione distance_HV fornita già implementata nella classe State.
Il cammino deve inoltre rispettare i seguenti vincoli:
- Da un nodo, ci si può spostare solamente verso un nodo con densità di popolazione strettamente
crescente. La densità di popolazione è calcolata come Population/Area (Colonne del DB)
### Soluzione (mia):
```
    def getBestPercorso(self):
        self._bestSoluzione = []
        self._bestScore = 0
        parziale = []
        for n in list(self._graph.nodes):
            parziale.append(n)
            self._ricorsione(parziale, self._densita(n))
            parziale.pop()
        listaTuple = []
        for n in self._bestSoluzione:
            listaTuple.append((n, self._densita(n)))
        return self._bestSoluzione, self._bestScore, listaTuple

    def _ricorsione(self, parziale, densita):
        if self._score(parziale) > self._bestScore:
            self._bestSoluzione = copy.deepcopy(parziale)
            self._bestScore = self._score(parziale)
        for n in self._graph.neighbors(parziale[-1]):
            if self._densita(n) > densita and n not in parziale:
                parziale.append(n)

                self._ricorsione(parziale, self._densita(n))
                parziale.pop()


    def _densita(self, n):
        return n.Population/n.Area

    def _score(self, parziale):
        if len(parziale) <2:
            return 0
        pesoTot = 0
        distTot = 0
        for i in range(0, len(parziale)-1):
            pesoTot += self._graph[parziale[i]][parziale[i+1]]["weight"]
            distTot += parziale[i].distance_HV(parziale[i+1])
        if distTot != 0:
            return pesoTot / distTot
        else:
            return 0
```
-------------------------------------------------------------------------------------------------------------------------
### A partire dal grafo definito al punto 1, si vuole implementare una procedura ricorsiva che identifichi il cammino più lungo che minimizza la somma dei pesi del percorso, e con le seguenti caratteristiche:
- Un nodo può essere attraversato una sola volta.
- Gli archi possono essere attraversati solo nella loro direzione di percorrenza
- Nel cammino, non ci possono essere due geni consecutivi con lo stesso valore del campo Essential
- Si possono attraversare solo archi di peso crescente (ovvero ogni nuovo arco percorso deve avere
peso >= del precedente).
### Soluzione (mia):
```
    def getBestPath(self):
        self._bestSoluzione = []
        self._bestScore = float('inf')
        parziale = []
        for n in self._graph.nodes:
            parziale.append(n)
            self._ricorsione(parziale, float('inf'))
            parziale.pop()
        return self._bestSoluzione, self._bestScore

    def _ricorsione(self, parziale, peso):
        if len(parziale) > len(self._bestSoluzione):
            self._bestSoluzione = copy.deepcopy(parziale)
            self._bestScore= self._score(parziale)

        elif len(parziale) == len(self._bestSoluzione):
            if (self._score(parziale)<self._bestScore):
                self._bestSoluzione = copy.deepcopy(parziale)
                self._bestScore= self._score(parziale)

        for n in self._graph.successors(parziale[-1]):
            if n not in parziale:
                if n.Essential != parziale[-1].Essential and self._graph[parziale[-1]][n]["weight"] >= peso:
                    parziale.append(n)
                    self._ricorsione(parziale, self._graph[parziale[-1]][n]["weight"])
                    parziale.pop()

    def _score(self, parziale):
        tot = 0
        for n in range(0, len(parziale)-1):
            tot += self._graph[parziale[n]][parziale[n+1]]["weight"]
        return tot
```
-------------------------------------------------------------------------------------------------------------------------
### Si implementi una procedura ricorsiva che sia in grado di identificare una lista di nodi appartenenti al grafo di cui al punto 1, tale per cui siano verificate le seguenti richieste:
- I nodi devono essere presentati nella lista ordinati in senso crescente di GeneID
-  I nodi devono essere o tutti “Essenziali” oppure tutti “Non-Essenziali”. Vanno esclusi i nodi per i quali
     l’essenzialità non è nota (ovvero in cui il campo è “?”)
- La soluzione proposta deve massimizzare il numero di elementi nella lista.
- A parità di lunghezza della soluzione, il numero di componenti connesse del sottografo ottenuto considerando solo i nodi del set trovato deve essere minimo (hint: usare il metodo Graph.subgraph())
### Soluzione (mia):
```
    def getLista(self):
       #si può migliorare ordinando prima i geneID e selezionando solo quelli con il campo giusto
        self._bestSoluzione = []
        self._compConnesse = 0
        parziale = []
        daVisitare = copy.deepcopy(self._graph.nodes)
        for nodi in daVisitare:
            if nodi.essential == "Essential" or nodi.essential == "Non-Essential":
                parziale.append(nodi)
                daVisitare.remove(nodi)
                self._ricorsione(parziale, nodi.essential, daVisitare)
                parziale.pop()
                daVisitare.append(nodi)
        return self._bestSoluzione

    def _ricorsione(self, parziale, tipo, daVisitare):
        #con. ottimale
        if len(parziale) > len(self._bestSoluzione):
            self._bestSoluzione = copy.deepcopy(parziale)

        elif len(parziale) == len(self._bestSoluzione):
            compConn = list(nx.connected_components(self._graph.subgraph(parziale)))
            if len(compConn) < self._compConnesse:
                self._compConnesse = len(compConn)
                self._bestSoluzione = copy.deepcopy(parziale)

        for n in daVisitare:
            if n.essential == tipo:
                if n.GeneID > parziale[-1].GeneID:
                    if n not in parziale:
                        parziale.append(n)
                        daVisitare.remove(n)
                        self._ricorsione(parziale, n.essential, daVisitare)
                        parziale.pop()
                        daVisitare.append(n)
```

