---
title: Hantera användar data i Azure Security Center | Microsoft Docs
description: Lär dig hur du hanterar användar data i Azure Security Center. Att hantera användar data innebär att du kan komma åt, ta bort eller exportera data.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595652"
---
# <a name="manage-user-data-in-azure-security-center"></a>Hantera användardata i Azure Security Center
Den här artikeln innehåller information om hur du kan hantera användar data i Azure Security Center. Att hantera användar data innebär att du kan komma åt, ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

En Security Center användare som har tilldelats rollen läsare, ägare, deltagare eller konto administratör kan komma åt kund information i verktyget. Läs mer om rollen konto administratör i [inbyggda roller för rollbaserad åtkomst kontroll i Azure](../role-based-access-control/built-in-roles.md) om du vill veta mer om rollerna läsare, ägare och deltagare. Se [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Söker efter och identifierar personuppgifter
En Security Center användare kan visa sina personliga data via Azure Portal. Security Center lagrar endast säkerhets kontakt information, till exempel e-postadresser och telefonnummer. Mer information finns i [Ange säkerhets kontakt uppgifter i Azure Security Center](security-center-provide-security-contact-details.md).

I Azure Portal kan en användare Visa tillåtna IP-konfigurationer med hjälp av Security Centers just-in-Time-funktionen för VM-åtkomst. Mer information finns i [Hantera åtkomst till virtuella datorer med just-in-Time](security-center-just-in-time.md).

I Azure Portal kan en användare se säkerhets aviseringar som tillhandahålls av Security Center inklusive information om IP-adresser och angripare. Mer information finns i [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klassificera person uppgifter
Du behöver inte klassificera person uppgifter som finns i Security Centerens säkerhets kontakt funktion. De data som sparas är en e-postadress (eller flera e-postadresser) och ett telefonnummer. [Kontakt data](security-center-provide-security-contact-details.md) verifieras av Security Center.

Du behöver inte klassificera IP-adresserna och port numren som sparats med Security Center [just-in-Time-](security-center-just-in-time.md) funktionen.

Endast en användare som har tilldelats rollen administratör kan klassificera personliga data genom att [Visa aviseringar](security-center-managing-and-responding-alerts.md) i Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Skydda och kontrol lera åtkomst till personliga data
En Security Center användare som har tilldelats rollen som läsare, ägare, deltagare eller konto administratör kan komma åt [säkerhets kontakt data](security-center-provide-security-contact-details.md).

En Security Center användare som tilldelats rollen läsare, ägare, deltagare eller konto administratör kan komma åt sina [just-in-Time-](security-center-just-in-time.md) principer.

En Security Center användare som tilldelats rollen läsare, ägare, deltagare eller konto administratör kan se sina [aviseringar](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Uppdatera personliga data
En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan uppdatera [säkerhets kontakt data](security-center-provide-security-contact-details.md) via Azure Portal.

En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan uppdatera sina [just-in-Time-principer](security-center-just-in-time.md).

En konto administratör kan inte redigera varnings incidenter. En [varnings incident](security-center-managing-and-responding-alerts.md) betraktas som säkerhets data och är skrivskyddad.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan ta bort [säkerhets kontakt data](security-center-provide-security-contact-details.md) via Azure Portal.

En Security Center användare som tilldelats rollen ägare, deltagare eller konto administratör kan ta bort [just-in-Time-principerna](security-center-just-in-time.md) via Azure Portal.

En Security Center användare kan inte ta bort varnings incidenter. Av säkerhets skäl betraktas en [varnings incident](security-center-managing-and-responding-alerts.md) som skrivskyddade data.

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center användare som tilldelats rollen läsare, ägare, deltagare eller konto administratör kan exportera [säkerhets kontakt data](security-center-provide-security-contact-details.md) genom att:

- Kopierar från Azure Portal
- Kör Azure REST API-anropet, Hämta HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

En Security Center användare som tilldelats rollen som konto administratör kan exportera [just-in-Time-principerna](security-center-just-in-time.md) som innehåller IP-adresserna genom att:

- Kopierar från Azure Portal
- Kör Azure REST API-anropet, Hämta HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

En konto administratör kan exportera aviserings informationen genom att:

- Kopierar från Azure Portal
- Kör Azure REST API-anropet, Hämta HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Mer information finns i [Hämta säkerhets aviseringar (Hämta samling)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Begränsa användningen av person uppgifter för profilering eller marknadsföring utan medgivande
En Security Center användare kan välja att avanmäla sig genom att ta bort sina [säkerhets kontakt data](security-center-provide-security-contact-details.md).

[Just-in-Time-data](security-center-just-in-time.md) betraktas som icke-identifierbara data och bevaras under en period på 30 dagar.

[Aviserings data](security-center-managing-and-responding-alerts.md) anses vara säkerhets data och bevaras under en period av två år.

## <a name="auditing-and-reporting"></a>Granskning och rapportering
Gransknings loggar för säkerhets kontakt, just-in-Time och aviserings uppdateringar underhålls i [Azure aktivitets loggar](../azure-monitor/essentials/platform-logs-overview.md).