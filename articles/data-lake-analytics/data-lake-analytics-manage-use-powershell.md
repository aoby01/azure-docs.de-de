---
title: Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell | Microsoft Docs
description: "Erfahren Sie, wie Sie Data Lake Analytics-Aufträge, -Datenquellen, und -Benutzer verwalten. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 4dd1ba30101d364fa52738a4e1c3e07874c5ed1f
ms.contentlocale: de-de
ms.lasthandoff: 05/26/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Erfahren Sie, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mithilfe von Azure PowerShell verwalten. Klicken Sie oben auf die Auswahlregisterkarte, um Themen anzuzeigen, in denen die Verwaltung mit anderen Tools stattfindet.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/pricing/free-trial/)(in englischer Sprache).
* **Azure PowerShell**. Siehe den Abschnitt „Voraussetzungen“ unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).

## <a name="running-the-snippets"></a>Ausführen der Codeausschnitte

In den PowerShell-Codeausschnitten dieses Tutorials werden die folgenden Variablen zum Speichern dieser Informationen verwendet:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="manage-accounts"></a>Konten verwalten

### <a name="create-a-data-lake-analytics-account"></a>Erstellen eines Data Lake Analytics-Kontos

```
New-AzureRmResourceGroup -Name  $rg -Location $location
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="create-a-data-lake-analytics-account-using-a-template"></a>Erstellen eines Data Lake Analytics-Kontos mithilfe einer Vorlage

Sie können hierfür auch eine Azure-Ressourcengruppenvorlage verwenden. Eine Vorlage für das Erstellen eines Data Lake Analytics-Kontos und des zugehörigen Data Lake Store-Kontos finden Sie in [Anhang A](#appendix-a). Speichern Sie die Vorlage in eine Datei mit JSON-Vorlage, und verwenden Sie dann das folgende PowerShell-Skript, um sie aufzurufen:

    $AzureSubscriptionID = "<Your Azure Subscription ID>"

    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"

    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID

    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 


### <a name="list-accounts"></a>Auflisten von Konten

Auflisten von Data Lake Analytics-Konten im aktuellen Abonnement

    Get-AzureRmDataLakeAnalyticsAccount

Auflisten von Data Lake Analysekonten in einer bestimmten Ressourcengruppe

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Abrufen von Details eines bestimmten Data Lake Analytics-Kontos

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Prüfen auf Vorhandensein eines bestimmten Data Lake Analytics-Kontos

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Das Cmdlet gibt entweder **True** oder **False** zurück.

### <a name="delete-data-lake-analytics-accounts"></a>Löschen von Data Lake Analytics-Konten
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Durch das Löschen eines Data Lake Analytics-Kontos wird das zugehörige Data Lake-Speicherkonto nicht gelöscht. Im folgenden Beispiel werden das Data Lake Analytics-Konto und das Data Lake-Standardspeicherkonto gelöscht.

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Verwalten von Kontodatenquellen
Data Lake Analytics unterstützt derzeit die folgenden Datenquellen:

* [Azure Data Lake-Speicher](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage (in englischer Sprache)](../storage/storage-introduction.md)

Beim Erstellen eines Analytics-Kontos müssen Sie ein Azure Data Lake-Speicherkonto als Standardspeicherkonto festlegen. Das Data Lake-Standardspeicherkonto dient zum Speichern von Auftragsmetadaten und -überwachungsprotokollen. Nachdem Sie ein Analytics-Konto erstellt haben, können Sie zusätzliche Data Lake-Speicherkonten und/oder Azure-Speicherkonten hinzufügen. 

### <a name="find-the-default-data-lake-store-account"></a>Ermitteln des Data Lake-Standardspeicherkontos
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Hinzufügen zusätzlicher Azure Blob-Speicherkonten
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Hinzufügen zusätzlicher Data Lake-Speicherkonten
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>Auflisten von Datenquellen:
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts



<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Verwalten von Aufträgen
Für das Erstellen eines Auftrags ist ein Data Lake Analytics-Konto erforderlich.  Weitere Informationen finden Sie unter [Verwalten von Data Lake Analytics-Konten](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Auflisten von Aufträgen
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"    

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Abrufen von Auftragsdetails
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>

### <a name="submit-jobs"></a>Übermitteln von Aufträgen
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [!NOTE]
> Die Standardpriorität eines Auftrags ist 1000, und der Standardparallelitätsgrad eines Auftrag ist 1.
> 
> 

### <a name="cancel-jobs"></a>Abbrechen von Aufträgen
    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Verwalten von Katalogelemente
Der U-SQL-Katalog wird zum Strukturieren von Daten und Code verwendet, damit diese von U-SQL-Skripts gemeinsam genutzt werden können. Der Katalog ermöglicht die höchstmögliche Leistung mit Daten in Azure Data Lake. Weitere Informationen finden Sie unter [Verwenden des U-SQL-Katalogs](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Auflisten von Katalogelementen
    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database



    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

### <a name="get-catalog-item-details"></a>Abrufen von Details von Katalogelementen
    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

### <a name="test-existence-of--catalog-item"></a>Prüfen auf Vorhandensein eines Katalogelements
    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

## <a name="use-azure-resource-manager-groups"></a>Verwenden von Azure-Ressourcen-Manager-Gruppen
Anwendungen bestehen normalerweise aus vielen Komponenten, z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-overview.md). 

Ein Data Lake Analytics-Dienst kann folgende Komponenten enthalten:

* Azure Data Lake Analytics-Konto
* Erforderliches Azure Data Lake-Speicherkonto
* Zusätzliche Azure Data Lake-Speicherkonten
* Zusätzliche Azure-Speicherkonten

Alle diese Komponenten lassen sich zur einfacheren Verwaltung unter einer ARM-Gruppe erstellen.

![Azure Data Lake Analytics-Konto und -Speicher](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Ein Data Lake Analytics-Konto und die dazugehörigen Speicherkonten müssen sich im gleichen Azure-Rechenzentrum befinden.
Die ARM-Gruppe kann sich jedoch in einem anderen Rechenzentrum befinden.  

## <a name="see-also"></a>Siehe auch
* [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
* [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md)
* [Überwachen und Problembehandeln von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="appendix-a---data-lake-analytics-arm-template"></a>Anhang A – ARM-Vorlage für Data Lake Analytics
Die folgende ARM-Vorlage kann zum Bereitstellen eines Data Lake Analytics-Kontos und des zugehörigen Data Lake-Speicherkontos verwendet werden.  Speichern Sie die Vorlage als JSON-Datei, und rufen Sie dann die Vorlage mit dem PowerShell-Skript auf. Weitere Informationen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure Resource Manager-Vorlage](../azure-resource-manager/resource-group-template-deploy.md) und [Verfassen von Azure Resource Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md).

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }


