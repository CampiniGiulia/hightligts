# Esempi ricorsione
### Si vuole valutare la coesione generazionale della coorte di piloti considerata. Dato un valore K intero fornito dall’utente, l’obiettivo del programma è di identificare il range di età anagrafica più piccolo per il quale sia possibile identificare un set di K piloti distinti tale per cui:
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
