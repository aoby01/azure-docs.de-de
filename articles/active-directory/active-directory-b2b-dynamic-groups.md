---
title: Dynamische Gruppen und Azure Active Directory B2B-Zusammenarbeit | Microsoft-Dokumentation
description: Die Azure Active Directory B2B-Zusammenarbeit kann mit dynamischen Azure AD-Gruppen verwendet werden.
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/04/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: a694d01281cfdc4559f779f18b92d0412d59cf45
ms.contentlocale: de-de
ms.lasthandoff: 05/05/2017


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamische Gruppen und Azure Active Directory B2B-Zusammenarbeit

## <a name="what-are-dynamic-groups"></a>Was sind dynamische Gruppen?
Die dynamische Konfiguration der Mitgliedschaft in Sicherheitsgruppen für Azure Active Directory (Azure AD) ist im [Azure-Portal](https://portal.azure.com) verfügbar. Administratoren können Regeln einrichten, um in Azure Active Directory erstellte Gruppen anhand von Benutzerattributen (z.B. Benutzertyp, Abteilung oder Land) aufzufüllen. So können Mitglieder basierend auf Änderungen an ihren Attributen automatisch zu einer Sicherheitsgruppe hinzugefügt oder daraus entfernt werden. Anhand dieser Gruppen können Administratoren Zugriff auf Anwendungen oder Cloudressourcen gewähren (z.B. SharePoint-Websites und -Dokumente) oder den Mitgliedern Lizenzen zuweisen. Weitere Informationen zu dynamischen Gruppen erhalten Sie unter [Dedizierte Gruppen in Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Mit einem AAD-Premium-Abonnement P1 oder P2 haben Sie im Azure-Portal die Möglichkeit, erweiterte Regeln zu erstellen, mit denen Sie komplexere attributbasierte, dynamische Mitgliedschaften für Azure Active Directory-Gruppen aktivieren können. Weitere Informationen zum Erstellen von erweiterten Regeln finden Sie unter [Verwenden von Attributen zum Erstellen erweiterter Regeln für Gruppenmitgliedschaften im Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Was sind die integrierten dynamischen Gruppen?
Die dynamische Gruppe **Alle Benutzer** ermöglicht es Mandantenadministratoren, mit nur einem Klick eine Gruppe zu erstellen, die alle Benutzer des Mandanten umfasst. Die Gruppe **Alle Benutzer** umfasst standardmäßig alle Benutzer im Verzeichnis, einschließlich Mitgliedern und Gästen.
Im neuen Azure Active Directory-Verwaltungsportal können Sie die Gruppe **Alle Benutzer** in der Ansicht „Gruppeneinstellungen“ aktivieren.

![Integrierte Gruppen](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

Festschreiben der dynamischen Gruppe „Alle Benutzer“. Die Gruppe **Alle Benutzer** umfasst standardmäßig auch Ihre Benutzer und Gastbenutzer für die B2B-Zusammenarbeit. Wenn Sie Ihre Gruppe **Alle Benutzer** zusätzlich sichern möchten, indem Sie Gastbenutzer entfernen, können Sie dies ganz einfach durch eine Änderung der attributbasierten Regel der Gruppe **Alle Benutzer** erreichen. Die Abbildung unten zeigt die Gruppe **Alle Benutzer**, die modifiziert wurde und Gäste ausschließt.

![Aktivieren der Gruppe „Alle Benutzer“](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Es kann auch hilfreich sein, eine neue dynamische Gruppe zu erstellen, die nur Gastbenutzer enthält. Dies ist eine praktische Methode, wenn Sie Richtlinien (z.B. Richtlinien für bedingten Zugriff) nur für Gastbenutzer festlegen möchten.
Eine solche Gruppe könnte beispielsweise folgendermaßen aussehen:

![Ausschließen von Gastbenutzern](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Kollaboration:

* [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Eigenschaften von B2B-Zusammenarbeitsbenutzern](active-directory-b2b-user-properties.md)
* [Hinzufügen eines B2B-Zusammenarbeitsbenutzers zu einer Rolle](active-directory-b2b-add-guest-to-role.md)
* [Delegieren von Einladungen zur B2B-Zusammenarbeit](active-directory-b2b-delegate-invitations.md)
* [B2B-Zusammenarbeit: Code- und PowerShell-Beispiele](active-directory-b2b-code-samples.md)
* [Konfigurieren von SaaS-Apps für die B2B-Zusammenarbeit](active-directory-b2b-configure-saas-apps.md)
* [Benutzertoken für die B2B-Zusammenarbeit](active-directory-b2b-user-token.md)
* [Zuordnen von Benutzeransprüchen für die B2B-Zusammenarbeit](active-directory-b2b-claims-mapping.md)
* [Externe Office 365-Freigabe](active-directory-b2b-o365-external-user.md)
* [Aktuelle Einschränkungen der B2B-Zusammenarbeit](active-directory-b2b-current-limitations.md)

