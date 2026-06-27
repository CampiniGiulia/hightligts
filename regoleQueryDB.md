# TIPI DI JOIN
- Inner join: Restituisce le righe solo se c'è una corrispondenza esatta in entrambe le tabelle. Se un record di una tabella non trova il corrispettivo nell'altra, viene completamente eliminato dal risultato.
- Left join: Restituisce tutte le righe della tabella di sinistra (quella scritta per prima nel FROM), insieme alle righe corrispondenti della tabella di destra. Se a destra non c'è corrispondenza, SQL riempie le colonne mancanti con dei valori NULL.
- Union: Unisce il risultato di due query eliminando i duplicati.
- Union all: Unisce il risultato di due query senza eliminare i duplicati.
- INTERSECT: Restituisce solo le righe che compaiono in entrambi i risultati delle query.
- EXCEPT (o MINUS): Restituisce le righe della prima query che non sono presenti nella seconda query.
- Il costrutto CASE WHEN: per calcolare un peso o una proprietà condizionata in base al valore di un campo. Permette di inserire una logica condizionale direttamente nella SELECT.
  ```
  SELECT InvoiceId, Total,
         CASE 
             WHEN Total > 10.00 THEN 'BIG'
             ELSE 'SMALL'
         END AS CategoriaFattura
  FROM Invoice
  ```
  - COALESCE(): restituisce il primo valore non-NULL che incontra nella lista.
  - YEAR(date): Estrae solo l'anno
  - MONTH(date): Estrae il mese numerico (da 1 a 12).
  - DATEDIFF(date1, date2): Calcola la differenza in giorni tra due date.
  - InvoiceDate >= '2010-01-01': Puoi usare i normali operatori di confronto (>, <, >=, <=) direttamente sulle date. Se il campo è datetime imposta automaticamente l'ora a 00, meglio usare date() per il confronto del limite superiore e inferiore cos' elimina completamente l'ora.
