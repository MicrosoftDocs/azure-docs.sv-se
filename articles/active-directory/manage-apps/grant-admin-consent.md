---
title: Bevilja administratörs medgivande för hela klienten till ett program – Azure AD
description: Lär dig hur du beviljar ett programs medgivande till ett program så att slutanvändare inte tillfrågas om medgivande vid inloggning till ett program.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 646c2216c3d71aa441d33dde0ab3e2ef7bb4fd89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101643566"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bevilja administratörsmedgivande för hela klientorganisationen till ett program

  Lär dig hur du beviljar administratörs medgivande för hela klienten till ett program. Den här artikeln innehåller olika sätt att åstadkomma detta.

Mer information om samtycker till program finns i [Azure Active Directory medgivande Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Förutsättningar

Om du beviljar administratörs medgivande för hela klienten måste du logga in som en användare som har behörighet att godkänna för organisationens räkning. Detta inkluderar [Global administratör](../roles/permissions-reference.md#global-administrator) och [administratör för privilegierade roller](../roles/permissions-reference.md#privileged-role-administrator)och, för vissa program, [program administratör](../roles/permissions-reference.md#application-administrator) och [moln program administratör](../roles/permissions-reference.md#cloud-application-administrator). En användare kan också auktoriseras för att bevilja ett överordnat medgivande om de tilldelas en [anpassad katalog roll](../roles/custom-create.md) som innehåller [behörighet att bevilja program behörigheter](../roles/custom-consent-permissions.md).

> [!WARNING]
> Om du beviljar administratörs medgivande för klient organisationer till ett program får appen och appens utgivare åtkomst till din organisations data. Noggrant granska de behörigheter som programmet begär innan medgivande beviljas.

> [!IMPORTANT]
> När ett program har beviljats administratörs medgivande kan alla användare logga in på appen om den inte har kon figurer ATS för att kräva användar tilldelning. För att begränsa vilka användare som kan logga in i ett program, kräver användar tilldelning och tilldelar sedan användare eller grupper till programmet. Mer information finns i [metoder för att tilldela användare och grupper](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Bevilja administratörs medgivande från Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Bevilja administrativt medgivande i företags program

Du kan bevilja klient organisationens godkännande genom *företags program* om programmet redan har etablerats i din klient organisation. En app kan till exempel tillhandahållas i din klient om minst en användare redan har samtyckt till programmet. Mer information finns i [hur och varför program läggs till i Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Så här beviljar du ett administrativt medgivande till en app som anges i **företags program**:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../roles/permissions-reference.md#global-administrator), [program administratör](../roles/permissions-reference.md#application-administrator)eller en [moln program administratör](../roles/permissions-reference.md#cloud-application-administrator).
2. Välj **Azure Active Directory** sedan **företags program**.
3. Välj det program som du vill bevilja klient organisationens administratörs medgivande till.
4. Välj **behörigheter** och klicka sedan på **bevilja administrativt medgivande**.
5. Granska de behörigheter som programmet kräver noggrant.
6. Om du samtycker till de behörigheter som krävs för programmet måste du bevilja medgivande. Annars klickar du på **Avbryt** eller Stäng fönstret.

> [!WARNING]
> Om du beviljar ett klient organisations medgivande via **företags program** återkallar du alla behörigheter som tidigare har beviljats för hela klient organisationen. Behörigheter som tidigare har beviljats av användare i ett eget ställe påverkas inte. 

### <a name="grant-admin-consent-in-app-registrations"></a>Bevilja administratörs medgivande i Appregistreringar

För program som din organisation har utvecklat, eller som har registrerats direkt i din Azure AD-klient, kan du även bevilja administratörs tillåtelse för hela klienten från **Appregistreringar** i Azure Portal.

Så här tilldelar du administratörs medgivande från klient organisationen från **Appregistreringar**:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../roles/permissions-reference.md#global-administrator), [program administratör](../roles/permissions-reference.md#application-administrator)eller en [moln program administratör](../roles/permissions-reference.md#cloud-application-administrator).
2. Välj **Azure Active Directory** sedan **Appregistreringar**.
3. Välj det program som du vill bevilja klient organisationens administratörs medgivande till.
4. Välj **API-behörigheter** och klicka sedan på **bevilja administrativt medgivande**.
5. Granska de behörigheter som programmet kräver noggrant.
6. Om du samtycker till de behörigheter som krävs för programmet måste du bevilja medgivande. Annars klickar du på **Avbryt** eller Stäng fönstret.

> [!WARNING]
> Om du beviljar ett klient organisations medgivande via **Appregistreringar** återkallar du alla behörigheter som tidigare har beviljats för hela klienten. Behörigheter som tidigare har beviljats av användare i ett eget ställe påverkas inte. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Skapa URL: en för att bevilja administratörs tillåtelse för hela klienten

När du beviljar administratörs medgivande för hela klienten med hjälp av någon av metoderna som beskrivs ovan, öppnas ett fönster från Azure Portal för att begära att klient organisationens administratörs medgivande. Om du känner till klient-ID: t (även kallat program-ID) för programmet kan du bygga samma URL för att bevilja administratörs medgivande för hela klienten.

URL: en för hela klient organisationens administratörs medgivande följer följande format:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

där:

* `{client-id}` är programmets klient-ID (även kallat app-ID).
* `{tenant-id}` är organisationens klient-ID eller ett verifierat domän namn.

Som alltid bör du noga granska de behörigheter som ett program begär innan medgivande beviljas.

> [!WARNING]
> Om du beviljar administratörs medgivande via den här URL: en så återkallar du alla behörigheter som tidigare har beviljats för hela klienten. Behörigheter som tidigare har beviljats av användare i ett eget ställe påverkas inte. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)

[Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)

[Behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

[Azure AD på Microsoft Q&A](/answers/topics/azure-active-directory.html)
