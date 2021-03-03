---
title: Introduktion till att skydda Azure Active Directory tjänst konton
description: Förklaring av de typer av tjänst konton som är tillgängliga i Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 049025a5d871f1dd26e5dab498756aa44d2ebfe2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693287"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Introduktion till att skydda Azure-tjänstekonton

Det finns tre typer av tjänst konton som är inbyggda för Azure Active Directory: hanterade identiteter, tjänstens huvud namn och användarbaserade tjänst konton. Tjänst konton är en särskild typ av konto som är avsedd att representera en icke-mänsklig entitet, till exempel ett program, en API eller en annan tjänst. Dessa entiteter körs i säkerhets kontexten som tillhandahålls av tjänst kontot. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Typer av Azure Active Directory tjänst konton

För tjänster som är värd för Azure rekommenderar vi att du använder en hanterad identitet om möjligt och ett huvud namn för tjänsten. Hanterade identiteter kan inte användas för tjänster som ligger utanför Azure. I så fall rekommenderar vi ett huvud namn för tjänsten. Om du kan använda en hanterad identitet eller ett huvud namn för tjänsten gör du det. Vi rekommenderar att du inte använder ett Azure Active Directory-användarkonto som tjänstens huvud namn. I följande tabell finns en sammanfattning.
 

| Tjänst värd| Hanterad identitet| Tjänstens huvudnamn| Azure-användarkonto |
| - | - | - | - |
|Tjänsten finns i Azure.| Ja. <br>Rekommenderas om tjänsten <br>har stöd för en hanterad identitet.| Ja.| Rekommenderas ej. |
| Tjänsten finns inte i Azure.| Nej| Ja. Rekommenderas.| Rekommenderas ej. |
| Tjänsten är flera innehavare| Nej| Ja. Rekommenderas.| Nej. |


## <a name="managed-identities"></a>Hanterade identiteter

Hanterade identiteter är säkra Azure Active Directory (Azure AD)-identiteter som skapas för att tillhandahålla identiteter för Azure-resurser. Det finns [två typer av hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types): 
 
* Systemtilldelade hanterade identiteter kan tilldelas direkt till en instans av en tjänst. 

* Användare som tilldelats hanterade identiteter kan skapas som en fristående resurs. 

Mer information finns i [skydda hanterade identiteter](service-accounts-managed-identities.md). Allmän information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="service-principals"></a>Tjänstens huvudnamn

Om du inte kan använda en hanterad identitet för att representera ditt program, använder du ett huvud namn för tjänsten. Tjänstens huvud namn kan användas med både en klient och flera klient program. 

Ett huvud namn för tjänsten är den lokala åter givningen av ett program objekt i en enda Azure AD-klient. Den fungerar som identitet för program instansen, definierar vem som kan komma åt programmet och vilka resurser som programmet kan komma åt. Ett huvud namn för tjänsten skapas i (lokalt till) varje klient där programmet används och refererar till ett globalt unikt program objekt. Klienten skyddar tjänstens huvud namn för inloggning och åtkomst till resurser.

Det finns två mekanismer för autentisering med tjänstens huvud namn – klient certifikat och klient hemligheter. Certifikat är säkrare: Använd klient certifikat om möjligt. Till skillnad från klient hemligheter kan klient certifikat inte oavsiktligt bäddas in i kod.

Information om hur du skyddar tjänstens huvud namn finns i skydda tjänstens huvud namn.

 
## <a name="next-steps"></a>Nästa steg


Mer information om hur du skyddar Azure-tjänstekonton finns i:

[Skydda hanterade identiteter](service-accounts-managed-identities.md)

[Skydda tjänstens huvud namn](service-accounts-principal.md)

[Styrande Azure-tjänstekonton](service-accounts-governing-azure.md)



