---
title: Entwickeln von Skriptaktionen mit Linux-basiertem HDInsight | Microsoft Docs
description: "Es wird beschrieben, wie Sie mit Skriptaktionen Linux-basierte HDInsight-Cluster anpassen können. Skriptaktionen stellen eine Möglichkeit zum Anpassen von Azure HDInsight-Clustern dar, indem Clusterkonfigurationseinstellungen angegeben oder zusätzliche Dienste, Tools oder andere Software auf dem Cluster installiert werden. "
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: aaa5134fafea387e63fc9c9819422d24de6baed0
ms.contentlocale: de-de
ms.lasthandoff: 05/18/2017


---
# <a name="script-action-development-with-hdinsight"></a>Entwickeln von Skriptaktionen mit HDInsight

Erfahren Sie, wie Sie Ihr HDInsight-Cluster mit Bash-Skripts anpassen können. Mit Skriptaktionen können Sie HDInsight während oder nach der Erstellung eines Clusters anpassen.

> [!IMPORTANT]
> Die Schritte in diesem Dokument erfordern einen HDInsight-Cluster mit Linux. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a name="what-are-script-actions"></a>Was sind Skriptaktionen?

Skriptaktionen sind Bash-Skripts, die Azure auf dem Clusterknoten ausführt, um Konfigurationsänderungen vorzunehmen oder Software zu installieren. Eine Skriptaktion wird als Stamm ausgeführt und bietet Vollzugriffsrechte auf die Clusterknoten.

Skriptaktionen können mithilfe der folgenden Methoden angewendet werden:

| Verwenden Sie diese Methode, um ein Skript anzuwenden... | Während der Clustererstellung... | In einem ausgeführten Cluster... |
| --- |:---:|:---:|
| Azure-Portal |✓  |✓ |
| Azure PowerShell |✓ |✓ |
| Azure-Befehlszeilenschnittstelle |&nbsp; |✓ |
| HDInsight .NET-SDK |✓ |✓ |
| Azure Resource Manager-Vorlage |✓ |&nbsp; |

Weitere Informationen zur Verwendung dieser Methoden zum Anwenden von Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Bewährte Methoden für die Entwicklung von Skripts

Wenn Sie ein benutzerdefiniertes Skript für einen HDInsight-Cluster entwickeln, sollten Sie mehrere bewährte Methoden beachten:

* [Auswählen der Hadoop-Version](#bPS1)
* [Auswählen der Betriebssystemversion](#bps10)
* [Einrichten stabiler Verknüpfungen mit Skriptressourcen](#bPS2)
* [Verwenden vorkompilierter Ressourcen](#bPS4)
* [Sicherstellen, dass das Clusteranpassungsskript idempotent ist](#bPS3)
* [Sicherstellen einer hohen Verfügbarkeit der Clusterarchitektur](#bPS5)
* [Konfigurieren benutzerdefinierter Komponenten zur Verwendung von Azure-Blobspeicher](#bPS6)
* [Schreiben von Informationen in STDOUT und STDERR](#bPS7)
* [Speichern von Dateien im ASCII-Format mit LF-Zeilenenden](#bps8)
* [Verwenden von Wiederholungsversuchlogik zum Wiederherstellen bei vorübergehenden Fehlern](#bps9)

> [!IMPORTANT]
> Skriptaktionen müssen innerhalb von 60 Minuten abgeschlossen sein. Andernfalls schlägt das Skript fehl. Während der Knotenbereitstellung wird das Skript gleichzeitig mit anderen Einrichtungs- und Konfigurationsprozessen ausgeführt. Der Wettbewerb um Ressourcen wie CPU-Zeit oder Netzwerkbandbreite kann dazu führen, dass es länger als in Ihrer Entwicklungsumgebung dauert, bis das Skript abgeschlossen ist.

### <a name="bPS1"></a>Auswählen der Hadoop-Version

In den verschiedenen HDInsight-Versionen sind unterschiedliche Versionen von Hadoop-Diensten und Hadoop-Komponenten installiert. Wenn bei Ihrem Skript eine bestimmte Version eines Diensts oder einer Komponente vorausgesetzt wird, sollten Sie das Skript nur mit der HDInsight-Version verwenden, die die erforderlichen Komponenten enthält. Informationen zu den in HDInsight enthaltenen Komponentenversionen finden Sie im Dokument [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen](hdinsight-component-versioning.md) .

### <a name="bps10"></a> Auswählen der Betriebssystemversion

Linux-basiertes HDInsight basiert auf der Ubuntu Linux-Distribution. Da unterschiedliche Versionen von HDInsight auf verschiedenen Versionen von Ubuntu basieren, können sich Auswirkungen auf das Skriptverhalten ergeben. Beispielsweise basiert HDInsight 3.4 und früher auf Ubuntu-Versionen, für die Upstart genutzt wird. Version 3.5 basiert auf Ubuntu 16.04 mit Verwendung von Systemd. Für Systemd und Upstart sind unterschiedliche Befehle erforderlich, und Ihr Skript sollte so geschrieben sein, dass es für beide Fälle funktioniert.

Ein weiterer wichtiger Unterschied zwischen HDInsight 3.4 und 3.5 besteht darin, dass `JAVA_HOME` jetzt auf Java 8 verweist.

Sie können `lsb_release` verwenden, um die Betriebssystemversion zu prüfen. Der folgende Code veranschaulicht, wie ermittelt werden kann, ob das Skript unter Ubuntu 14 oder 16 ausgeführt wird:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Sie finden das vollständige Skript, in dem diese Codeausschnitte enthalten sind, unter „https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh“.

Informationen zur Version von Ubuntu, die von HDInsight verwendet wird, finden Sie im Dokument [HDInsight-Komponentenversion](hdinsight-component-versioning.md).

Informationen zu den Unterschieden zwischen Systemd und Upstart finden Sie unter [Systemd for Upstart users](https://wiki.ubuntu.com/SystemdForUpstartUsers) (Systemd für Upstart-Benutzer).

### <a name="bPS2"></a>Einrichten stabiler Verknüpfungen mit Skriptressourcen

Das Skript und die damit verknüpften Ressourcen müssen während des gesamten Lebenszyklus des Clusters verfügbar sein. Diese Ressourcen werden benötigt, wenn während der Skalierungsvorgänge neue Knoten zum Cluster hinzugefügt werden.

Die bewährte Methode ist das Herunterladen und Archivieren aller Daten in einem Azure-Speicherkonto Ihres Abonnements.

> [!IMPORTANT]
> Bei dem verwendeten Speicherkonto muss es sich um das Standardspeicherkonto des Clusters oder um einen öffentlichen, schreibgeschützten Container eines anderen Speicherkontos handeln.

Die von Microsoft bereitgestellten Beispiele sind beispielsweise im Speicherkonto [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) gespeichert, einem öffentlichen, schreibgeschützten Container, der vom HDInsight-Team verwaltet wird.

### <a name="bPS4"></a>Verwenden vorkompilierter Ressourcen

Zur Verringerung des Zeitraums, der für die Ausführung des Skripts benötigt wird, sollten Sie Vorgänge vermeiden, mit denen Ressourcen aus dem Quellcode kompiliert werden. Kompilieren Sie die Ressourcen vor, und speichern Sie diese in Azure Blob Storage, damit sie schnell heruntergeladen werden können.

### <a name="bPS3"></a>Sicherstellen, dass das Clusteranpassungsskript idempotent ist

Skripts müssen idempotent sein. Wenn das Skript mehrfach ausgeführt wird, sollte es den Cluster jedes Mal in den gleichen Zustand zurückversetzen.

Ein Skript, dass Konfigurationsdateien modifiziert, sollte z.B. keine doppelten Einträge hinzufügen, wenn es mehrfach ausgeführt wird.

### <a name="bPS5"></a>Sicherstellen einer hohen Verfügbarkeit der Clusterarchitektur

Linux-basierte HDInsight-Cluster stellen zwei Hauptknoten bereit, die im Cluster aktiv sind. Skriptaktionen werden für beide Knoten ausgeführt. Wenn die von Ihnen installierten Komponenten nur einen Hauptknoten erwarten, installieren Sie die Komponenten nicht auf beiden Hauptknoten.

> [!IMPORTANT]
> Dienste, die im Rahmen von HDInsight bereitgestellt werden, wurden entwickelt, um bei Bedarf ein Failover zwischen den beiden Hauptknoten auszuführen. Diese Funktion gilt nicht für benutzerdefinierte Komponenten, die mit Skriptaktionen installiert wurden. Wenn Sie eine hohe Verfügbarkeit für benutzerdefinierte Komponenten benötigen, müssen Sie Ihren eigenen Failovermechanismus implementieren.

### <a name="bPS6"></a>Konfigurieren benutzerdefinierter Komponenten zur Verwendung von Azure-Blobspeicher

Die Komponenten, die Sie auf dem Cluster installieren, sind möglicherweise standardmäßig so konfiguriert, dass sie den HDFS-Speicher (Hadoop Distributed File System) verwenden. HDInsight verwendet als Standardspeicher entweder Azure Storage oder Azure Data Lake Store. Diese bieten ein mit HDFS kompatibles Dateisystem, das Daten auch dann beibehält, wenn der Cluster gelöscht wird. Möglicherweise müssen Sie die Komponenten, die Sie installieren, so konfigurieren, dass sie WASB oder ADL anstelle von HDFS verwenden.

Für die meisten Vorgänge müssen Sie das Dateisystem nicht angeben. Mit dem folgenden Code wird beispielsweise die Datei „giraph-examples.jar“ aus dem lokalen Dateisystem in den Clusterspeicher kopiert:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

In diesem Beispiel verwendet der Befehl `hdfs` auf transparente Weise den Standardclusterspeicher. Für manche Vorgänge müssen Sie möglicherweise den URI angeben. Z.B. `adl:///example/jars` für Data Lake Store und `wasb:///example/jars` für Azure Storage.

### <a name="bPS7"></a>Schreiben von Informationen in STDOUT und STDERR

HDInsight protokolliert Skriptausgaben, die in STDOUT oder STDERR geschrieben werden. Diese Informationen können Sie mit der Ambari-Webbenutzeroberfläche anzeigen.

> [!NOTE]
> Ambari ist nur dann verfügbar, wenn der Cluster erfolgreich erstellt wurde. Wenn Sie während der Clustererstellung eine Skriptaktion verwenden, und ein Fehler bei der Erstellung auftritt, finden Sie im Abschnitt zur Problembehandlung unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) andere Möglichkeiten, um auf protokollierte Informationen zuzugreifen.

Die meisten Dienstprogramme und Installationspakete schreiben bereits Informationen in STDOUT und STDERR. Möglicherweise möchten Sie jedoch weitere Protokollierungsinformationen hinzufügen. Verwenden Sie `echo`, um Text an STDOUT zu senden. Beispiel:

```bash
echo "Getting ready to install Foo"
```

Standardmäßig sendet `echo` die Zeichenfolge an STDOUT. Soll dieser an STDERR geleitet werden, setzen Sie `>&2` vor `echo`. Beispiel:

```bash
>&2 echo "An error occurred installing Foo"
```

So werden in STDOUT geschriebene Informationen stattdessen an STDERR (2) weitergeleitet. Weitere Informationen zur E/A-Umleitung finden Sie unter [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Weitere Informationen zum Anzeigen der durch Skriptaktionen protokollierten Daten finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Speichern von Dateien im ASCII-Format mit LF-Zeilenenden

Bash-Skripts sollten im ASCII-Format und mit LF als Zeilenende gespeichert werden. Dateien, die als UTF-8 gespeichert wurden, oder die CRLF als Zeilenende verwenden, schlagen möglicherweise mit folgendem Fehler fehl:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a>Verwenden von Wiederholungsversuchlogik zum Wiederherstellen bei vorübergehenden Fehlern

Beim Herunterladen von Dateien, die Pakete mit apt-get installieren, oder anderen Aktionen, die Daten über das Internet übertragen, können vorübergehende Netzwerkfehler die erfolgreiche Ausführung verhindern. Für die Remoteressource, mit der Sie kommunizieren, könnte z.B. gerade ein Failover zu einem Sicherungsknoten durchgeführt werden.

Damit Ihr Skript gegenüber vorübergehenden Fehlern stabil ist, können Sie die Wiederholungsversuchlogik implementieren. Die folgende Funktion veranschaulicht, wie Sie Wiederholungslogik implementieren können. Der Vorgang wird dreimal wiederholt, bevor er fehlschlägt.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Die folgenden Beispiele veranschaulichen, wie Sie diese Funktion verwenden können.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Hilfsmethoden für benutzerdefinierte Skripts

Hilfsmethoden für Skriptaktionen sind Hilfsprogramme, die Sie zum Schreiben von benutzerdefinierten Skripts verwenden können. Diese Methoden sind im Skript [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) enthalten. Verwenden Sie Folgendes, um sie herunterzuladen und als Teil Ihres Skripts zu verwenden:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Folgende Hilfsprogramme stehen für das Verwenden in Ihrem Skript zur Verfügung:

| Hilfsprogramm | Beschreibung |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Lädt eine Datei aus dem Quell-URI in den angegebenen Dateipfad herunter. Standardmäßig wird eine vorhandene Datei nicht überschrieben. |
| `untar_file TARFILE DESTDIR` |Extrahiert eine TAR-Datei (mit `-xf`) in das Zielverzeichnis |
| `test_is_headnode` |Bei Ausführung auf dem Hauptknoten eines Clusters wird „1“ zurückgegeben, andernfalls „0“. |
| `test_is_datanode` |Wenn der aktuelle Knoten ein Datenknoten (Workerknoten) ist, wird „1“ zurückgegeben, andernfalls „0“. |
| `test_is_first_datanode` |Wenn der aktuelle Knoten der erste Datenknoten (Workerknoten) ist (mit dem Namen „workernode0“), wird „1“ zurückgegeben, andernfalls „0“. |
| `get_headnodes` |Gibt den vollqualifizierten Domänennamen der Hauptknoten im Cluster zurück. Namen sind durch Kommas getrennt. Bei einem Fehler wird eine leere Zeichenfolge zurückgegeben. |
| `get_primary_headnode` |Ruft den vollqualifizierten Domänennamen des primären Hauptknotens ab. Bei einem Fehler wird eine leere Zeichenfolge zurückgegeben. |
| `get_secondary_headnode` |Ruft den vollqualifizierten Domänennamen des sekundären Hauptknotens ab. Bei einem Fehler wird eine leere Zeichenfolge zurückgegeben. |
| `get_primary_headnode_number` |Ruft das numerische Suffix des primären Hauptknotens ab. Bei einem Fehler wird eine leere Zeichenfolge zurückgegeben. |
| `get_secondary_headnode_number` |Ruft das numerische Suffix des sekundären Hauptknotens ab. Bei einem Fehler wird eine leere Zeichenfolge zurückgegeben. |

## <a name="commonusage"></a>Gängige Verwendungsmuster

Dieser Abschnitt enthält Anweisungen für das Implementieren einiger gängiger Verwendungsmuster, die Ihnen beim Schreiben Ihrer eigenen benutzerdefinierten Skripts begegnen können.

### <a name="passing-parameters-to-a-script"></a>Übergeben von Parametern an ein Skript

In einigen Fällen müssen Sie Parameter für das Skript angeben. Beispielsweise müssen Sie das Administratorkennwort für den Cluster angeben, wenn Sie die Ambari-REST-API verwenden.

Parameter, die an das Skript übergeben werden, werden als *Positionsparameter* bezeichnet und für den ersten Parameter `$1` zugewiesen, dann für den zweiten Parameter `$2` usw. `$0` enthält den Namen des Skripts.

Werte, die als Parameter an das Skript übergeben wurden, sollten mit einfache Anführungszeichen (‚‘) verwendet werden. So stellen Sie sicher, dass der übergebene Wert als Literal behandelt wird.

### <a name="setting-environment-variables"></a>Festlegen von Umgebungsvariablen

Eine Umgebungsvariable wird mit folgender Anweisung festgelegt:

    VARIABLENAME=value

Dabei ist VARIABLENNAME der Name der Variable. Verwenden Sie `$VARIABLENAME`, um auf die Variable zuzugreifen. Verwenden Sie beispielsweise folgende Anweisung, um einen Wert, der von einem Positionsparameter bereitgestellt wird, als Umgebungsvariable mit dem Namen „PASSWORD“ zuzuweisen:

    PASSWORD=$1

Für den anschließenden Zugriff auf die Informationen kann dann `$PASSWORD` verwendet werden.

Umgebungsvariablen, die im Skript festgelegt werden, gelten nur innerhalb des Gültigkeitsbereichs des Skripts. In einigen Fällen müssen Sie möglicherweise systemweite Umgebungsvariablen hinzufügen, die nach Abschluss des Skripts beibehalten werden. Fügen Sie die Variable in `/etc/environment` ein, um systemweite Umgebungsvariablen hinzuzufügen. Die folgende Anweisung fügt beispielsweise `HADOOP_CONF_DIR` hinzu:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Zugriff auf Speicherorte benutzerdefinierter Skripts

Skripts zum Anpassen eines Clusters müssen an einem der folgenden Speicherorte gespeichert werden:

* Ein __Azure-Speicherkonto__, das dem Cluster zugeordnet ist.

* __Zusätzliches Speicherkonto__, das dem Cluster zugeordnet ist

* Ein __öffentlich lesbarer URI__ Beispielsweise eine Daten-URL, die in OneDrive, Dropbox oder in einem anderen Dateihostingdienst gespeichert ist

* __Azure Data Lake Store__-Konto, das dem HDInsight-Cluster zugeordnet ist Weitere Informationen zum Verwenden von Azure Data Lake Store mit HDInsight finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > Der Dienstprinzipal, der von HDInsight zum Zugreifen auf Data Lake Store genutzt wird, muss über Lesezugriff auf das Skript verfügen.

Vom Skript verwendete Ressourcen müssen ebenfalls öffentlich verfügbar sein.

Durch das Speichern von Dateien in einem Azure-Speicherkonto oder in Azure Data Lake Store erhalten Sie schnellen Zugriff, da sich beide innerhalb des Azure-Netzwerks befinden.

> [!NOTE]
> Das URI-Format zum Verweisen auf das Skript unterscheidet sich je nach dem verwendeten Dienst. Verwenden Sie für Speicherkonten, die dem HDInsight-Cluster zugeordnet sind, `wasb://` oder `wasbs://`. Verwenden Sie für öffentlich lesbare URIs `http://` oder `https://`. Verwenden Sie für Data Lake Store `adl://`.

### <a name="checking-the-operating-system-version"></a>Überprüfen der Betriebssystemversion

Unterschiedliche Versionen von HDInsight basieren auf bestimmten Versionen von Ubuntu. Unter Umständen bestehen Unterschiede zwischen den Betriebssystemversionen, die Sie für Ihr Skript überprüfen sollten. Beispielweise müssen Sie ggf. eine Binärdatei installieren, die an die Version von Ubuntu gebunden ist.

Verwenden Sie `lsb_release`, um die Betriebssystemversion zu überprüfen. Das folgende Skript veranschaulicht beispielsweise, wie Sie je nach Betriebssystemversion auf eine spezifische TAR-Datei verweisen:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Prüfliste für die Bereitstellung einer Skriptaktion

Es folgen unsere Schritte bei der Vorbereitung der Bereitstellung dieser Skripts:

* Legen Sie Daten mit den benutzerdefinierten Skripts an einem Speicherort ab, auf den die Clusterknoten während der Bereitstellung zugreifen können. Z.B. der Standardspeicher für den Cluster Dateien können außerdem in öffentlich lesbaren Hostingdiensten gespeichert werden.
* Vergewissern Sie sich, dass das Skript idempotent ist. Damit kann das Skript mehrfach auf demselben Knoten ausgeführt werden.
* Legen Sie die heruntergeladenen von den Skripts verwendeten Dateien in einem temporären Dateiverzeichnis ab (z. B. "/tmp"), und löschen Sie sie nach der Ausführung der Skripts.
* Wenn die Einstellungen auf Betriebssystemebene oder Dienstkonfigurationsdateien geändert werden, sollten Sie HDInsight-Dienste neu starten.

## <a name="runScriptAction"></a>Ausführen einer Skriptaktion

Sie können Skriptaktionen verwenden, um HDInsight-Cluster mit der folgenden Methode anzupassen:

* Azure-Portal
* Azure PowerShell
* Azure-Ressourcen-Manager-Vorlagen
* Das HDInsight .NET SDK

Weitere Informationen zur Verwendung jeder Methode finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Beispiele benutzerdefinierter Skripts

Microsoft bietet Beispielskripts für die Installation von Komponenten in einem HDInsight-Cluster. Unter folgenden Links finden Sie weitere Beispiele für Skriptaktionen.

* [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md)
* [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md)
* [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md)
* [Installieren oder Aktualisieren von Mono in HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Problembehandlung

Bei der Verwendung der von Ihnen entwickelten Skripts können die folgenden Fehler auftreten:

**Fehler**: `$'\r': command not found`. Manchmal gefolgt von `syntax error: unexpected end of file`.

*Ursache*: Dieser Fehler tritt auf, wenn die Zeilen in einem Skript mit CR-LF enden. In UNIX-Systemen wird als Zeilenende nur LF erwartet.

Dieses Problem tritt am häufigsten auf, wenn das Skript in einer Windows-Umgebung erstellt wird, da CR-LF in vielen Text-Editoren unter Windows als gängiges Zeilenende verwendet wird.

*Lösung*: Wenn Ihr Text-Editor über die entsprechende Option verfügt, wählen Sie UNIX als Format oder LF für das Zeilenende aus. In einem UNIX-System können Sie außerdem die folgenden Befehle verwenden, um CR-LF in LF zu ändern:

> [!NOTE]
> Mit den folgenden annähernd gleichwertigen Befehlen sollten sich die CR-LF-Zeilenenden in LF ändern lassen. Wählen Sie einen Befehl entsprechend den in Ihrem System verfügbaren Dienstprogrammen aus.

| Befehl | Hinweise |
| --- | --- |
| `unix2dos -b INFILE` |Die ursprüngliche Datei wird mit einer BAK-Erweiterung gesichert |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE enthält eine Version, die ausschließlich LF-Zeilenenden umfasst |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifiziert die Datei direkt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE enthält eine Version, die ausschließlich LF-Zeilenenden umfasst. |

**Fehler**: `line 1: #!/usr/bin/env: No such file or directory`.

*Ursache*: Dieser Fehler tritt auf, wenn das Skript im Format UTF-8 mit einer Bytereihenfolge-Marke (BOM) gespeichert wurde.

*Lösung*: Speichern Sie die Datei entweder im ASCII-Format oder im UTF-8-Format ohne Bytereihenfolge-Marke. In einem Linux- oder UNIX-System können Sie auch mit dem folgenden Befehl eine Datei ohne Bytereihenfolge-Marke (BOM) erstellen:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Ersetzen Sie `INFILE` durch die Datei ohne BOM. Für `OUTFILE` sollte ein neuer Dateiname eingegeben werden. Die Datei enthält dann das Skript ohne BOM.

## <a name="seeAlso"></a>Nächste Schritte

* Lernen Sie das [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md)
* Verwenden Sie die [HDInsight .NET SDK-Referenz](https://msdn.microsoft.com/library/mt271028.aspx) , um mehr über das Erstellen von .NET-Anwendungen zu erfahren, die HDInsight verwalten.
* Verwenden Sie die [HDInsight-REST-API](https://msdn.microsoft.com/library/azure/mt622197.aspx) , um zu erfahren, wie Sie REST verwenden, um Verwaltungsaktionen auf HDInsight-Clustern auszuführen.

