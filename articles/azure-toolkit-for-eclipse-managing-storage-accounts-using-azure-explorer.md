---
title: "Verwalten von Speicherkonten mithilfe von Azure-Explorer für Eclipse | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie Ihre Azure Storage-Konten mit Azure-Explorer für Eclipse verwalten."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 5b3014b5aca368be8ea46863c83665abde10fed5
ms.contentlocale: de-de
ms.lasthandoff: 05/08/2017


---

# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Verwalten von Speicherkonten mithilfe von Azure-Explorer für Eclipse

Der Azure-Explorer gehört zum Azure-Toolkit für Eclipse und bietet Java-Entwicklern eine einfach zu bedienende Lösung zum Verwalten von Speicherkonten in ihrem Azure-Konto innerhalb der integrierten Entwicklungsumgebung (IDE) von Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-storage-account-in-eclipse"></a>Erstellen eines Speicherkontos in Eclipse

Gehen Sie folgendermaßen vor, um ein Speicherkonto mit dem Azure-Explorer zu erstellen:

1. Melden Sie sich beim Azure-Konto gemäß den Anweisungen in [Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse] an.

2. Erweitern Sie in der Ansicht des **Azure-Explorers** den Knoten **Azure**. Klicken Sie mit der rechten Maustaste auf **Speicherkonten**, und klicken Sie dann auf **Speicherkonto erstellen**.

   ![Befehl „Speicherkonto erstellen“][CS01]

3. Geben Sie im Dialogfeld **Speicherkonto erstellen** folgende Details an:

   ![Dialogfeld „Neues Speicherkonto erstellen“][CS02]

   * **Name:** Geben Sie den Namen des neuen Speicherkontos an.

   * **Abonnement:** Geben Sie das Azure-Abonnement an, das Sie für das neue Speicherkonto verwenden möchten.

   * **Ressourcengruppe:** Geben Sie die Ressourcengruppe für Ihren virtuellen Computer an. Wählen Sie eine der folgenden Optionen:
      * **Create New**: Gibt an, dass Sie eine neue Ressourcengruppe erstellen möchten.
      * **Vorhandene verwenden:** Geben Sie an, dass Sie in einer Liste von Ressourcengruppen, die Ihrem Azure-Konto zugeordnet sind, eine Auswahl treffen möchten.

   * **Standort:** Geben Sie den Standort an, an dem Ihr Speicherkonto erstellt wird, z.B. „USA, Westen“.

   * **Kontoart:** Geben Sie den Typ des zu erstellenden Speicherkontos an (z.B. „Blob-Speicher“). Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten].

   * **Leistung:** Geben Sie an, welches Speicherkontoangebot vom ausgewählten Herausgeber verwendet werden soll (z.B. „Premium“). Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage].

   * **Replikation**: Gibt die Replikation des Speicherkontos an (z.B. „zonenredundant“). Weitere Informationen finden Sie unter [Azure-Speicherreplikation].

4. Nachdem Sie alle vorhergehenden Optionen angegeben haben, klicken Sie auf **Erstellen**.

## <a name="create-a-storage-container-in-eclipse"></a>Erstellen eines Speichercontainers in Eclipse

Gehen Sie folgendermaßen vor, um einen Speichercontainer mit dem Azure-Explorer zu erstellen:

1. Klicken Sie in der **Azure-Explorer**-Ansicht mit der rechten Maustaste auf das Speicherkonto, in dem Sie einen Container erstellen möchten, und klicken Sie dann auf **Blobcontainer erstellen**.

   ![Befehl „Blobcontainer erstellen“][CC01]

2. Geben Sie im Dialogfeld **Blobcontainer erstellen** den Namen für Ihren Container ein, und klicken Sie dann auf **OK**. Weitere Informationen zum Benennen von Speichercontainern finden Sie unter [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese].

   ![Dialogfeld „Blobcontainer erstellen“][CC02]

## <a name="delete-a-storage-container-in-eclipse"></a>Löschen eines Speichercontainers in Eclipse

Gehen Sie folgendermaßen vor, um einen Speichercontainer mit dem Azure-Explorer zu löschen:

1. Klicken Sie in der **Azure-Explorer**-Ansicht mit der rechten Maustaste auf den Speichercontainer, und klicken Sie dann auf **Löschen**.

   ![Befehl „Speichercontainer löschen“][DC01]

2. Klicken Sie im Bestätigungsfenster auf **OK**.

   ![Bestätigungsfenster für „Speichercontainer löschen“][DC02]

## <a name="delete-a-storage-account-in-eclipse"></a>Löschen eines Speicherkontos in Eclipse

Gehen Sie folgendermaßen vor, um ein Speicherkonto mit dem Azure-Explorer zu löschen:

1. Klicken Sie in der **Azure-Explorers**-Ansicht mit der rechten Maustaste auf das Speicherkonto, und klicken Sie dann auf **Löschen**.

   ![Befehl „Speicherkonto löschen“][DS01]

2. Klicken Sie im Bestätigungsfenster auf **OK**.

   ![Bestätigungsfenster für „Speicherkonto löschen“][DS02]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Storage-Konten sowie Größen und Preisen finden Sie in den folgenden Ressourcen:

* [Einführung in Microsoft Azure Storage]
* [Informationen zu Azure-Speicherkonten]
* Größen für Azure Storage-Konten
  * [Größen für Windows-Speicherkonten in Azure]
  * [Größen für Linux-Speicherkonten in Azure]
* Preise von Azure Storage-Konten
  * [Preise von Windows-Speicherkonten]
  * [Preise von Linux-Speicherkonten]

Weitere Informationen zu Azure-Toolkits für Java-IDEs finden Sie in den folgenden Ressourcen:

* [Azure-Toolkit für Eclipse]
  * [Neuerungen im Azure-Toolkit für Eclipse]
  * [Installation des Azure Toolkit für Eclipse]
  * [Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse]
  * [Erstellen einer „Hello World“-Web-App für Azure in Eclipse]
* [Azure Toolkit für IntelliJ]
  * [Neuerungen im Azure-Toolkit für IntelliJ]
  * [Installieren des Azure Toolkit für IntelliJ]
  * [Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ]
  * [Erstellen einer „Hello World“-Web-App für Azure in IntelliJ]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center] und in den [Java-Tools für Visual Studio Team Services].

<!-- URL List -->

[Azure-Toolkit für Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit für IntelliJ]: ./azure-toolkit-for-intellij.md
[Erstellen einer „Hello World“-Web-App für Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Erstellen einer „Hello World“-Web-App für Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation des Azure Toolkit für Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installieren des Azure Toolkit für IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Neuerungen im Azure-Toolkit für Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Neuerungen im Azure-Toolkit für IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Java-Tools für Visual Studio Team Services]: https://java.visualstudio.com/

[Einführung in Microsoft Azure Storage]: /azure/storage/storage-introduction
[Informationen zu Azure-Speicherkonten]: /azure/storage/storage-create-storage-account
[Azure-Speicherreplikation]: /azure/storage/storage-redundancy
[Skalierbarkeits- und Leistungsziele für Azure Storage]: /azure/storage/storage-scalability-targets
[Benennen von Containern, Blobs und Metadaten und Verweisen auf diese]: http://go.microsoft.com/fwlink/?LinkId=255555

[Größen für Windows-Speicherkonten in Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Größen für Linux-Speicherkonten in Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Preise von Windows-Speicherkonten]: /pricing/details/virtual-machines/windows/
[Preise von Linux-Speicherkonten]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png

