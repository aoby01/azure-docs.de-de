---
title: "Azure Automation – Hybrid Runbook Worker | Microsoft Docs"
description: "Der vorliegende Artikel stellt Informationen zum Installieren und Verwendung der Azure Automation-Funktion &quot;Hybrid-Runbook-Worker&quot; bereit, mit der Sie Runbooks auf Computern in Ihrem lokalen Datencenter ausführen können."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a63778c300a3d215a2a0025824f6363e1d9e9675
ms.contentlocale: de-de
ms.lasthandoff: 05/11/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Automatisieren von Ressourcen im Rechenzentrum mit Hybrid Runbook Worker
Runbooks in Azure Automation können nicht auf Ressourcen in Ihrem lokalen Datencenter zugreifen, da sie in der Azure-Cloud ausgeführt werden.  Die Funktion „Hybrid Runbook Worker“ von Azure Automation ermöglicht Ihnen das Ausführen von Runbooks auf Computern in Ihrem Rechenzentrum, um lokale Ressourcen verwalten zu können. Die Runbooks werden in Azure Automation gespeichert und verwaltet und an einen oder mehrere lokale Computer übermittelt.  

Diese Funktionalität wird in der folgenden Abbildung veranschaulicht:<br>  

![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/automation.png)

Sie können einen oder mehrere Computer in Ihrem Datencenter als Hybrid-Runbook-Worker einsetzen und Runbooks aus Azure Automation starten.  Jeder Worker erfordert den Microsoft-Verwaltungs-Agent mit einer Verbindung zu Microsoft Operations Management Suite und der Azure Automation-Runbookumgebung.  Operations Management Suite wird nur zum Installieren und Warten des Verwaltungs-Agents und zum Überwachen der Workerfunktionalität eingesetzt.  Die Übermittlung von Runbooks und die Anweisung zu ihrer Ausführung erfolgt durch Azure Automation.

Es gelten keine eingehenden Firewallanforderungen zur Unterstützung von Hybrid-Runbook-Workern. Der Agent auf dem lokalen Computer initiiert die gesamte Kommunikation mit Azure Automation in der Cloud. Wenn ein Runbook gestartet wird, erstellt Azure Automation eine Anweisung, die vom Agent abgerufen wird. Der Agent ruft anschließend das Runbook und alle Parameter über einen Pullvorgang ab.  Außerdem werden alle im Runbook verwendeten [Ressourcen](http://msdn.microsoft.com/library/dn939988.aspx) aus Azure Automation abgerufen.

> [!NOTE]
> Um die Konfiguration der Server zu verwalten, die die Hybrid Runbook Worker-Rolle mit Desired State Configuration (DSC) unterstützen, müssen Sie die Server als DSC-Knoten hinzufügen.  Weitere Informationen zu ihrer Integration für die Verwaltung durch DSC finden Sie unter [Integrieren von Computern für die Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).           
><br>
>Wenn Sie die [Lösung für die Updateverwaltung](../operations-management-suite/oms-solution-update-management.md) aktivieren, werden alle mit dem OMS-Arbeitsbereich verbundenen Windows-Computer automatisch als Hybrid Runbook Worker konfiguriert, um Runbooks zu unterstützen, die in dieser Lösung enthalten sind.  Es wird aber keine Registrierung für Hybrid Worker-Gruppen durchgeführt, die in Ihrem Automation-Konto bereits definiert wurden.  Der Computer kann einer Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzugefügt werden, um Automation-Runbooks zu unterstützen, solange Sie sowohl für die Lösung als auch die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Konto verwenden.  Diese Funktionalität wurde Version 7.2.12024.0 des Hybrid Runbook Worker hinzugefügt.  

## <a name="hybrid-runbook-worker-groups"></a>Hybrid-Runbook-Workergruppen
Jeder Hybrid-Runbook-Worker ist ein Mitglied einer Hybrid-Runbook-Workergruppe, die Sie beim Installieren des Agents angeben.  Eine Gruppe kann einen einzelnen Agent umfassen, aber für eine höhere Verfügbarkeit können Sie mehrere Agents installieren.

Wenn Sie ein Runbook auf einen Hybrid Runbook Worker starten, geben Sie die Gruppe an, in der das Runbook ausgeführt werden soll.  Die Mitglieder der Gruppe legen fest, welcher Worker die Anforderung verarbeitet.  Sie können keinen bestimmten Worker angeben.

## <a name="hybrid-runbook-worker-requirements"></a>Anforderungen von Hybrid Runbook Worker
Sie müssen mindestens einen lokalen Computer zum Ausführen von Hybrid Runbook-Aufträgen festlegen.  Dieser Computer muss Folgendes aufweisen:

* Windows Server 2012 oder höher
* Windows PowerShell 4.0 oder höher.  Aufgrund der höheren Zuverlässigkeit wird die Installation von Windows PowerShell 5.0 auf dem Computer empfohlen. Sie können die neueste Version aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=50395) herunterladen.
* Mindestens zwei Kerne und 4 GB RAM

Beachten Sie die folgenden Empfehlungen für Hybridworker:

* Legen Sie für hohe Verfügbarkeit in jeder Gruppe mehrere Hybrid-Worker fest.  
* Hybridworker können mit Service Management Automation- oder System Center Orchestrator-Runbookservern parallel betrieben werden.
* Es empfiehlt sich, einen Computer zu verwenden, der sich physisch in der Nähe oder in der Region des Automation-Kontos befindet, da die Auftragsdaten bei Abschluss eines Auftrags an Azure Automation zurückgesendet werden.

### <a name="configure-proxy-and-firewall-settings"></a>Konfigurieren von Proxy- und Firewalleinstellungen
Damit der lokale Hybrid Runbook Worker eine Verbindung mit dem Microsoft Operations Management Suite (OMS)-Dienst herstellen und sich bei dem Dienst registrieren kann, benötigt er Zugriff auf die unten angegebene Portnummer und die angegebenen URLs.  Dies ist zusätzlich zu den [für den Microsoft Monitoring Agent benötigten Ports und URLs](../log-analytics/log-analytics-windows-agents.md) für die Herstellung einer Verbindung mit der OMS erforderlich. Wenn Sie einen Proxyserver für die Kommunikation zwischen dem Agent und dem OMS-Dienst verwenden, müssen Sie sicherstellen, dass auf die entsprechenden Ressourcen zugegriffen werden kann. Wenn Sie eine Firewall verwenden, um den Zugriff auf das Internet einzuschränken, müssen Sie die Firewall so konfigurieren, dass der Zugriff möglich ist.

Nachfolgend sind der Port und die URLs aufgeführt, die für die Kommunikation zwischen Hybrid Runbook Worker und Automation erforderlich sind.

* Port: Für ausgehenden Zugriff auf das Internet ist nur TCP 443 erforderlich.
* Globale URL: *.azure-automation.net

Wenn Sie ein Automation-Konto für eine bestimmte Region festgelegt haben und die Kommunikation mit diesem regionalen Datencenter beschränken möchten, sehen Sie sich die folgende Tabelle mit dem DNS-Eintrag für jede Region an:

| **Region** | **DNS-Einträge** |
| --- | --- |
| USA (Mitte/Süden) |scus-jobruntimedata-prod-su1.azure-automation.net |
| USA (Ost) 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| USA, Westen-Mitte | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Westeuropa |we-jobruntimedata-prod-su1.azure-automation.net |
| Nordeuropa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Kanada, Mitte |cc-jobruntimedata-prod-su1.azure-automation.net |
| Südostasien |sea-jobruntimedata-prod-su1.azure-automation.net |
| Indien (Mitte) |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japan Ost |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australien, Südosten |ase-jobruntimedata-prod-su1.azure-automation.net |
| UK, Süden | uks-jobruntimedata-prod-su1.azure-automation.net |
| US Government, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Wenn Sie eine Liste mit IP-Adressen statt Namen benötigen, können Sie die XML-Datei mit [Azure Datacenter-IP-Adressen](https://www.microsoft.com/download/details.aspx?id=41653) aus dem Microsoft Download Center herunterladen und überprüfen.

> [!NOTE]
> Diese Datei enthält die IP-Adressbereiche (einschließlich Compute-, SQL- und Speicherbereiche), die in Microsoft Azure-Rechenzentren verwendet werden. Eine aktualisierte Datei mit den derzeit bereitgestellten Bereichen und alle anstehenden Änderungen an den IP-Adressbereichen wird wöchentlich veröffentlicht. In der Datei enthaltene neue Bereiche werden frühestens nach einer Woche in den Rechenzentren verwendet. Laden Sie die neue XML-Datei jede Woche herunter, und nehmen Sie die erforderlichen Änderungen an Ihrer Website vor, um in Azure ausgeführte Dienste ordnungsgemäß zu ermitteln. Diese Datei zum Aktualisieren der BGP-Ankündigung von Azure-Bereichen wird ExpressRoute-Benutzern jeweils in der ersten Woche des Monats angezeigt.
>

## <a name="installing-hybrid-runbook-worker"></a>Installieren von Hybrid-Runbook-Worker

Zum Installieren und Konfigurieren eines Hybrid Runbook Worker stehen zwei Methoden zur Verfügung.  Die empfohlene Methode besteht darin, ein Automation-Runbook zu verwenden, um den Prozess, der zum Konfigurieren eines Windows-Computers erforderlich ist, vollständig zu automatisieren.  Bei der zweiten Methode wird die Rolle Schritt für Schritt manuell installiert und konfiguriert.  

### <a name="automated-deployment"></a>Automatisierte Bereitstellung

Führen Sie die folgenden Schritte aus, um die Installation und Konfiguration der Hybrid Worker-Rolle zu automatisieren.  

1. Laden Sie das Skript *New-OnPremiseHybridWorker.ps1* aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) direkt über den Computer herunter, auf dem die Hybrid Runbook Worker-Rolle ausgeführt wird, oder laden Sie es über einen anderen Computer in Ihrer Umgebung herunter, und kopieren sie es auf den Worker.  

    Das Skript *New-OnPremiseHybridWorker.ps1* erfordert während der Ausführung folgende Parameter:

  * *AutomationAccountName* (obligatorisch): Der Name Ihres Automation-Kontos.  
  * *ResourceGroupName* (obligatorisch): Der Name der Ressourcengruppe, die Ihrem Automation-Konto zugeordnet ist.  
  * *HybridGroupName* (obligatorisch): Der Name einer Hybrid Runbook Worker-Gruppe, die Sie als Ziel für die Runbooks angeben, die dieses Szenario unterstützen.
  *  *SubscriptionID* (obligatorisch): Die Azure-Abonnement-ID Ihres Automation-Kontos.
  *  *WorkspaceName* (optional): Der Name des OMS-Arbeitsbereichs.  Falls kein OMS-Arbeitsbereich vorhanden ist, wird dieser durch das Skript erstellt und konfiguriert.  

     > [!NOTE]
     > Derzeit werden für die Automation-OMS-Integration lediglich folgende Regionen unterstützt: **Australien, Südosten**, **USA, Osten 2**, **Asien, Südosten** und **Europa, Westen**.  Falls sich Ihr Automation-Konto nicht in einer dieser Regionen befindet, erstellt das Skript zwar einen OMS-Arbeitsbereich, weist Sie aber in einer Warnung darauf hin, dass keine Verknüpfung möglich ist.
     >
2. Starten Sie auf Ihrem Computer **Windows PowerShell** über den **Startbildschirm** im Administratormodus.  
3. Navigieren Sie über die PowerShell-Befehlszeilenshell zu dem Ordner mit dem heruntergeladenen Skript, und führen Sie es aus. Ändern Sie dabei die Werte für die Parameter *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* und *-WorkspaceName*.

     > [!NOTE]
     > Nach dem Ausführen des Skripts werden Sie aufgefordert, sich bei Azure zu authentifizieren.  Sie **müssen** sich mit einem Konto anmelden, das Mitglied der Rolle „Abonnement-Administratoren“ und Co-Administrator des Abonnements ist.  
     >  

        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Sie werden aufgefordert, der Installation von **NuGet** zuzustimmen und sich mit Ihren Azure-Anmeldeinformationen zu authentifizieren.<br><br> ![Ausführen des Skripts „New-OnPremiseHybridWorker“](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Nach vollständiger Ausführung des Skripts werden auf dem Blatt „Hybrid Worker-Gruppen“ die neue Gruppe und die Anzahl von Mitgliedern angezeigt. Im Falle einer bereits vorhandenen Gruppe wird die Anzahl der Mitglieder erhöht.  Sie können die Gruppe aus der Liste auf dem Blatt **Hybrid Worker-Gruppen** auswählen und dann die Kachel **Hybrid Worker** auswählen.  Auf dem Blatt **Hybrid Worker** werden die einzelnen Mitglieder der Gruppe aufgelistet.  

### <a name="manual-deployment"></a>Manuelle Bereitstellung
Führen Sie die ersten beiden Schritte einmal für Ihre Automation-Umgebung durch, und wiederholen Sie dann die verbleibenden Schritte für jeden Workercomputer.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Erstellen eines Operations Management Suite-Arbeitsbereichs
Sofern Sie noch nicht über einen Operations Management Suite-Arbeitsbereich verfügen, erstellen Sie diesen mithilfe der Anweisungen unter [Verwalten von Arbeitsbereichen](../log-analytics/log-analytics-manage-access.md). Wenn Sie bereits über einen Arbeitsbereich verfügen, können Sie diesen verwenden.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Hinzufügen der Automation-Lösung zum Operations Management Suite-Arbeitsbereich
Die Funktionalität von Operations Management Suite kann durch Lösungen erweitert werden.  Die Automation-Lösung fügt Azure Automation Funktionalität hinzu, einschließlich der Unterstützung für Hybrid Runbook Worker.  Wenn Sie die Lösung Ihrem Arbeitsbereich hinzufügen, werden Workerkomponenten automatisch per Push auf den Agent-Computer übertragen, den Sie im nächsten Schritt installieren werden.

Befolgen Sie die Anweisungen unter [Hinzufügen einer Lösung über den Lösungskatalog](../log-analytics/log-analytics-add-solutions.md) , um Ihrem Operations Management Suite-Arbeitsbereich die Lösung **Automation** hinzuzufügen.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installieren von Microsoft Monitoring Agent
Microsoft Monitoring Agent verbindet Computer mit Operations Management Suite.  Wenn Sie den Agent auf dem lokalen Computer installieren und mit Ihrem Arbeitsbereich verbinden, werden automatisch die erforderlichen Komponenten für Hybrid Runbook Worker heruntergeladen.

Befolgen Sie die Anweisungen unter [Verbinden von Windows-Computern mit Log Analytics](../log-analytics/log-analytics-windows-agents.md) , um den Agent auf dem lokalen Computer zu installieren.  Sie können diesen Vorgang für mehrere Computer wiederholen, um Ihrer Umgebung mehrere Worker hinzuzufügen.

Wenn der Agent erfolgreich mit Operations Management Suite verbunden ist, wird er im Operations Management Suite-Bereich **Einstellungen** auf der Registerkarte **Verbundene Datenquellen** aufgeführt.  Sie können sicherstellen, dass der Agent die Automation-Lösung ordnungsgemäß heruntergeladen hat, indem Sie prüfen, ob unter „C:\Programme\Microsoft Monitoring Agent\Agent“ ein Ordner namens **AzureAutomationFiles** vorhanden ist.  Unter „C:\Programme\Microsoft Monitoring Agent\Agent\AzureAutomation\“ im Unterordner \\*version* können Sie die Hybrid Runbook Worker-Version überprüfen.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installieren der Runbookumgebung und Verbindungsherstellung mit Azure Automation
Wenn Sie der Operations Management Suite einen Agent hinzufügen, lädt die Automation-Lösung per Push das PowerShell-Modul **HybridRegistration** herunter, in dem das Cmdlet **Add-HybridRunbookWorker** enthalten ist.  Sie verwenden dieses Cmdlet zum Installieren der Runbookumgebung auf dem Computer und registrieren diesen bei Azure Automation.

Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie die folgenden Befehle zum Importieren des Moduls aus.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Führen Sie dann das Cmdlet **Add-HybridRunbookWorker** mit der folgenden Syntax aus:

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Sie finden die für dieses Cmdlet erforderlichen Informationen auf dem Blatt **Schlüssel verwalten** im Azure-Portal.  Öffnen Sie dieses Blatt, indem Sie in Ihrem Automation-Konto auf dem Blatt **Einstellungen** die Option **Schlüssel** auswählen.

![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name** ist der Name der Hybrid-Runbook-Workergruppe. Wenn die Gruppe bereits im Automation-Konto vorhanden ist, wird der aktuelle Computer der Gruppe hinzugefügt.  Ist die Gruppe nicht vorhanden, wird sie hinzugefügt.
* **EndPoint** ist das Feld **URL** auf dem Blatt **Schlüssel verwalten**.
* **Token** ist der **Primäre Zugriffsschlüssel** im Blatt **Schlüssel verwalten**.  

Verwenden Sie den Schalter **-Verbose** mit **Add-HybridRunbookWorker**, um detaillierte Informationen über die Installation zu erhalten.

#### <a name="5-install-powershell-modules"></a>5. Installieren von PowerShell-Modulen
Runbooks können beliebige Aktivitäten und Cmdlets der Module verwenden, die Sie in Ihrer Azure Automation-Umgebung installiert haben.  Diese Module werden nicht automatisch auf den lokalen Computern bereitgestellt, sie müssen manuell installiert werden.  Eine Ausnahme stellt das standardmäßig installierte Azure-Modul dar. Es bietet Zugriff auf Cmdlets für alle Azure-Dienste und -Aktivitäten für Azure Automation.

Da der primäre Zweck der Hybrid Runbook Worker-Funktion darin besteht, lokale Ressourcen zu verwalten, müssen Sie sehr wahrscheinlich die Module zur Unterstützung dieser Ressourcen installieren.  Unter [Installieren von Modulen](http://msdn.microsoft.com/library/dd878350.aspx) finden Sie Informationen zum Installieren von PowerShell-Modulen.  Installierte Module müssen sich in einem Speicherort befinden, auf den von der PSModulePath-Umgebungsvariable verwiesen wird, damit sie vom Hybrid Worker automatisch importiert werden.  Weitere Informationen finden Sie unter [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Ändern des PSModulePath-Installationspfads).

## <a name="removing-hybrid-runbook-worker"></a>Entfernen von Hybrid Runbook Worker
Je nach ihren Anforderungen können Sie eine oder mehrere Hybrid Runbook Worker aus einer Gruppe entfernen, oder Sie können die Gruppe entfernen, je nach Ihren Anforderungen.  Wenn Sie einen Hybrid Runbook Worker von einem lokalen Computer entfernen möchten, führen Sie die folgenden Schritte aus.

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.  
2. Wählen Sie auf dem Blatt **Einstellungen** die Option **Schlüssel** aus, und notieren Sie sich die Werte für die Felder **URL** und **Primärer Zugriffsschlüssel**.  Sie benötigen diese Informationen im nächsten Schritt.
3. Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie den folgenden Befehl aus: `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Verwenden Sie den Schalter **-Verbose** , um ein ausführliches Protokoll zum Entfernungsvorgang zu erhalten.

> [!NOTE]
> Dadurch wird nicht Microsoft Monitoring Agent von diesem Computer entfernt, sondern nur die Funktionen und die Konfiguration der Hybrid Runbook-Workerrolle.  

## <a name="remove-hybrid-worker-groups"></a>Entfernen von Hybrid Worker-Gruppen
Um eine Gruppe zu entfernen, müssen Sie zuerst den Hybrid Runbook Worker von allen Computern entfernen, die Mitglied der Gruppe sind. Verwenden Sie dazu das weiter oben gezeigte Verfahren, und führen Sie dann die folgenden Schritte aus, um die Gruppe zu entfernen.  

1. Wählen Sie im Azure-Portal das Automation-Konto aus.
2. Wählen Sie die Kachel **Hybrid Worker-Gruppen** Kachel und auf dem Blatt **Hybrid Worker-Gruppen** die Gruppe, die Sie löschen möchten.  Nach dem Auswählen der Gruppe wird das Blatt mit den Eigenschaften der **Hybrid Worker-Gruppen** angezeigt.<br> ![Blatt „Hybrid-Runbook-Workergruppen“](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. Klicken Sie im Fenster mit den Eigenschaften der ausgewählten Gruppe auf **Löschen**.  Eine Meldung wird angezeigt, in der Sie aufgefordert werden, diese Aktion zu bestätigen. Wählen Sie **Ja**, wenn Sie sicher sind, dass Sie fortfahren möchten.<br> ![Bestätigungsdialogfeld zum Löschen der Gruppe](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Dieser Vorgang kann einige Sekunden dauern, und Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Starten von Runbooks auf einem Hybrid-Runbook-Worker
[Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) werden die verschiedenen Methoden zum Starten eines Runbooks beschrieben.  Hybrid-Runbook-Worker fügt die Option **RunOn** hinzu, mit der Sie den Namen einer Hybrid-Runbook-Workergruppe angeben können.  Beim Angeben einer Gruppe wird das Runbook von den Workern in dieser Gruppe abgerufen und ausgeführt.  Wird diese Option nicht angegeben, erfolgt die Ausführung in normaler Weise in Azure Automation.

Wenn Sie ein Runbook im Azure-Portal starten, wird die Option **Ausführen auf** angezeigt. Sie können entweder **Azure** oder **Hybrid Worker** auswählen.  Bei Auswahl von **Hybrid-Worker** können Sie anschließend aus einer Dropdownliste die gewünschte Gruppe auswählen.

Verwenden Sie den Parameter **RunOn**.  Mit dem folgenden Befehl wird mithilfe von Windows PowerShell ein Runbook namens „Test-Runbook“ auf einer Hybrid Runbook Worker-Gruppe mit dem Namen „MyHybridGroup“ gestartet.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> Der Parameter **RunOn** wurde dem Cmdlet **Start-AzureAutomationRunbook** in Version 0.9.1 von Microsoft Azure PowerShell hinzugefügt.  Sie sollten die [neueste Version herunterladen](https://azure.microsoft.com/downloads/) , wenn auf Ihrem System eine Vorgängerversion installiert ist.  Sie müssen diese Version nur auf der Arbeitsstation installieren, auf der Sie das Runbook aus Windows PowerShell starten.  Eine Installation auf dem Workercomputer ist nur erforderlich, wenn Sie Runbooks von diesem Computer starten möchten.  Sie können ein Runbook auf einem Hybrid-Runbook-Worker derzeit nicht aus einem anderen Runbook starten, da hierzu die neueste Version von Azure PowerShell in Ihrem Automation-Konto installiert sein müsste.  Die neueste Version wird automatisch in Azure Automation aktualisiert und kurz darauf automatisch per Push auf die Worker übertragen.
>
>

## <a name="runbook-permissions"></a>Runbookberechtigungen
Für Runbooks, die auf einem Hybrid Runbook Worker ausgeführt werden, kann nicht dieselbe Methode verwendet werden, mit der Runbooks üblicherweise bei Azure-Ressourcen authentifiziert werden, da diese Runbooks auf Ressourcen außerhalb von Azure zugreifen.  Das Runbook kann entweder eine eigene Authentifizierung für lokale Ressourcen bereitstellen, oder Sie können ein „RunAs“-Konto angeben, um einen Benutzerkontext für alle Runbooks bereitzustellen.

### <a name="runbook-authentication"></a>Authentifizierung von Runbooks
Runbooks werden standardmäßig im Kontext des lokalen Systemkontos auf dem lokalen Computer ausgeführt. Deshalb müssen sie sich bei Ressourcen authentifizieren, auf die sie zugreifen.  

Sie können die [Anmeldeinformationsobjekte](http://msdn.microsoft.com/library/dn940015.aspx) und [Zertifikatobjekte](http://msdn.microsoft.com/library/dn940013.aspx) in Ihrem Runbook gemeinsam mit Cmdlets zur Angabe von Anmeldeinformationen verwenden, um eine Authentifizierung für verschiedene Ressourcen durchzuführen.  Das folgende Beispiel zeigt einen Teil eines Runbooks, mit dem ein Computer neu gestartet wird.  Es werden Anmeldeinformationen aus einem Anmeldeinformationsobjekt und der Name des Computers aus einem Variablenobjekt abgerufen, anschließend werden diese Werte im Cmdlet "Restart-Computer" eingesetzt.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Sie können auch [InlineScript](automation-powershell-workflow.md#inlinescript) verwenden, mit dem Sie Codeblöcke mit den im [allgemeinen PSCredential-Parameter](http://technet.microsoft.com/library/jj129719.aspx) angegebenen Anmeldeinformationen auf einem anderen Computer ausführen können.

### <a name="runas-account"></a>„RunAs“-Konto
Um zu vermeiden, dass Runbooks ihre eigene Authentifizierung für lokale Ressourcen bereitstellen müssen, können Sie für eine Hybrid Worker-Gruppe ein **RunAs**-Konto angeben.  Sie geben ein [Anmeldeinformationsobjekt](automation-credentials.md) mit Zugriff auf lokale Ressourcen an. Alle Runbooks verwenden anschließend diese Anmeldeinformationen, wenn die Ausführung auf einem Hybrid Runbook Worker in der Gruppe erfolgt.  

Der Benutzername für die Anmeldeinformationen muss eines der folgenden Formate haben:

* Domäne\Benutzername
* username@domain
* Benutzername (für Konten, die für den lokalen Computer lokal sind)

Verwenden Sie das folgende Verfahren, um ein „RunAs“-Konto für eine Hybrid Worker Gruppe anzugeben:

1. Erstellen Sie ein [Anmeldeinformationsobjekt](automation-credentials.md) mit Zugriff auf lokale Ressourcen.
2. Wählen Sie im Azure-Portal das Automation-Konto aus.
3. Wählen Sie die Kachel **Hybrid Worker-Gruppen** und dann die Gruppe aus.
4. Wählen Sie **Alle Einstellungen** und dann **Einstellungen für Hybrid Worker-Gruppe** aus.
5. Ändern Sie **Ausführen als** von **Standard** in **Benutzerdefiniert**.
6. Wählen Sie die Anmeldeinformationen aus, und klicken Sie auf **Speichern**.

### <a name="automation-run-as-account"></a>Ausführendes Automation-Konto
Im Rahmen Ihres automatisierten Buildprozesses für die Bereitstellung von Ressourcen in Azure ist es möglicherweise erforderlich, lokale Systeme abzufragen, um einen Task oder eine Reihe von Schritten in Ihrer Bereitstellungssequenz zu unterstützen.  Um die Authentifizierung bei Azure mit einem ausführenden Konto zu unterstützen, müssen Sie das Zertifikat für das ausführende Konto installieren.  

Das folgende PowerShell-Runbook, *Export-RunAsCertificateToHybridWorker*, exportiert das Zertifikat für das ausführende Konto aus Ihrem Azure Automation-Konto, lädt es herunter und importiert es in den Zertifikatspeicher für den lokalen Computer auf einem Hybrid Worker, der mit dem gleichen Konto verbunden ist.  Sobald dieser Schritt abgeschlossen ist, überprüft das Runbook, ob der Worker sich unter Verwendung des ausführenden Kontos erfolgreich bei Azure authentifizieren kann.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT
    .TAGS Azure Automation
    .LICENSEURI
    .PROJECTURI
    .ICONURI
    .EXTERNALMODULEDEPENDENCIES
    .REQUIREDSCRIPTS
    .EXTERNALSCRIPTDEPENDENCIES
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])]

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

    Write-Output ("Importing certificate into local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Select-AzureRmSubscription -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Speichern Sie das *Export-RunAsCertificateToHybridWorker*-Runbook mit der Erweiterung `.ps1` auf Ihrem Computer.  Importieren Sie das Runbook in Ihr Automation-Konto, und bearbeiten Sie es. Ändern Sie dabei den Wert der Variablen `$Password` in Ihr eigenes Kennwort.  Veröffentlichen Sie das Runbook, und führen Sie es dann mit der Hybrid Worker-Gruppe als Ziel aus, die Runbooks über das ausführende Konto ausführt und authentifiziert.  Der Auftragsdatenstrom meldet den Versuch, das Zertifikat in den lokalen Computerspeicher zu importieren. Im Bericht folgen mehrere Zeilen, je nachdem, wie viele Automation-Konten in Ihrem Abonnement definiert sind und ob die Authentifizierung erfolgreich ist.  

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Erstellen von Runbooks für Hybrid-Runbook-Worker
Es gibt keine Unterschiede in der Struktur von Runbooks, die in Azure Automation oder auf einem Hybrid-Runbook-Worker ausgeführt werden. Die jeweils verwendeten Runbooks unterscheiden sich jedoch wahrscheinlich erheblich, da Runbooks für Hybrid Runbook Worker typischerweise lokale Ressourcen in Ihrem Rechenzentrum verwalten, während Runbooks in Azure Automation üblicherweise Ressourcen in der Azure-Cloud verwalten.

Sie können ein Runbook für Hybrid-Runbook-Worker in Azure Automation bearbeiten, es ist jedoch möglicherweise schwierig, das Runbook im Editor zu testen.  Die PowerShell-Module für den Zugriff auf die lokalen Ressourcen sind möglicherweise nicht in Ihrer Azure Automation-Umgebung installiert, sodass beim Testen Fehler auftreten.  Wenn Sie die erforderlichen Module installieren, wird das Runbook ausgeführt, der Test kann aufgrund des fehlenden Zugriffs auf die lokalen Ressourcen jedoch nicht vollständig ausgeführt werden.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Problembehandlung bei Runbooks auf Hybrid Runbook Worker
[Runbookausgabe und -meldungen](automation-runbook-output-and-messages.md) werden von Hybridworkern an Azure Automation gesendet, genau wie Runbookaufträge, die in der Cloud ausgeführt werden.  Sie können auch die Verbose- und Progress-Streams auf dieselbe Weise aktivieren wie für andere Runbooks.  

Protokolle werden lokal auf jedem Hybridworker unter "C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes" gespeichert.

Wenn Ihre Runbooks nicht erfolgreich ausgeführt werden und in der Auftragsübersicht der Status **Angehalten**angezeigt wird, lesen Sie den Artikel zur Problembehandlung [Hybrid Runbook Worker: Ein Runbookauftrag wird mit dem Status „Angehalten“ beendet](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

## <a name="relationship-to-service-management-automation"></a>Beziehung zu Service Management Automation (SMA)
Mit [Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) können Sie dieselben Runbooks, die von Azure Automation unterstützt werden, in Ihrem lokalen Rechenzentrum ausführen. SMA wird in der Regel mit Windows Azure- Pack bereitgestellt, da Windows Azure Pack eine grafische Benutzeroberfläche für die SMA-Verwaltung enthält. Im Gegensatz zu Azure Automation erfordert SMA eine lokale Installation, die Webserver zum Hosten der API, eine Datenbank für Runbooks und SMA-Konfiguration sowie Runbook Worker zum Ausführen von Aufträgen enthält. Azure Automation stellt diese Dienste in der Cloud bereit, Sie müssen lediglich die Hybrid-Runbook-Worker in Ihrer lokalen Umgebung verwalten.

Wenn Sie SMA bereits verwenden, können Sie Ihre Runbooks nach Azure Automation verschieben, damit diese ohne Änderungen mit Hybrid-Runbook-Worker verwendet werden. Hierbei wird vorausgesetzt, dass eine eigene Authentifizierung gegenüber den verwendeten Ressourcen durchgeführt wird, wie beschrieben unter [Erstellen von Runbooks für Hybrid-Runbook-Worker](#creating-runbooks-for-hybrid-runbook-worker).  Runbooks werden in SMA im Kontext des Dienstkontos auf dem Workerserver ausgeführt, der möglicherweise die Authentifizierung für die Runbooks bereitstellt.

Sie können anhand der folgenden Kriterien prüfen, ob Azure Automation mit Hybrid-Runbook-Worker oder Service Management Automation die bessere Wahl für Ihre Anforderungen ist.

* SMA erfordert eine lokale Installation von zugrunde liegenden Komponenten, die mit Windows Azure Pack verbunden sind, wenn eine grafische Verwaltungsschnittstelle erforderlich ist. Darüber hinaus benötigt SMA mehr lokale Ressourcen mit höheren Wartungskosten als Azure Automation, bei dem nur ein Agent auf den lokalen Runbookworkern installiert werden muss. Die Agents werden von Operations Management Suite verwaltet, wodurch sich Ihre Wartungskosten verringern.
* Azure Automation speichert Runbooks in der Cloud und übermittelt diese an lokale Hybrid-Runbook-Worker. Wenn Ihre Sicherheitsrichtlinie dieses Verhalten nicht zulässt, sollten Sie SMA verwenden.
* SMA ist in System Center enthalten. und erfordert daher eine System Center 2012 R2-Lizenz erforderlich. Azure Automation basiert auf einem mehrstufigen Abonnementmodell.
* Azure Automation bietet erweiterte Funktionen wie z. B. grafische Runbooks, die in SMA nicht verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu den verschiedenen Methoden, die zum Starten eines Runbooks verwendet werden können, finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).  
* Informationen zu den verschiedenen Verfahren, wie Sie in Azure Automation mit PowerShell und PowerShell-Workflow-Runbooks unter Verwendung des Text-Editors arbeiten können, finden Sie unter [Bearbeiten eines Runbooks in Azure Automation](automation-edit-textual-runbook.md)

