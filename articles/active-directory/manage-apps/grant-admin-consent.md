---
title: Bevilja administratörsmedgivande för hela klientorganisationen till ett program – Azure AD
description: Lär dig hur du beviljar ett program medgivande för hela klientorganisationen så att slutanvändarna inte uppmanas att ge sitt medgivande när de loggar in i ett program.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374190"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bevilja administratörsmedgivande för hela klientorganisationen till ett program

  Lär dig hur du beviljar administratörsmedgivande för hela klientorganisationen till ett program. Den här artikeln innehåller olika sätt att åstadkomma detta.

Mer information om medgivande till program finns i Azure Active Directory [ramverket för medgivande.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Förutsättningar

När du beviljar administratörsmedgivande för hela klientorganisationen måste du logga in som en användare som har behörighet att godkänna för organisationens räkning. Detta inkluderar [global administratör](../roles/permissions-reference.md#global-administrator) och [privilegierad rolladministratör](../roles/permissions-reference.md#privileged-role-administrator)och, för vissa program, [programadministratör och](../roles/permissions-reference.md#application-administrator) [molnprogramadministratör.](../roles/permissions-reference.md#cloud-application-administrator) En användare kan också ges behörighet att bevilja medgivande [](../roles/custom-create.md) för hela klientorganisationen om de har tilldelats en anpassad katalogroll som innehåller behörighet att [bevilja behörigheter till program](../roles/custom-consent-permissions.md).

> [!WARNING]
> Genom att bevilja administratörsmedgivande för hela klientorganisationen till ett program beviljas appen och appens utgivare åtkomst till organisationens data. Granska noggrant de behörigheter som programmet begär innan du beviljar medgivande.

> [!IMPORTANT]
> När ett program har beviljats administratörsmedgivande för hela klientorganisationen kan alla användare logga in på appen om det inte har konfigurerats för att kräva användartilldelning. Om du vill begränsa vilka användare som kan logga in på ett program måste du kräva användartilldelning och sedan tilldela användare eller grupper till programmet. Mer information finns i [Metoder för att tilldela användare och grupper.](./assign-user-or-group-access-portal.md)

## <a name="grant-admin-consent-from-the-azure-portal"></a>Bevilja administratörsmedgivande från Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Bevilja administratörsmedgivande i Enterprise-appar

Du kan bevilja administratörsmedgivande för hela *klientorganisationen via Företagsprogram* om programmet redan har etablerats i din klientorganisation. En app kan till exempel etableras i din klientorganisation om minst en användare redan har samtyckt till programmet. Mer information finns i Hur [och varför program läggs till i Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Bevilja administratörsmedgivande för hela klientorganisationen till en app som anges i **Företagsprogram:**

1. Logga in på [Azure Portal](https://portal.azure.com) som [global administratör,](../roles/permissions-reference.md#global-administrator) [programadministratör](../roles/permissions-reference.md#application-administrator)eller [molnprogramadministratör.](../roles/permissions-reference.md#cloud-application-administrator)
2. Välj **Azure Active Directory** sedan **Företagsprogram**.
3. Välj det program som du vill ge administratörsmedgivande för hela klientorganisationen till.
4. Välj **Behörigheter** och klicka sedan på **Bevilja administratörsmedgivande.**
5. Granska noggrant de behörigheter som programmet kräver.
6. Om du accepterar de behörigheter som krävs av programmet beviljar du medgivande. Annars klickar du **på Avbryt** eller stänger fönstret.

> [!WARNING]
> Om du beviljar administratörsmedgivande för **hela klientorganisationen via** företagsappar återkallas alla behörigheter som tidigare har beviljats i hela klientorganisationen. Behörigheter som tidigare har beviljats av användare på egen hand påverkas inte. 

### <a name="grant-admin-consent-in-app-registrations"></a>Bevilja administratörsmedgivande i Appregistreringar

För program som din organisation har utvecklat eller som är registrerade direkt i din Azure AD-klientorganisation kan du också bevilja administratörsmedgivande för hela klientorganisationen **från** Appregistreringar i Azure Portal.

Bevilja administratörsmedgivande för hela klientorganisationen **från Appregistreringar:**

1. Logga in på [Azure Portal](https://portal.azure.com) som [global administratör,](../roles/permissions-reference.md#global-administrator) [programadministratör](../roles/permissions-reference.md#application-administrator)eller [molnprogramadministratör.](../roles/permissions-reference.md#cloud-application-administrator)
2. Välj **Azure Active Directory** och **Appregistreringar**.
3. Välj det program som du vill ge administratörsmedgivande för hela klientorganisationen till.
4. Välj **API-behörigheter** och klicka sedan på **Bevilja administratörsmedgivande.**
5. Granska noggrant de behörigheter som programmet kräver.
6. Om du accepterar de behörigheter som krävs av programmet beviljar du medgivande. Annars klickar du **på Avbryt** eller stänger fönstret.

> [!WARNING]
> Om du beviljar administratörsmedgivande **för hela Appregistreringar** återkallas alla behörigheter som tidigare hade beviljats i hela klientorganisationen. Behörigheter som tidigare har beviljats av användare på egen hand påverkas inte. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Skapa URL:en för att bevilja administratörsmedgivande för hela klientorganisationen

När du beviljar administratörsmedgivande för hela klientorganisationen med någon av metoderna som beskrivs ovan öppnas ett fönster från Azure Portal för att fråga efter administratörsmedgivande för hela klientorganisationen. Om du känner till klient-ID:t (även kallat program-ID) för programmet kan du skapa samma URL för att bevilja administratörsmedgivande för hela klientorganisationen.

URL:en för administratörsmedgivande för hela klientorganisationen har följande format:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

där:

* `{client-id}` är programmets klient-ID (även kallat app-ID).
* `{tenant-id}` är din organisations klientorganisations-ID eller ett verifierat domännamn.

Som alltid bör du noggrant granska de behörigheter som ett program begär innan du beviljar medgivande.

> [!WARNING]
> Om du beviljar administratörsmedgivande för hela klientorganisationen via den här URL:en återkallas alla behörigheter som tidigare hade beviljats i hela klientorganisationen. Behörigheter som tidigare har beviljats av användare på egen hand påverkas inte. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)

[Konfigurera arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md)

[Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)

[Azure AD på Microsoft Q&A](/answers/topics/azure-active-directory.html)
