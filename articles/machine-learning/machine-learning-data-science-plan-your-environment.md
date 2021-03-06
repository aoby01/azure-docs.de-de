---
title: "Identifizieren von Szenarien und Planen des Analyseprozesses – Azure | Microsoft-Dokumentation"
description: "Planen Sie die erweiterte Analyse unter Berücksichtigung verschiedener Kernfragen."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a712baabf2674bda0a53de63e7c204b08ba9d105
ms.lasthandoff: 04/27/2017


---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Bestimmen von Szenarien und Planen der Datenverarbeitung für die erweiterte Analyse
Welche Ressourcen sollten Sie einplanen, wenn Sie eine Umgebung für die Verarbeitung erweiterter Analysen für ein Dataset einrichten? In diesem Artikel werden verschiedene zu stellende Fragen vorgeschlagen, mit deren Hilfe Sie die für Ihr Szenario relevanten Aufgaben und Ressourcen bestimmen können. Die Reihenfolge der allgemeinen Schritte für Predictive Analytics werden unter [Was ist der Team Data Science-Prozess (TDSP)?](data-science-process-overview.md)vorgestellt. Jeder dieser Schritte erfordert bestimmte Ressourcen für die Aufgaben, die für Ihr spezielles Szenario relevant sind. Die wichtigsten Fragen zum Bestimmen Ihres Szenarios betreffen die Datenlogistik, Merkmale, Qualität der Datasets sowie die Tools und Sprachen, die für die Analyse verwendet werden sollen.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistische Fragen: Datenspeicherorte und -verschiebung
Die logistischen Fragen betreffen den Speicherort der **Datenquelle**, das **Ziel** in Azure und Anforderungen für das Verschieben der Daten, einschließlich Zeitplan, Menge und Ressourcenaufwand. Die Daten müssen möglicherweise während des Analyseprozesses mehrere Male verschoben werden. Ein gängiges Szenario ist das Verschieben lokaler Daten in eine Form von Speicher in Azure und anschließend in Machine Learning Studio.

1. **Was ist Ihre Datenquelle?** Ist sie lokal oder in der Cloud? Beispiel:
   
   * Die Daten sind öffentlich über eine HTTP-Adresse verfügbar.
   * Die Daten befinden sich an einem lokalen/Netzwerkspeicherort.
   * Die Daten befinden sich in einer SQL Server-Datenbank.
   * Die Daten sind in einem Azure-Speichercontainer gespeichert.
2. **Was ist das Azure-Ziel?** Wo muss es sich für Verarbeitungs- oder Modellierungszwecke befinden? Beispiel:
   
   * Azure Blob Storage
   * SQL Azure-Datenbanken
   * SQL Server auf Azure VM
   * HDInsight (Hadoop in Azure) oder Hive-Tabellen
   * Azure Machine Learning
   * Bereitstellbare virtuelle Azure-Datenträger
3. **Wie möchten Sie die Daten verschieben?** In den folgenden Themen werden die Verfahren und Ressourcen für das Erfassen und Laden von Daten in einer Vielzahl anderer Speicher und Verarbeitungsumgebungen beschrieben.
   
   * [Laden von Daten in Speicherumgebungen für Analysen](machine-learning-data-science-ingest-data.md)
   * [Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio](machine-learning-data-science-import-data.md)
4. **Müssen die Daten nach einem regelmäßigen Zeitplan verschoben oder während der Migration geändert werden?** Sie sollten die Verwendung von Azure Data Factory (ADF) in Betracht ziehen, wenn Daten insbesondere in einem Hybridszenario kontinuierlich migriert werden müssen, das sowohl auf lokale als auch Cloudressourcen zugreift. Gleiches gilt, wenn die Daten Transaktionen unterworfen werden oder geändert werden müssen, oder wenn ihnen im Rahmen der Migration eine Geschäftslogik hinzugefügt wird. Weitere Informationen finden Sie unter [Verschieben von Daten von einem lokalen SQL Server zu SQL Azure mithilfe von Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md).
5. **Wie viele Daten werden in Azure verschoben?** Sehr große Datasets können die Speicherkapazität bestimmter Umgebungen überschreiten. Ein Beispiel finden Sie in der Erörterung von Größenbeschränkungen für Machine Learning Studio im nächsten Abschnitt. In solchen Fällen kann eine Stichprobe der Daten während der Analyse verwendet werden. Details zum Erstellen von Stichproben in verschiedenen Azure-Umgebungen finden Sie unter [Stichprobendaten im Team Data Science-Prozess](machine-learning-data-science-sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Fragen zu Datenmerkmalen: Typ, Format und Größe
Diese Fragen sind wichtig für die Planung Ihrer Speicher- und Verarbeitungsumgebungen, die jeweils für verschiedene Datentypen geeignet sind und für die jeweils bestimmte Einschränkungen gelten.

1. **Was sind die Datentypen?** Beispiel:
   
   * Numerisch
   * Kategorisch
   * Zeichenfolgen
   * Binär
2. **Wie sind Ihre Daten formatiert?** Beispiel:
   
   * Durch Trennzeichen getrennte (CSV) oder durch Tabulator getrennten (TSV) Flatfiles
   * Komprimierte oder nicht komprimierte
   * Azure-Blobs
   * Hadoop Hive-Tabellen
   * SQL Server-Tabellen
3. **Wie umfangreich sind Ihre Daten?**
   
   * Klein: Weniger als 2 GB
   * Mittel: Mehr als 2 GB, aber weniger als 10 GB
   * Groß: Mehr als 10 GB

Nehmen wir beispielsweise die Azure Machine Learning Studio-Umgebung:

* Eine Liste der Datenformate und -typen, die von Azure Machine Learning Studio unterstützt werden, finden Sie im Abschnitt [Unterstützte Datenformate und Datentypen](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) .
* Informationen zu Datenbeschränkungen für Azure Machine Learning Studio finden Sie unter **Importieren und Exportieren von Daten für Machine Learning** im Abschnitt [Wie groß können Datasets für meine Module sein?](machine-learning-faq.md#machine-learning-studio-questions)

Informationen zu den Einschränkungen anderer Azure-Dienste, die im Analyseprozess verwendet werden, finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Fragen zur Datenqualität: Untersuchung und Vorverarbeitung
1. **Was wissen Sie über Ihre Daten?** Untersuchen Sie Daten, wenn Sie ihre grundlegenden Merkmale verstehen müssen: Welche Muster oder Trends sind erkennbar, welche Ausreißer sind vorhanden und wie viele Werte fehlen? Dieser Schritt ist wichtig, um das Ausmaß der erforderlichen Vorverarbeitung zu bestimmen, um Hypothesen zu formulieren, die auf die geeignetsten Features bzw. den geeignetsten Analysetyp hindeuten, oder um Pläne für eine zusätzliche Datensammlung zu definieren. Die Berechnung beschreibender Statistiken und Zeichnung von Visualisierungen sind nützliche Techniken für die Datenuntersuchung. Details zum Untersuchen eines Datasets in verschiedenen Azure-Umgebungen finden Sie unter [Durchsuchen von Daten im Team Data Science-Prozess](machine-learning-data-science-explore-data.md).
2. **Erfordern die Daten eine Vorverarbeitung oder Bereinigung?**
   Die Vorverarbeitung und Bereinigung von Daten ist eine wichtige Aufgabe, die vor dem effektiven Verwenden eines Datasets für Machine Learning durchgeführt werden muss. Unformatierte Daten enthalten oft unnötige bzw. fehlende Werte und sind unzuverlässig. Die Verwendung dieser Daten für die Modellierung kann zu falschen Ergebnissen führen. Eine Beschreibung finden Sie unter [Aufgaben zur Vorbereitung von Daten für erweitertes Machine Learning](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Fragen zu Tools und Sprachen
Je nachdem, welche Sprachen und Entwicklungsumgebungen bzw. Tools Sie brauchen oder am liebsten nutzen, gibt es hier viele Optionen.

1. **Welche Sprachen bevorzugen Sie für die Analyse?**  
   
   * R
   * Python
   * SQL
2. **Welche Tools sollen Sie für die Datenanalyse verwendet werden?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) : Eine Skriptsprache zum Verwalten von Azure-Ressourcen.
   * [Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools für Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter-Notebooks](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Bestimmen des Szenarios für die erweiterte Analyse
Nachdem Sie die Fragen im vorherigen Abschnitt beantwortet haben, können Sie bestimmen, welches Szenario für Ihren Fall am besten geeignet ist. Die Beispielszenarien werden unter [Szenarien für die erweiterte Analyse in Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md)erläutert.


