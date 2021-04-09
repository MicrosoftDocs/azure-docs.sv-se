---
title: Hanterade identiteter för Azure
description: Lär dig mer om att använda hanterade identiteter för Azure med Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881728"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Använda hanterade identiteter för Azure med Service Fabric

En vanlig utmaning när du bygger moln program är att på ett säkert sätt hantera autentiseringsuppgifterna i din kod för att autentisera till olika tjänster utan att spara dem lokalt på en arbets station för utvecklare eller i käll kontroll. *Hanterade identiteter för Azure* löser det här problemet för alla resurser i Azure Active Directory (Azure AD) genom att tillhandahålla dem automatiskt hanterade identiteter i Azure AD. Du kan använda en tjänst identitet för att autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan att några autentiseringsuppgifter lagras i din kod.

*Hanterade identiteter för Azure-resurser* är kostnads fria med Azure AD för Azure-prenumerationer. Ingen extra kostnad utgår.

> [!NOTE]
> *Hanterade identiteter för Azure* är det nya namnet på tjänsten som tidigare kallades HANTERAD TJÄNSTIDENTITET (MSI).

## <a name="concepts"></a>Begrepp

Hanterade identiteter för Azure baseras på flera viktiga begrepp:

- **Klient-ID** – en unik identifierare som genererats av Azure AD och som är kopplad till ett program och tjänstens huvud namn under dess första etablering (se även [program-ID](../active-directory/develop/developer-glossary.md#application-id-client-id).)

- **Huvud-ID** – objekt-ID för tjänstens huvud namn för din hanterade identitet som används för att bevilja rollbaserad åtkomst till en Azure-resurs.

- **Tjänstens huvud namn** – ett Azure Active Directory objekt som representerar projektionen av ett AAD-program i en specifik klient organisation (se [tjänstens huvud namn](../active-directory/develop/developer-glossary.md#service-principal-object)).

Det finns två typer av hanterade identiteter:

- En **systemtilldelad hanterad identitet** aktive ras direkt på en Azure-tjänstinstans.  Livs cykeln för en tilldelad identitet är unik för den Azure-tjänstinstans som den är aktive rad på.
- En **användartilldelad hanterad identitet** skapas som en fristående Azure-resurs. Identiteten kan tilldelas till en eller flera Azure Service-instanser och hanteras separat från instansernas livscykler.

Mer information om skillnaden mellan hanterade identitets typer finns i [Hur hanterar hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

## <a name="supported-scenarios-for-service-fabric-applications"></a>Scenarier som stöds för Service Fabric program

Hanterade identiteter för Service Fabric stöds bara i Azure-distribuerade Service Fabric-kluster och endast för program som distribueras som Azure-resurser. ett program som inte har distribuerats som en Azure-resurs kan inte tilldelas en identitet. Konceptuellt att tala, stöd för hanterade identiteter i ett Azure Service Fabric-kluster består av två faser:

1. Tilldela program resursen en eller flera hanterade identiteter. ett program kan tilldelas en enda tilldelad system identitet och/eller upp till 32 användarspecifika identiteter.

2. I programmets definition mappar du en av identiteterna som tilldelats programmet till en enskild tjänst som omfattar programmet.

Den systemtilldelade identiteten för ett program är unik för det programmet. en användardefinierad identitet är en fristående resurs som kan tilldelas till flera program. I ett program kan en enda identitet (om systemtilldelade eller tilldelade användare) tilldelas till flera tjänster i programmet, men varje enskild tjänst kan bara tilldelas en identitet. Slutligen måste en tjänst tilldelas en identitet som uttryckligen har åtkomst till den här funktionen. Mappningen av ett programs identiteter till komponenternas tjänster gör det möjligt för isolering i program – en tjänst kan bara använda den identitet som är mappad till den.  

För närvarande stöds följande scenarier för den här funktionen:

- Distribuera ett nytt program med en eller flera tjänster och en eller flera tilldelade identiteter

- Tilldela en eller flera hanterade identiteter till ett befintligt (Azure-distribuerat) program för att få åtkomst till Azure-resurser

Följande scenarier stöds inte eller rekommenderas inte. Observera att de här åtgärderna inte blockeras, men kan leda till avbrott i dina program:

- Ta bort eller ändra identiteter som har tilldelats till ett program. Om du måste göra ändringar måste du skicka in separata distributioner för att först lägga till en ny identitets tilldelning och sedan ta bort en tidigare tilldelad. Borttagning av en identitet från ett befintligt program kan ha oönskade effekter, inklusive att lämna programmet i ett tillstånd som inte kan uppgraderas. Det är säkert att ta bort programmet helt om det är nödvändigt att ta bort en identitet. OBS! detta tar bort den systemtilldelade identiteten (om den har definierats) som är associerad med programmet och tar bort alla associationer med de användardefinierade identiteter som tilldelats programmet.

- Service Fabric stöd för hanterade identiteter är inte integrerat för närvarande i [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication).

## <a name="next-steps"></a>Nästa steg

- [Distribuera ett nytt Azure Service Fabric-kluster med stöd för hanterad identitet](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Aktivera stöd för hanterad identitet i ett befintligt Azure Service Fabric-kluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Utnyttja den hanterade identiteten för ett Service Fabric program från service code](./how-to-managed-identity-service-fabric-app-code.md)
- [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
- [Deklarera och använda program hemligheter som KeyVaultReferences](./service-fabric-keyvault-references.md)