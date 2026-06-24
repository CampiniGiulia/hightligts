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
### (versione2) Aggiungere oggetti Option a una lista:
1.
```
def populate_dd_anno(self):
        """methodo che popola la tendina con tutti gli anni in cui ci sono state vendite,
        prendendo le informazioni dal database"""
        anni = self._model.get_years()
        for anno in anni:
            self._view.dd_anno.options.append(ft.dropdown.Option(anno[0]))
        self._view.update_page()

    def read_anno(self, e):
        """event handler che legge l'anno scelto dal menu a tendina ogniqualvolta viene cambiata
        la scelta, e lo memorizza in una variabile di instanza. L'anno è un intero, se si tratta di un anno,
        oppure un None se viene scelta l'opzione nessun filtro sull'anno"""
        if e.control.value == "None":
            self._anno = None
        else:
            self._anno = e.control.value
```
- (nella view è così: ```self.dd_anno = ft.Dropdown(width=200,
                                   hint_text="Filtro per anno",
                                   label="anno",
                                   options=[ft.dropdown.Option(key="None",
                                                            text="Nessun filtro")],
                                                            on_change=self._controller.read_anno)
        self._controller.populate_dd_anno()``` )
2.
```
retailers = self._model.get_retailers()
        for retailer in retailers:
            self._view.dd_retailer.options.append(ft.dropdown.Option(text=retailer.retailer_name,
                                                                     data=retailer,
                                                                     on_click=self.read_retailer))
        self._view.update_page()

```
                                                                                                                             
- (nella view è così: ```       self.dd_retailer = ft.Dropdown(width=500,
                                   hint_text="Filtro per retailer",
                                   label="retailer",
                                   options=[ft.dropdown.Option(key="None",
                                                               text="Nessun filtro", data=None,
                                                               on_click=self._controller.read_retailer)]) ```)

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

