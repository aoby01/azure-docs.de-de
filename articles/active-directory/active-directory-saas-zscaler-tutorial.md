---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler | Microsoft Docs'
description: "Hier erfahren Sie, wie Sie Zscaler mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f9e6f640b97a35aab84063f17a155a32f809d374
ms.openlocfilehash: 835f2e0b636af17d2c2c032cda572b00a4fdbef7
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Azure Active Directory-Integration mit Zscaler
In diesem Tutorial wird die Integration von Azure und Zscaler erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Ein gültiges Azure-Abonnement
* Ein Mandant in Zscaler

Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1. Aktivieren der Anwendungsintegration für Zscaler
2. Konfigurieren der einmaligen Anmeldung
3. Konfigurieren von Proxyeinstellungen
4. Konfigurieren der Benutzerbereitstellung
5. Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Szenario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Aktivieren der Anwendungsintegration für Zscaler
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zscaler aktivieren.

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>So aktivieren Sie die Anwendungsintegration für Zscaler:
1. Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen** .
   
    ![Anwendungen](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Anwendungen")

4. Klicken Sie unten auf der Seite auf **Hinzufügen** .
   
    ![Anwendung hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Anwendung hinzufügen")

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.
   
    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6. Geben Sie im **Suchfeld** den Begriff **Zscaler** ein.
   
    ![Anwendungskatalog](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Anwendungskatalog")

7. Wählen Sie im Ergebnisbereich **Zscaler** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>Konfigurieren der einmaligen Anmeldung
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zscaler zu authentifizieren.  
Im Rahmen dieses Verfahrens müssen Sie ein Zertifikat auf Zscaler hochladen.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>So konfigurieren Sie einmaliges Anmelden
1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Zscaler** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.
   
    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Einmaliges Anmelden aktivieren")

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Zscaler anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.
   
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Einmaliges Anmelden konfigurieren")

3. Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Zscaler-Anmelde-URL** die von Zscaler erhaltene Anmelde-URL ein, und klicken Sie auf **Weiter**: 
   
    > [!NOTE]
    > Bitte wenden Sie sich an das Zscaler-Supportteam, wenn Sie Ihre Anmelde-URL nicht kennen.
    > 
    > 
   
    ![App-URL konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769231.png "App-URL konfigurieren")

4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für Zscaler** die folgenden Schritte aus.
   
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Einmaliges Anmelden konfigurieren")
   
    1. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat dann lokal unter **C:\\Zscaler.cer**.
    2. Kopieren Sie die **Authentifizierungsanforderungs-URL** in die Zwischenablage.

5. Melden Sie sich bei Ihrem Zscaler-Mandanten an.

6. Klicken Sie oben im Menü auf **Verwaltung**.
   
    ![Verwaltung](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Verwaltung")

7. Klicken Sie unter **Administratoren & Rollen verwalten** auf **Benutzer & Authentifizierung verwalten**.
   
    ![Administratoren und Rollen verwalten](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Administratoren und Rollen verwalten")

8. Führen Sie im Abschnitt **Auswählen von Authentifizierungsoptionen für Ihre Organisation** die folgenden Schritte aus:
   
    ![Authentifizierungsoptionen wählen](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Authentifizierungsoptionen wählen")
   
    1. Wählen Sie **Authentifizeren mit der einmaligen Anmeldung für SAML**.
    2. Klicken Sie auf **Einzelne Parameter der einmaligen Anmeldung für SAML konfigurieren**.

9. Führen Sie auf der Dialogfeldseite **Parameter der einmaligen Anmeldung für SAML konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Fertig**:
   
    ![Zertifikat hochladen](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Zertifikat hochladen")
   
    1. Fügen Sie in das Textfeld **URL des SAML-Portals, an das Benutzer zur Authentifizierung weitergeleitet werden** den Wert des Felds **Authentifizierungsanforderungs-URL** aus dem klassischen Azure-Portal ein.
   
    2. Geben Sie im Textfeld **Attribut mit Anmeldenamen** die **NameID** ein.
   
    3. Laden Sie in das Feld **Öffentliches SSL-Zertifikat hochladen** das Zertifikat hoch, das Sie aus dem klassischen Azure-Portal heruntergeladen haben.
   
    4. Wählen Sie **Automatische SAML-Bereitstellung aktivieren**.

10. Führen Sie auf der Dialogseite **Benutzerauthentifizierung konfigurieren** die folgenden Schritte aus:
    
    ![Benutzerauthentifizierung konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Benutzerauthentifizierung konfigurieren")
    
    1. Klicken Sie auf **Speichern**.
    2. Klicken Sie auf **Jetzt aktivieren**.

11. Bestätigen Sie im klassischen Azure-Portal die Konfiguration der einmaligen Anmeldung, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.
    
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Einmaliges Anmelden konfigurieren")


## <a name="configuring-proxy-settings"></a>Konfigurieren von Proxyeinstellungen
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1. Starten Sie **Internet Explorer**.

2. Wählen Sie im Menü **Extras** die Option **Internetoptionen**, um das Dialogfeld **Internetoptionen** zu öffnen.
   
    ![Internetoptionen](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internetoptionen")

3. Klicken Sie auf die Registerkarte **Verbindungen** .
   
    ![Verbindungen](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Verbindungen")

4. Klicken Sie zum Öffnen des Dialogfelds **LAN-Einstellungen** auf **LAN-Einstellungen**.

5. Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:
   
    ![Proxyserver](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxyserver")
   
    1. Wählen Sie "Proxyserver für LAN verwenden" aus.

    2. Geben Sie in das Textfeld "Adresse" **gateway.zscalertwo.net**ein.
   
    3. Geben Sie im Textfeld „Port“ **80**ein.
   
    4. Wählen Sie **Proxyserver für lokale Adressen umgehen**.
   
    5. Klicken Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** auf **OK**.

6. Klicken Sie zum Schließen des Dialogfelds **Internetoptionen** auf **OK**.

## <a name="configuring-user-provisioning"></a>Konfigurieren der Benutzerbereitstellung
Damit sich Azure AD-Benutzer bei Zscaler anmelden können, müssen sie in Zscaler bereitgestellt werden.  
Im Fall von Zscaler ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung

1. Melden Sie sich bei Ihrem **Zscaler** -Mandanten an.

2. Klicken Sie auf **Verwaltung**.

    ![Verwaltung](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Verwaltung")

3. Klicken Sie auf **Benutzerverwaltung**.
   
    ![Benutzerverwaltung](./media/active-directory-saas-zscaler-tutorial/IC781036.png "Benutzerverwaltung")

4. Klicken Sie auf der Registerkarte **Benutzer** auf **Hinzufügen**.
   
    ![Hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Hinzufügen")

5. Führen Sie im Abschnitt "Benutzer hinzufügen" die folgenden Schritte aus:
   
    ![Benutzer hinzufügen](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Benutzer hinzufügen")
   
    1. Geben Sie die **Benutzer-ID**, den **Benutzeranzeigenamen**, das **Kennwort** und **Kennwort bestätigen** ein, und wählen Sie dann **Gruppen** und die **Abteilung** eines gültigen AAD-Kontos aus, das Sie bereitstellen möchten.
   
    2. Klicken Sie auf **Speichern**.

## <a name="assigning-users"></a>Zuweisen von Benutzern
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>So weisen Sie Zscaler Benutzer zu:

1. Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2. Klicken Sie auf der Anwendungsintegrationsseite für **Zscaler** auf **Benutzer zuweisen**.
   
    ![Zuweisen von Benutzern](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Zuweisen von Benutzern")

3. Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.
   
    ![Ja](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


