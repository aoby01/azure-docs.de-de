---
title: Erste Schritte mit Azure Data Lake Analytics mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft Docs
description: 'Erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle zum Erstellen eines Data Lake Analytics-Kontos, zum Erstellen eines Data Lake Analytics-Auftrags mit U-SQL und zum Senden des Auftrags verwenden. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eeaa5641a4bcea0e8b46e85e40ff5b92113446c1
ms.contentlocale: de-de
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Lernprogramm: Erste Schritte mit Azure Data Lake Analytics mithilfe der Azure-Befehlszeilenschnittstelle (CLI)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Enthält Informationen zum Verwenden der Azure-CLI zum Erstellen von Azure Data Lake Analytics-Konten für das Definieren von Data Lake Analytics-Aufträgen in [U-SQL](data-lake-analytics-u-sql-get-started.md)und das Übermitteln von Aufträgen an Data Lake Analytics-Konten. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

In diesem Tutorial entwickeln Sie einen Auftrag, bei dem eine Datei mit tabulatorgetrennten Werten (TSV) gelesen und in eine Datei mit kommagetrennten Werten (CSV) konvertiert wird. Um das gleiche Lernprogramm unter Verwendung anderer unterstützter Tools zu durchlaufen, klicken Sie auf die Registerkarten oben in diesem Abschnitt.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Azure-Befehlszeilenschnittstelle**. Weitere Informationen finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../cli-install-nodejs.md).
  * Laden Sie für diese Demo die **Vorabversion** [Azure Command-line Tools (CLI) for Data Lake](https://github.com/MicrosoftBigData/AzureDataLake/releases) herunter, und installieren Sie sie.
* **Authentifizierung**. Verwenden Sie den folgenden Befehl:

        azure login
    Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](../xplat-cli-connect.md).
* **Wechseln Sie in den Azure-Ressourcen-Manager-Modus**, indem Sie den folgenden Befehl ausführen:

        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>Erstellen eines Data Lake Analytics-Kontos
Zum Ausführen von Aufträgen ist ein Data Lake Analytics-Konto erforderlich. Zum Erstellen eines Data Lake Analytics-Kontos müssen Sie Folgendes angeben:

* **Azure-Ressourcengruppe**: Es muss ein Data Lake Analytics-Konto in einer Azure-Ressourcengruppe erstellt werden. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ermöglicht es Ihnen, mit den Ressourcen in Ihrer Anwendung als Gruppe zu arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen.  

    So listen Sie die Ressourcengruppen in Ihrem Abonnement auf:

        azure group list

    So erstellen Sie eine neue Ressourcengruppe:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Name des Data Lake Analytics-Kontos**
* **Standort**: eines der Azure-Rechenzentren, die Data Lake Analytics unterstützen.
* **Data Lake-Standardkonto**: Jedes Data Lake Analytics-Konto verfügt über ein Data Lake-Standardkonto.

    So listen Sie das vorhandene Data Lake-Konto auf

        azure datalake store account list

    So erstellen Sie ein neues Data Lake-Konto

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

  > [!NOTE]
  > Der Data Lake-Kontoname darf nur Kleinbuchstaben und Zahlen enthalten.
  >
  >

**So erstellen Sie ein Data Lake Analytics-Konto**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Data Lake Analytics-Beispielkonto](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> Der Data Lake Analytics-Kontoname darf nur Kleinbuchstaben und Zahlen enthalten.
>
>

## <a name="upload-data-to-data-lake-store"></a>Hochladen von Daten in den Data Lake-Speicher
In diesem Tutorial verarbeiten Sie einige Suchprotokolle.  Das Suchprotokoll kann entweder in einem Data Lake-Speicher oder einem Azure-BLOB-Speicher gespeichert werden.

Das Azure-Portal enthält eine Benutzeroberfläche zum Kopieren einiger Beispieldatendateien in das Data Lake-Standardkonto. Hierzu gehört auch eine Suchprotokolldatei. Weitere Informationen zum Hochladen von Daten in das Data Lake-Standardspeicherkonto finden Sie unter [Vorbereiten von Quelldaten](data-lake-analytics-get-started-portal.md).

Zum Hochladen von Dateien über die Befehlszeilenschnittstelle verwenden Sie den folgenden Befehl:

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics hat auch Zugriff auf den Azure-Blob-Speicher.  Informationen zum Hochladen von Daten nach Azure Blob Storage finden Sie unter [Verwenden der Azure-CLI mit Azure Storage](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Übermitteln von Data Lake Analytics-Aufträgen
Die Data Lake Analytics-Aufträge werden in der Sprache U-SQL geschrieben. Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit der Sprache U-SQL](data-lake-analytics-u-sql-get-started.md) und in der [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348) (in englischer Sprache).

**So erstellen Sie ein Skript für Data Lake Analytics-Aufträge**

* Erstellen Sie mit dem folgenden U-SQL-Skript eine Textdatei, und speichern Sie die Textdatei auf der Arbeitsstation:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Mit diesem U-SQL-Skript wird die Quelldatei mithilfe von **Extractors.Tsv()** gelesen, und anschließend wird eine CSV-Datei mithilfe von **Outputters.Csv()** erstellt.

    Ändern Sie die beiden Pfade nur, wenn Sie die Quelldatei an einen anderen Speicherort kopieren.  Data Lake Analytics erstellt den Ausgabeordner, falls er nicht vorhanden ist.

    Es ist einfacher, für Dateien, die unter Data Lake-Standardkonten gespeichert sind, relative Pfade zu verwenden. Sie können aber auch absolute Pfade verwenden.  Beispiel:

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Sie müssen absolute Pfade verwenden, um auf Dateien in verknüpften Speicherkonten zuzugreifen.  Die Syntax für Dateien, die unter dem verknüpften Azure-Speicherkonto gespeichert werden, lautet wie folgt:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Azure-BLOB-Container mit öffentlichen Blobs oder Zugriffsberechtigungen für öffentliche Container werden derzeit nicht unterstützt.      
  >
  >

**So übermitteln Sie den Auftrag**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


Mit den folgenden Befehlen können Aufträge aufgelistet, Auftragsdetails abgerufen und Aufträge abgebrochen werden:

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

Nachdem der Auftrag abgeschlossen wurde, können Sie die folgenden Cmdlets verwenden, um die Datei anzugeben und herunterzuladen:

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Siehe auch
* Wenn Sie dasselbe Tutorial mit anderen Tools verwenden möchten, klicken Sie oben auf der Seite auf die Registerkartenauswahl.
* Eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Informationen zu den ersten Schritten der Entwicklung von U-SQL-Anwendungen finden Sie unter [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Informationen zum Erlernen von U-SQL finden Sie unter [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informationen zu Verwaltungsaufgaben finden Sie unter [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md).
* Eine Übersicht über Data Lake Analytics finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).

