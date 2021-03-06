---
title: "Verwenden von U-SQL-Fensterfunktionen für Azure Data Lake Analytics-Aufträge | Microsoft-Dokumentation"
description: 'Erfahren Sie, wie Sie U-SQL-Fensterfunktionen verwenden. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: a5e14b32-d5eb-4f4b-9258-e257359f9988
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data`
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 55d19a00198f1943a8196d31399c617397b4e5d2
ms.contentlocale: de-de
ms.lasthandoff: 05/05/2017


---
# <a name="using-u-sql-window-functions-for-azure-data-lake-analytics-jobs"></a>Verwenden von U-SQL-Funktionen für Azure Data Lake Analytics-Aufträge
Fensterfunktionen wurden 2003 in den ISO/ANSI SQL-Standard eingeführt. U-SQL übernimmt eine Teilmenge der Fensterfunktionen gemäß der Definition durch den ANSI SQL-Standard.

Fensterfunktionen werden verwendet, um Berechnungen in Gruppen von Zeilen durchzuführen, die *Fenster*genannt werden. Fenster werden von der OVER-Klausel definiert. Fensterfunktionen bieten für verschiedene wichtige Szenarien Lösungen auf überaus effiziente Weise.

Die Fensterfunktionen sind in die folgenden Kategorien unterteilt: 

* [Berichtsaggregationsfunktionen](#reporting-aggregation-functions), z.B. SUM oder AVG
* [Rangfolgefunktionen](#ranking-functions), z.B. DENSE_RANK, ROW_NUMBER, NTILE und RANK
* [Analysefunktionen](#analytic-functions), z.B. Summenverteilung oder Perzentile, greifen ohne Verwendung einer Selbstverknüpfung auf Daten in einer vorherigen Zeile (im gleichen Resultset) zu

## <a name="sample-datasets"></a>Beispieldatasets
In diesem Tutorial werden zwei Datasets verwendet:

### <a name="the-querylog-sample-dataset"></a>Das Beispieldataset „QueryLog“
  
„QueryLog“ enthält eine Übersicht, wonach Benutzer in der Suchmaschine gesucht haben. Jedes Abfrageprotokoll enthält Folgendes:
  
* Query: Die Suche des Benutzers
* Latency: Zeit, bis der Benutzer die Antwort erhalten hat, in Millisekunden
* Vertical: Die Art von Inhalt, die den Benutzer interessierte (Weblinks, Bilder, Videos)  
 
```
@querylog = 
    SELECT * FROM ( VALUES
        ("Banana"  , 300, "Image" ),
        ("Cherry"  , 300, "Image" ),
        ("Durian"  , 500, "Image" ),
        ("Apple"   , 100, "Web"   ),
        ("Fig"     , 200, "Web"   ),
        ("Papaya"  , 200, "Web"   ),
        ("Avocado" , 300, "Web"   ),
        ("Cherry"  , 400, "Web"   ),
        ("Durian"  , 500, "Web"   ) )
    AS T(Query,Latency,Vertical);
```

## <a name="the-employees-sample-dataset"></a>Das Beispieldataset „Employees“
  
Das Dataset „Employees“ enthält die folgenden Felder:
  
* EmpID: Mitarbeiter-ID
* EmpName: Mitarbeitername
* DeptName: Abteilungsname 
* DeptID: Abteilungs-ID
* Salary: Mitarbeitergehalt

```
@employees = 
    SELECT * FROM ( VALUES
        (1, "Noah",   "Engineering", 100, 10000),
        (2, "Sophia", "Engineering", 100, 20000),
        (3, "Liam",   "Engineering", 100, 30000),
        (4, "Emma",   "HR",          200, 10000),
        (5, "Jacob",  "HR",          200, 10000),
        (6, "Olivia", "HR",          200, 10000),
        (7, "Mason",  "Executive",   300, 50000),
        (8, "Ava",    "Marketing",   400, 15000),
        (9, "Ethan",  "Marketing",   400, 10000) )
    AS T(EmpID, EmpName, DeptName, DeptID, Salary);
```  

## <a name="compare-window-functions-to-grouping"></a>Vergleichen von Fensterfunktionen mit einer Gruppierung
Fensterfunktionen und Gruppierung sind konzeptionell verwandt. Es empfiehlt sich, diese Beziehung zu verstehen.

### <a name="use-aggregation-and-grouping"></a>Verwenden von Aggregation und Gruppierung
Die folgende Abfrage verwendet eine Aggregation, um die Summe der Gehälter aller Mitarbeiter zu berechnen:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

Das Ergebnis ist eine einzelne Zeile mit einer einzelnen Spalte. $165000 ist die Summe der Gehaltswerte in der gesamten Tabelle. 

| TotalSalary |
| --- |
| 165000 |


Die folgende Anweisung verwendet die GROUP BY-Klausel, um die gesamten Gehaltskosten für jede Abteilung zu berechnen:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Die Ergebnisse sind wie folgt:

| DeptName | SalaryByDept |
| --- | --- |
| Entwicklung |60000 |
| HR |30000 |
| Geschäftsleitung |50000 |
| Marketing |25000 |

Die Summe der Spalte „SalaryByDept“ ist $165000, was dem Betrag im letzten Skript entspricht.

In beiden Fällen gibt es weniger Ausgabezeilen als Eingabezeilen:

* Ohne GROUP BY reduziert die Aggregation alle Zeilen zu einer einzelnen Zeile. 
* Mit GROUP BY gibt es N Ausgabezeilen, wobei N die Anzahl unterschiedliche Werte ist, die in den Daten enthalten sind.  In diesem Fall werden vier Zeilen ausgegeben.

### <a name="use-a-window-function"></a>Verwenden einer Fensterfunktion
Die OVER-Klausel im folgenden Beispiel ist leer, sodass im Fenster alle Zeilen angezeigt werden. SUM wird in diesem Beispiel auf die OVER-Klausel angewendet, die vorausgeht.

Sie können diese Abfrage so lesen: „Die Summe der Gehälter über ein Fenster aller Zeilen“.

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

Im Gegensatz zu GROUP BY ist die Anzahl von Aus- und Eingabezeilen identisch: 

| EmpName | TotalAllDepts |
| --- | --- |
| Noah |165000 |
| Sophia |165000 |
| Liam |165000 |
| Emma |165000 |
| Jacob |165000 |
| Olivia |165000 |
| Mason |165000 |
| Ava |165000 |
| Ethan |165000 |

Der Wert 165000 (die Summe aller Gehälter) wird in jeder Ausgabezeile platziert. Die Summe stammt aus dem „Fenster“ aller Zeilen, weshalb sie alle Gehälter einschließt. 

Das nächste Beispiel veranschaulicht das Optimieren des „Fensters“, um alle Mitarbeiter, die Abteilung und die Gesamtgehaltskosten der Abteilung aufzulisten. PARTITION BY wird der OVER-Klausel hinzugefügt.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Die Ergebnisse sind wie folgt:

| EmpName | DeptName | SalaryByDep |
| --- | --- | --- |
| Noah |Entwicklung |60000 |
| Sophia |Entwicklung |60000 |
| Liam |Entwicklung |60000 |
| Mason |Geschäftsleitung |50000 |
| Emma |HR |30000 |
| Jacob |HR |30000 |
| Olivia |HR |30000 |
| Ava |Marketing |25000 |
| Ethan |Marketing |25000 |

Wiederum entspricht die Anzahl der Eingabezeilen der Anzahl der Ausgabezeilen. Jede Zeile enthält das Gesamtgehalt der jeweiligen Abteilung.

## <a name="reporting-aggregation-functions"></a>Berichtsaggregationsfunktionen
Fensterfunktionen unterstützen auch die folgenden Aggregate:

* COUNT
* SUM
* MIN
* MAX
* DURCHSCHN.
* STDEV
* VAR

Die Syntax ist:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Hinweis: 

* Standardmäßig werden NULL-Werte von allen Aggregatfunktionen außer COUNT ignoriert.
* Wenn Aggregatfunktionen zusammen mit der OVER-Klausel angegeben werden, ist die ORDER BY-Klausel in der OVER-Klausel nicht zulässig.

### <a name="use-sum"></a>Verwenden von SUM
Das folgende Beispiel fügt das Gesamtgehalt nach Abteilung den einzelnen Eingabezeilen hinzu:

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Hier sehen Sie die Ausgabe:

| EmpID | EmpName | DeptName | DeptID | Salary | TotalByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Entwicklung |100 |10000 |60000 |
| 2 |Sophia |Entwicklung |100 |20000 |60000 |
| 3 |Liam |Entwicklung |100 |30000 |60000 |
| 7 |Mason |Geschäftsleitung |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |30000 |
| 5 |Jacob |HR |200 |10000 |30000 |
| 6 |Olivia |HR |200 |10000 |30000 |
| 8 |Ava |Marketing |400 |15000 |25000 |
| 9 |Ethan |Marketing |400 |10000 |25000 |

### <a name="use-count"></a>Verwenden von COUNT
Das folgende Beispiel fügt jeder Zeile ein zusätzliches Feld hinzu, um die Gesamtanzahl von Mitarbeitern in jeder Abteilung anzuzeigen.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Das Ergebnis:

| EmpID | EmpName | DeptName | DeptID | Salary | CountByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Entwicklung |100 |10000 |3 |
| 2 |Sophia |Entwicklung |100 |20000 |3 |
| 3 |Liam |Entwicklung |100 |30000 |3 |
| 7 |Mason |Geschäftsleitung |300 |50000 |1 |
| 4 |Emma |HR |200 |10000 |3 |
| 5 |Jacob |HR |200 |10000 |3 |
| 6 |Olivia |HR |200 |10000 |3 |
| 8 |Ava |Marketing |400 |15000 |2 |
| 9 |Ethan |Marketing |400 |10000 |2 |

### <a name="use-min-and-max"></a>Verwenden von MIN und MAX
Das folgende Beispiel fügt jeder Zeile ein zusätzliches Feld hinzu, um das niedrigste Gehalt in jeder Abteilung anzuzeigen:

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Die Ergebnisse:

| EmpID | EmpName | DeptName | DeptID | Salary | MinSalary |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Entwicklung |100 |10000 |10000 |
| 2 |Sophia |Entwicklung |100 |20000 |10000 |
| 3 |Liam |Entwicklung |100 |30000 |10000 |
| 7 |Mason |Geschäftsleitung |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |10000 |
| 5 |Jacob |HR |200 |10000 |10000 |
| 6 |Olivia |HR |200 |10000 |10000 |
| 8 |Ava |Marketing |400 |15000 |10000 |
| 9 |Ethan |Marketing |400 |10000 |10000 |

## <a name="ranking-functions"></a>Rangfolgefunktionen
Rangfolgefunktionen geben gemäß der Definition der Klauseln PARTITION BY und OVER für jede Zeile in jeder Partition einen Rangfolgewert (LONG) zurück. Die Reihenfolge der Ränge wird über ORDER BY in der OVER-Klausel gesteuert.

Die folgenden Rangfolgefunktionen werden unterstützt:

* RANK
* DENSE_RANK 
* NTILE
* ROW_NUMBER

**Syntax:**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

* Die ORDER BY-Klausel ist für Rangfolgefunktionen optional. Wenn die ORDER BY-Klausel nicht angegeben wird, weist U-SQL Werte auf Grundlage der Reihenfolge zu, in der die Datensätze gelesen werden, was zu nicht deterministischen Werten für ROW_NUMBER, RANK oder DENSE_RANK führt.
* NTILE erfordert einen Ausdruck, der in eine positive ganze Zahl ausgewertet wird. Diese Zahl gibt die Anzahl der Gruppen an, in die jede Partition unterteilt werden muss. Dieser Bezeichner wird nur bei der Rangfolgefunktion NTILE verwendet. 

Weitere Informationen zur OVER-Klausel finden Sie in der [U-SQL-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=691348).

ROW_NUMBER, RANK und DENSE_RANK weisen Zeilen in einem Fenster Werte zu. Anstatt diese Funktionen separat zu behandeln, empfiehlt es sich zu prüfen, wie sie auf die gleiche Eingabe reagieren.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

Beachten Sie, dass die OVER-Klauseln identisch sind. Das Ergebnis:

| Abfrage | Latency: INT | Vertical | RowNumber | RANK | DenseRank |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Image |1 |1 |1 |
| Cherry |300 |Image |2 |1 |1 |
| Durian |500 |Image |3 |3 |2 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |3 |2 |2 |
| Fig |300 |Web |4 |4 |3 |
| Cherry |400 |Web |5 |5 |4 |
| Durian |500 |Web |6 |6 |5 |

### <a name="rownumber"></a>ROW_NUMBER
In jedem Fenster (Vertical, entweder „Image“ oder „Web“) wird der Zeilenwert nach „Latency“ sortiert um 1 erhöht.  

![U-SQL-Fensterfunktion ROW_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### <a name="rank"></a>RANK
Im Unterschied zu ROW_NUMBER() verwendet RANK() den Wert von „Latency“, der in der ORDER BY-Klausel für das Fenster angegeben ist.

RANK beginnt mit (1, 1, 3), da die beiden ersten Werte für „Latency“ identisch sind. Dann ist der nächste Wert 3, da der „Latency“-Wert in 500 geändert wurde. Wichtig ist der Hinweis, dass obwohl doppelte Werte denselben Rang erhalten, der RANK-Wert zum nächsten ROW_NUMBER-Wert springt. Dieses Muster wird mit der Sequenz (2, 2, 4) für (Vertical, Web) wiederholt.

![U-SQL-Fensterfunktion RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### <a name="denserank"></a>DENSE_RANK
DENSE_RANK entspricht RANK, außer dass nicht zum nächsten ROW_NUMBER-Wert gesprungen wird. DENSE_RANK geht zur nächsten Zahl in der Sequenz. Sehen Sie sich die Sequenzen (1, 1, 2) und (2, 2, 3) im Beispiel an.

![U-SQL-Fensterfunktion DENSE_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### <a name="remarks"></a>Anmerkungen
* Wird ORDER BY nicht angegeben, wird die Rangfolgefunktion ohne jegliche Reihenfolge auf das Rowset angewendet, was zu nicht deterministischem Verhalten führt.
* Die folgenden Bedingungen müssen erfüllt sein, um zu gewährleisten, dass die von einer Abfrage mit ROW_NUMBER zurückgegebenen Zeilen bei jeder Ausführung exakt gleich sortiert werden.
  
  * Die Werte der partitionierten Spalte sind eindeutig.
  * Die Werte der ORDER BY-Spalten sind eindeutig.
  * Kombinationen von Werten in der Partitionsspalte und den ORDER BY-Spalten sind eindeutig.

### <a name="ntile"></a>NTILE
Mit NTILE werden die Zeilen in einer sortierten Partition auf eine angegebene Anzahl von Gruppen verteilt. Die Gruppen sind beginnend mit 1 nummeriert. 

Im folgenden Beispiel wird die Menge der Zeilen in jeder Partition (Vertical) in vier Gruppen geteilt, nach Latenz sortiert, und die Gruppennummer für jede Zeile wird zurückgegeben. 

(Image, Vertical) enthält drei Zeilen, verfügt also über drei Gruppen. 

(Web, Vertical) enthält sechs Zeilen.  Die zwei zusätzlichen Zeilen werden auf die ersten beiden Gruppen verteilt. Damit gibt es zwei Zeilen in Gruppe 1 und Gruppe 2 und nur eine Zeile in Gruppe 3 und Gruppe 4.  

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

Die Ergebnisse:

| Abfragen | Latenz | Vertical | Quartile |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |2 |
| Durian |500 |Image |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |1 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |2 |
| Cherry |400 |Web |3 |
| Durian |500 |Web |4 |

NTILE verwendet den „numgroups“-Parameter. „numgroups“ ist eine positive ganze Zahl (int) oder ein Konstantenausdruck vom Typ „long“, der die Anzahl der Gruppen angibt, in die jede Partition unterteilt werden muss. 

* Wenn die Anzahl der Zeilen in der Partition von „numgroups“ gleichmäßig geteilt werden kann, haben die Gruppen dieselbe Größe. 
* Wenn die Anzahl der Zeilen in einer Partition nicht von „numgroups“ geteilt werden kann, haben die Gruppen eine leicht unterschiedliche Größe. Größere Gruppen stehen in der von der OVER-Klausel angegebenen Reihenfolge vor kleineren Gruppen. 

Beispiel:

    100 rows divided into 4 groups: 
    [ 25, 25, 25, 25 ]

    102 rows divided into 4 groups: 
    [ 26, 26, 25, 25 ]

### <a name="top-n-records-per-partition-via-rank-denserank-or-rownumber"></a>Top N Datensätze pro Partition über RANK, DENSE_RANK oder ROW_NUMBER
Viele Benutzer möchten nur die oberste n Zeilen pro Gruppe auswählen. Dies ist mit dem herkömmlichen GROUP BY nicht möglich. 

Sie haben am Anfang des Abschnitts zu Rangfolgefunktionen das folgende Beispiel gesehen. Es zeigt nicht die TOP N Datensätze für jede Partition:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Die Ergebnisse:

| Abfragen | Latenz | Vertical | RANK | DenseRank | RowNumber |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Image |1 |1 |1 |
| Cherry |300 |Image |1 |1 |2 |
| Durian |500 |Image |3 |2 |3 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |2 |2 |3 |
| Fig |300 |Web |4 |3 |4 |
| Cherry |400 |Web |5 |4 |5 |
| Durian |500 |Web |6 |5 |6 |

### <a name="top-n-with-dense-rank"></a>TOP N mit DENSE RANK
Im folgenden Beispiel werden die obersten drei Datensätze in jeder Gruppe ohne Lücken in der sequenziellen Rangnummerierung von Zeilen in jeder Partition zurückgegeben.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Die Ergebnisse:

| Abfragen | Latenz | Vertical | DenseRank |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |1 |
| Durian |500 |Image |2 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |3 |

### <a name="top-n-with-rank"></a>TOP N mit RANK
    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Die Ergebnisse:    

| Abfragen | Latenz | Vertical | RANK |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |1 |
| Durian |500 |Image |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |

### <a name="top-n-with-rownumber"></a>TOP N mit ROW_NUMBER
    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Die Ergebnisse:   

| Abfragen | Latenz | Vertical | RowNumber |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |2 |
| Durian |500 |Image |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |3 |

### <a name="assign-globally-unique-row-number"></a>Zuweisen einer global eindeutigen Zeilennummer
Es ist häufig nützlich, jeder Zeile eine global eindeutige Nummer zuzuweisen. Rangfolgefunktionen sind einfacher und effizienter als die Verwendung eines Reducers.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## <a name="analytic-functions"></a>Analysefunktionen
Analysefunktionen dienen zum Verstehen der Verteilung von Werten in Fenstern. Das häufigste Szenario für die Verwendung von Analysefunktionen ist die Berechnung von Perzentilen.

**Unterstützte Analysefunktionen für Fenster**

* CUME_DIST 
* PERCENT_RANK
* PERCENTILE_CONT
* PERCENTILE_DISC

### <a name="cumedist"></a>CUME_DIST

CUME_DIST berechnet die relative Position eines angegebenen Werts in einer Gruppe von Werten. Berechnet wird den Prozentsatz der Abfragen mit einer Latenz kleiner gleich der aktuellen Abfragelatenz im selben „Vertical“. 

CUME_DIST für eine Zeile R ist bei angenommener aufsteigender Reihenfolge die Anzahl von Zeilen mit Werten kleiner gleich dem Wert von R dividiert durch die Anzahl von Zeilen, die im Resultset der Partition ausgewertet werden. 

CUME_DIST gibt Zahlen im Bereich 0 < x < = 1 zurück.

**Syntax:**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

Im folgenden Beispiel wird die CUME_DIST-Funktion zum Berechnen des Latenzperzentils für jede Abfrage in einem „Vertical“ verwendet. 

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Die Ergebnisse:

| Abfragen | Latenz | Vertical | CumeDist |
| --- | --- | --- | --- |
| Durian |500 |Image |1 |
| Banana |300 |Image |0.666666666666667 |
| Cherry |300 |Image |0.666666666666667 |
| Durian |500 |Web |1 |
| Cherry |400 |Web |0.833333333333333 |
| Fig |300 |Web |0.666666666666667 |
| Fig |200 |Web |0,5 |
| Papaya |200 |Web |0,5 |
| Apple |100 |Web |0.166666666666667 |

Es gibt in der Partition sechs Zeilen mit dem Partitionsschlüssel „Web“

* Es gibt sechs Zeilen mit einem Wert kleiner gleich 500, sodass der CUME_DIST-Wert 6/6=1 entspricht.
* Es gibt fünf Zeilen mit einem Wert kleiner gleich 400, sodass der CUME_DIST-Wert 5/6=0,83 entspricht.
* Es gibt vier Zeilen mit einem Wert kleiner gleich 300, sodass der CUME_DIST-Wert 4/6=0,66 entspricht.
* Es gibt drei Zeilen mit einem Wert kleiner gleich 200, sodass der CUME_DIST-Wert 3/6=0,5 entspricht. Es gibt zwei Zeilen mit demselben Latenzwert.
* Es gibt eine Zeile mit einem Wert kleiner gleich 100, sodass der CUME_DIST-Wert 1/6=0,16 entspricht. 

**Hinweise zur Verwendung:**

* Gleichwertige Werte werden stets in denselben kumulativen Verteilungswert ausgewertet.
* NULL-Werte werden als die niedrigsten möglichen Werte behandelt.
* Eine ORDER BY-Klausel ist erforderlich, um CUME_DIST zu berechnen.
* CUME_DIST ist vergleichbar mit der PERCENT_RANK-Funktion.

Hinweis: Wenn auf die SELECT-Anweisung nicht OUTPUT folgt, ist die ORDER BY-Klausel nicht zulässig.

### <a name="percentrank"></a>PERCENT_RANK
PERCENT_RANK berechnet den relativen Rang einer Zeile innerhalb einer Gruppe von Zeilen. Mit PERCENT_RANK können Sie den relativen Rang eines Werts innerhalb eines Resultsets oder einer Partition ermitteln. Der von PERCENT_RANK zurückgegebene Wertebereich ist größer als 0 und kleiner gleich 1. Im Gegensatz zu CUME_DIST ist PERCENT_RANK für die erste Zeile immer 0.

**.Syntax:**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Hinweise**

* Die erste Zeile in einer Menge hat den PERCENT_RANK 0.
* NULL-Werte werden als die niedrigsten möglichen Werte behandelt.
* PERCENT_RANK erfordert eine ORDER BY-Klausel.
* CUME_DIST ist vergleichbar mit der PERCENT_RANK-Funktion. 

Im folgenden Beispiel wird die PERCENT_RANK-Funktion zum Berechnen des Latenzperzentils für jede Abfrage in einem „Vertical“ verwendet. 

Die PARTITION BY-Klausel wird angegeben, um die Zeilen im Resultset gemäß dem „Vertical“ zu partitionieren. Die ORDER BY-Klausel in der OVER-Klausel sortiert die Zeilen in jeder Partition. 

Der von der PERCENT_RANK-Funktion zurückgegebene Wert stellt den Rang der Latenz der Abfragen in einem „Vertical“ als Prozentsatz dar. 

    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Die Ergebnisse:

| Abfrage | Latency: INT | Vertical | PercentRank |
| --- | --- | --- | --- |
| Banana |300 |Image |0 |
| Cherry |300 |Image |0 |
| Durian |500 |Image |1 |
| Apple |100 |Web |0 |
| Fig |200 |Web |0,2 |
| Papaya |200 |Web |0,2 |
| Fig |300 |Web |0,6 |
| Cherry |400 |Web |0,8 |
| Durian |500 |Web |1 |

### <a name="percentilecont--percentiledisc"></a>PERCENTILE_CONT und PERCENTILE_DISC
Diese beiden Funktionen berechnen ein Perzentil basierend auf einer kontinuierlichen oder diskreten Verteilung der Spaltenwerte.

**Syntax:**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal**: das zu berechnende Perzentil. Der Wert muss im Bereich von 0,0 bis 1,0 liegen.

    WITHIN GROUP (ORDER BY <identifier> [ ASC | DESC ])

Gibt eine Liste von numerischen Werten für die Sortierung und Berechnung des Perzentils an. Nur ein einziger Spaltenbezeichner ist zulässig. Der Ausdruck muss in einen numerischen Datentyp ausgewertet werden. Andere Datentypen sind nicht zulässig. Die Standardsortierreihenfolge ist aufsteigend.

    OVER ([ PARTITION BY <identifier,>…[n] ] )

Teilt das Eingaberowset in Partitionen gemäß dem Partitionsschlüssel auf, für den die Perzentilfunktion gilt. Weitere Informationen finden Sie im Abschnitt RANGFOLGE dieses Dokuments.
Hinweis: Alle NULL-Werte im Dataset werden ignoriert.

**PERCENTILE_CONT** berechnet ein Perzentil basierend auf einer kontinuierlichen Verteilung der Spaltenwerte. Das Ergebnis wird interpoliert und entspricht ggf. keinem der spezifischen Werte in der Spalte. 

**PERCENTILE_DISC** berechnet ein Perzentil basierend auf einer diskreten Verteilung der Spaltenwerte. Das Ergebnis ist gleich einem bestimmten Wert in der Spalte. Mit anderen Worten gibt PERCENTILE_DISC im Gegensatz zu PERCENTILE_CONT stets einen tatsächlichen (ursprünglichen Eingabe-) Wert zurück.

Die Funktionsweise beider Funktionen zeigt das folgende Beispiel, bei dem der Medianwert (Perzentil=0,5) für „Latency“ in jedem „Vertical“ gesucht wird.

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 

        FROM @querylog;

Die Ergebnisse:

| Abfrage | Latency: INT | Vertical | PercentileCont50 | PercentilDisc50 |
| --- | --- | --- | --- | --- |
| Banana |300 |Image |300 |300 |
| Cherry |300 |Image |300 |300 |
| Durian |500 |Image |300 |300 |
| Apple |100 |Web |250 |200 |
| Fig |200 |Web |250 |200 |
| Papaya |200 |Web |250 |200 |
| Fig |300 |Web |250 |200 |
| Cherry |400 |Web |250 |200 |
| Durian |500 |Web |250 |200 |

Da die Werte für PERCENTILE_CONT interpoliert werden können, ist der Median für „Web“ 250, obwohl keine Abfrage in diesem „Vertical“ die Latenz 250 hatte. 

PERCENTILE_DISC interpoliert keine Werte, weshalb der Median für „Web“ 200 ist und so tatsächlich einem Wert in den Eingabezeilen entspricht.

## <a name="see-also"></a>Weitere Informationen
* [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Verwenden interaktiver Lernprogramme zu Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
* [Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md)



