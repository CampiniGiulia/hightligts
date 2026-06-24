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
