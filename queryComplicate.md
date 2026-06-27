# Esempi query complicate
1. Due nodi sono connessi se è solo se tra di loro esiste una interazione (tabella interaction). Il peso dell’arco è dato dalla somma di tutti i cromosomi (colonna Chromosome della tabella genes)
associati ai GeneID dei due vertici (nodi Classificazioni). Nella somma, bisogna considerare i cromosomi distinti (trucco cercare di avere un'unica colonna con tutti i cromosomi untilizzando una codizione or)
### soluzione: 
```
SELECT c1.GeneID as Gene1, c2.GeneID as Gene2, SUM(DISTINCT g.Chromosome) as W
FROM interactions i, classification c1, classification c2, genes g
WHERE c1.Localization=%s  and c2.Localization=c1.Localization 
and i.GeneID1 =c1.GeneID and i.GeneID2 =c2.GeneID and c1.GeneID<>c2.GeneID 
AND (g.GeneID=c1.GeneID or g.GeneID=c2.GeneID)
group by c1.GeneID, c2.GeneID
order by W asc
```
2. Un arco collega due ordini se sono stati ordinati a una distanza temporale di massimo K giorni (valore K incluso). L'arco va sempre dall'ordine effettuato in data precedente verso l'ordine effettuato in data successiva. Il numero massimo di giorni sarà impostato dall’utente tramite l’apposito TextField K. Il peso dell’arco è calcolato come (numero_totale_oggetti_nei_due_ordini) / (giorni_tra_ordini). Questo peso rappresenta un'intensità di correlazione normalizzata: archi tra ordini temporalmente vicini hanno peso maggiore rispetto ad archi tra ordini distanti nel tempo:
```
SELECT o1.order_id as o1, o2.order_id as o2, ((sum(oi1.quantity)+sum(oi2.quantity)) / DATEDIFF(o2.order_date, o1.order_date )) as peso
                FROM orders o1, orders o2, order_items oi1, order_items oi2  
                WHERE o1.order_id = oi1.order_id and o2.order_id=oi2.order_id 
                and o1.order_date <o2.order_date and o1.store_id = o2.store_id and o1.store_id = %s
                and DATEDIFF(o2.order_date, o1.order_date ) <= %s
                group by o1.order_id, o2.order_id
                order by peso desc
```
