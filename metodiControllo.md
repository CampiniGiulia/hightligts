# Controlli più comuni
### Convertire una stringa in un numero:
```
        if self._view._txtInK.value is "": # oppure is None per le tendine inizializzo nell'init
            self._view.txt_result.controls.append(
                ft.Text(f"Inserire un valore per il cammino", color="red"))
            self._view.update_page()
            return
        try:
            kInt = int(self._view._txtInK.value)
        except ValueError:
            self._view.txt_result.controls.append(
                ft.Text(f"Inserire un valore numerico per il cammino", color="red"))
            self._view.update_page()
            return
```
### Controllare che il grafo sia costruito:
```
        if self.grafo == False:
            self._view.txt_result.controls.clear()
            self._view.txt_result.controls.append(
                ft.Text(f"Costruire il grafo prima di usare questo metodo", color="red"))
            self._view.update_page()
            return
```
### Controllare selezione tendina, in particolare con gli anni:
```
        if self._annoInizio is None:
            self._view.txt_result.controls.clear()
            self._view.txt_result.controls.append(ft.Text(f"Selezionare un anno di inizio", color = "red"))
            self._view.update_page()
            return
        if self._annoFine is None:
            self._view.txt_result.controls.clear()
            self._view.txt_result.controls.append(ft.Text(f"Selezionare un anno di fine", color = "red"))
            self._view.update_page()
            return
        if self._annoInizio > self._annoFine:
            self._view.txt_result.controls.clear()
            self._view.txt_result.controls.append(ft.Text(f"Selezionare un anno di inizio minore di quello di fine", color = "red"))
            self._view.update_page()
            return
```
