---
title: Konfigurera lokal rollbaserad åtkomst kontroll (lokal RBAC) för Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar Azure API för FHIR att använda en extern Azure AD-klient för data planet
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020519"
---
# <a name="configure-local-rbac-for-fhir"></a>Konfigurera lokal RBAC för FHIR 

Den här artikeln förklarar hur du konfigurerar Azure-API: et för FHIR att använda en extern, sekundär Azure Active Directory-klient för hantering av data Plans åtkomst. Använd endast det här läget om du inte kan använda den Azure Active Directory klient som är associerad med din prenumeration.

> [!NOTE]
> Om ditt FHIR service-dataplan har kon figurer ATS för att använda din primära Azure Active Directory klient som är associerad med din prenumeration [använder du Azure RBAC för att tilldela data Plans roller](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Lägg till tjänstens huvudnamn

Lokalt RBAC gör att du kan använda en extern Azure Active Directory-klient med din FHIR-Server. För att tillåta att det lokala RBAC-systemet kontrollerar grupp medlemskap i den här klienten, måste Azure API för FHIR ha ett huvud namn för tjänsten i klient organisationen. Tjänstens huvud namn skapas automatiskt i klienter som är kopplade till prenumerationer som har distribuerat Azure API för FHIR, men om klienten inte har någon prenumeration kopplad till sig måste en innehavaradministratör skapa tjänstens huvud namn med något av följande kommandon:

Använda `Az` PowerShell-modulen:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

Du kan också använda `AzureAd` PowerShell-modulen:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

eller så kan du använda Azure CLI:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Konfigurera lokal RBAC

Du kan konfigurera Azure-API: t för FHIR att använda en extern eller sekundär Azure Active Directory klient på bladet **autentisering** :

![Lokala RBAC-tilldelningar](media/rbac/local-rbac-guids.png).

Ange en giltig Azure Active Directory-klient i rutan myndighet. När klienten har verifierats ska rutan **tillåtna objekt-ID** vara aktive rad och du kan ange en lista över identitets objekt-ID: n. Dessa ID: n kan vara identitets objekt-ID: n för:

* En Azure Active Directory användare.
* Ett Azure Active Directory tjänstens huvud namn.
* En Azure Active Directory-säkerhetsgrupp.

Du kan läsa artikeln om hur du [hittar identitets objekt-ID: n](find-identity-object-ids.md) för mer information.

När du har angett de objekt-ID: n som krävs klickar du på **Spara** och väntar på att ändringar ska sparas innan du försöker komma åt data planet med de tilldelade användarna, tjänstens huvud namn eller grupper.

## <a name="caching-behavior"></a>Funktioner för cachelagring

Azure API för FHIR kommer att cachelagra beslut i upp till 5 minuter. Om du beviljar en användare åtkomst till FHIR-servern genom att lägga till dem i listan över tillåtna objekt-ID: n, eller om du tar bort dem från listan, bör du förvänta dig att det tar upp till fem minuter innan ändringar av behörigheter för att spridas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du tilldelar FHIR data plan-åtkomst med hjälp av en extern (sekundär) Azure Active Directory klient. Härnäst lär du dig mer om ytterligare inställningar för Azure API för FHIR:
 
>[!div class="nextstepaction"]
>[Ytterligare inställningar Azure API för FHIR](azure-api-for-fhir-additional-settings.md)
