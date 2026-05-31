# Metodi più comuni del controllore
### Trasformare una lista di oggetti in Option:
```
      anni = self._model.getAllYear()
        anniOPTI = list(map(lambda x: ft.dropdown.Option(data= x, text= x, on_click= self._choiceDDI), anni))
        self._view._ddAnno1.options = anniOPTI
```
### Salavare l'oggetto selezionato nella tendina:
```
    def _choiceDDI(self, e):
        self._annoInizio = int(e.control.data) #inizializzo nell'init  self._annoInizio = None
        print(self._annoInizio)
```
### Creazione del grafo + stampa dettagli:
```
        self._model.buildGraph()
        self.grafo = True
        n, e = self._model.getDetails()
        self._view.txt_result.controls.clear()
        self._view.txt_result.controls.append(
            ft.Text(f"Grafo creato: "))
        self._view.txt_result.controls.append(
            ft.Text(f"Numero di nodi: {n} \nNumero di archi: {e}"))
        self._view.update_page()
```

