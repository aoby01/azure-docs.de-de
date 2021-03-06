---
title: 'Tutorial: Azure Active Directory-Integration mit Panopto | Microsoft Docs'
description: "Erfahren Sie, wie Sie Panopto mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 078a2ea0db006cf976f89a55c65a536a7b9f04aa
ms.lasthandoff: 04/03/2017


---

# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Azure Active Directory-Integration mit Panopto
In diesem Tutorial wird die Integration von Azure und Panopto erläutert. 

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Ein gültiges Azure-Abonnement
* Einen Panopto-Mandanten

Nach Abschluss dieses Tutorials können sich die Panopto zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Panopto-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Tutorial beschriebene Szenario besteht aus den folgenden Bausteinen:

1. Aktivieren der Anwendungsintegration für Panopto
2. Konfigurieren des einmaligen Anmeldens (SSO)
3. Konfigurieren der Benutzerbereitstellung
4. Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-panopto-tutorial/IC777665.png "Szenario")

## <a name="enable-the-application-integration-for-panopto"></a>Aktivieren der Anwendungsintegration für Panopto
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Panopto aktivieren.

**Um die Anwendungsintegration für Panopto zu aktivieren, führen Sie die folgenden Schritte durch:**

1. Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.
3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen** .
   
   ![Anwendungen](./media/active-directory-saas-panopto-tutorial/IC700994.png "Anwendungen")
4. Klicken Sie unten auf der Seite auf **Hinzufügen** .
   
   ![Anwendung hinzufügen](./media/active-directory-saas-panopto-tutorial/IC749321.png "Anwendung hinzufügen")
5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.
   
   ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-panopto-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")
6. Geben Sie im **Suchfeld** das Wort **Panopto** ein.
   
   ![Anwendungskatalog](./media/active-directory-saas-panopto-tutorial/IC777666.png "Anwendungskatalog")
7. Wählen Sie im Ergebnisbereich **Panopto** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Panopto zu authentifizieren.  

Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. 

Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)(Konvertieren eines binären Zertifikats in eine Textdatei; in englischer Sprache) weitere Informationen.

**Führen Sie zum Konfigurieren von SSO die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Panopto** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.
   
   ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777667.png "Einmaliges Anmelden konfigurieren")
2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Panopto anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.
   
   ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777668.png "Einmaliges Anmelden konfigurieren")
3. Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Panopto-Anmelde-URL** die URL im Format „*https://\<Mandantenname\> Panopto.com*“ ein, und klicken Sie dann auf **Weiter**.
   
   ![App-URL konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777528.png "App-URL konfigurieren")
4. Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Panopto** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.
   
   ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777669.png "Einmaliges Anmelden konfigurieren")
5. Melden Sie sich in einem anderen Webbrowserfenster bei der Panopto-Unternehmenswebsite als Administrator an.
6. Klicken Sie in der Symbolleiste auf der linken Seite auf **System** und dann auf **Identitätsanbieter**.
   
   ![System](./media/active-directory-saas-panopto-tutorial/IC777670.png "System")
7. Klicken Sie auf **Anbieter hinzufügen**.
   
   ![Identitätsanbieter](./media/active-directory-saas-panopto-tutorial/IC777671.png "Identitätsanbieter")
8. Führen Sie im Abschnitt für den SAML-Anbieter die folgenden Schritte aus:
   
   ![SaaS-Konfiguration](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS-Konfiguration")
   
   1. Wählen Sie in der Liste **Anbietertyp** die Option **SAML20** aus.
   2. Geben Sie im Textfeld **Instanzname** einen Namen für die Instanz ein.
   3. Geben Sie im Textfeld **Beschreibung** eine Beschreibung ein.
   4. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Panopto** den Wert für **Aussteller-URL**, und fügen Sie ihn in das Textfeld **Issuer** ein.
   5. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Panopto** den Wert für **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **Bounce Page Url** ein.
   6. Erstellen Sie eine **Base64-codierte** Datei aus dem heruntergeladenen Zertifikat.    
   
      >[!TIP]
      >Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)(in englischer Sprache).
      >
      
   7. Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Öffentlicher Schlüssel** ein.
   8. Klicken Sie auf **Speichern**.

 ![Speichern](./media/active-directory-saas-panopto-tutorial/IC777673.png "Speichern")
9. Bestätigen Sie im klassischen Azure-Portal die Konfiguration der einmaligen Anmeldung, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.
   
  ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-panopto-tutorial/IC777674.png "Einmaliges Anmelden konfigurieren")
   
## <a name="configure-user-provisioning"></a>Benutzerbereitstellung konfigurieren

Für das Konfigurieren der Benutzerbereitstellung in Panopto steht kein Aktionselement zur Verfügung.  
Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Panopto anzumelden, überprüft Panopto, ob der Benutzer vorhanden ist.  

Ist noch kein Benutzerkonto verfügbar, wird es von Panopto automatisch erstellt.

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Panopto-Benutzerkonten oder mithilfe der von Panopto bereitgestellten APIs erstellen.
>
>

## <a name="assign-users"></a>Benutzer zuweisen
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

**Um Panopto Benutzer hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Erstellen Sie im klassischen Azure-Portal ein Testkonto.
2. Klicken Sie auf der Anwendungsintegrationsseite für **Panopto** auf **Benutzer zuweisen**.
   
   ![Zuweisen von Benutzern](./media/active-directory-saas-panopto-tutorial/IC777675.png "Zuweisen von Benutzern")
3. Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.
   
   ![Ja](./media/active-directory-saas-panopto-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


