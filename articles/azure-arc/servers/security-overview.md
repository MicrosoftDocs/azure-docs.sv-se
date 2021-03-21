---
title: Säkerhetsöversikt
description: Säkerhets information om Azure Arc-aktiverade servrar.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aa8653b783e7eb3e211b7514831604dd5642cfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117035"
---
# <a name="azure-arc-for-servers-security-overview"></a>Säkerhets översikt över Azure Arc för servrar

I den här artikeln beskrivs den säkerhets konfiguration och de överväganden du bör utvärdera innan du distribuerar Azure Arc-aktiverade servrar i företaget.

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

Varje Azure Arc-aktiverad server har en hanterad identitet som en del av en resurs grupp i en Azure-prenumeration, representerar den här identiteten servern som körs lokalt eller i en annan moln miljö. Åtkomst till den här resursen styrs av [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/overview.md). Från [**Access Control-sidan (IAM)**](../../role-based-access-control/role-assignments-portal.md) i Azure Portal kan du kontrol lera vem som har åtkomst till din Azure Arc-aktiverade Server.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Åtkomst kontroll för Azure Arc-aktiverad" border="false" lightbox="./media/security-overview/access-control-page.png":::

Användare och program som beviljas [deltagare](../../role-based-access-control/built-in-roles.md#contributor) eller administratörs roll åtkomst till resursen kan göra ändringar i resursen, inklusive att distribuera eller ta bort [tillägg](manage-vm-extensions.md) på datorn. Tillägg kan innehålla godtyckliga skript som körs i en privilegie rad kontext, vilket innebär att alla deltagare på Azure-resursen är en indirekt administratör på servern.

Den **Azure-anslutna datorn onboarding** -rollen är tillgänglig för storskalig onboarding och kan bara läsa eller skapa nya ARC-aktiverade servrar i Azure. Det går inte att använda den för att ta bort servrar som redan har registrerats eller hanterar tillägg. Vi rekommenderar att du bara tilldelar den här rollen till den Azure Active Directory (Azure AD) tjänstens huvud namn som används för att registrera datorer i stor skala.

Användare som medlem i rollen **resurs administratör för Azure Connected Machine** kan läsa, ändra, återregistrera och ta bort en dator. Den här rollen har utformats för att stödja hantering av Arc-aktiverade servrar, men inte andra resurser i resurs gruppen eller prenumerationen.

## <a name="agent-security-and-permissions"></a>Agent säkerhet och behörigheter

För att hantera Azure Connected Machine agent (azcmagent) i Windows måste ditt användar konto vara medlem i den lokala administratörs gruppen. I Linux måste du ha rot åtkomst behörighet.

Den Azure-anslutna dator agenten består av tre tjänster som körs på din dator.

* Hybrid Instance Metadata Services tjänsten (himds) ansvarar för alla kärn funktioner i Arc. Detta omfattar att skicka pulsslag till Azure, exponera en lokal instans av metadatatjänst för andra appar för att lära dig om datorns Azure-resurs-ID och hämta Azure AD-token för att autentisera till andra Azure-tjänster. Den här tjänsten körs som ett konto utan privilegier i Windows och som **himds** -användare på Linux.

* Gäst konfigurations tjänsten (GCService) ansvarar för utvärdering av Azure Policy på datorn.

* ExtensionService (Guest Configuration Extension Service) ansvarar för att installera, uppdatera och ta bort tillägg (agenter, skript eller annan program vara) på datorn.

Gäst konfiguration och tillägg tjänster körs som lokalt system i Windows och som rot på Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Använda en hanterad identitet med ARC-aktiverade servrar

Som standard kan Azure Active Directory system som tilldelats identiteten som används av Arc endast användas för att uppdatera statusen för den Arc-aktiverade servern i Azure. Till exempel den *senast visade* pulsslags statusen. Du kan också tilldela ytterligare roller till identiteten om ett program på servern använder den tilldelade identiteten för att få åtkomst till andra Azure-tjänster.

Även om hybrid Instance Metadata Service kan nås av ett program som körs på datorn, kan endast auktoriserade program begära en Azure AD-token för den tilldelade identiteten. Vid det första försöket att komma åt token URI genererar tjänsten en slumpmässigt genererad kryptografisk BLOB på en plats i fil systemet som endast betrodda anropare kan läsa. Anroparen måste sedan läsa filen (vilket bevisar att den har rätt behörighet) och göra om begäran med fil innehållet i Authorization-huvudet för att kunna hämta en Azure AD-token.

* I Windows måste anroparen vara medlem i den lokala gruppen **Administratörer** eller **program gruppen hybrid agent tillägg** för att läsa blobben.

* På Linux måste anroparen vara medlem i **himds** -gruppen för att kunna läsa blobben.

## <a name="using-disk-encryption"></a>Använda disk kryptering

Den Azure-anslutna dator agenten använder autentisering med offentlig nyckel för att kommunicera med Azure-tjänsten. När du har publicerat en server på Azure-bågen sparas en privat nyckel på disken och används när agenten kommunicerar med Azure. Om den blir stulen kan den privata nyckeln användas på en annan server för att kommunicera med tjänsten och agera som om den var den ursprungliga servern. Detta omfattar att få åtkomst till den tilldelade identiteten och de resurser som identiteten har åtkomst till. Den privata nyckel filen är skyddad för att endast tillåta åtkomst till **himds** -kontot för att läsa den. För att förhindra offline-attacker rekommenderar vi starkt att du använder fullständig disk kryptering (till exempel BitLocker, dm-crypt osv.) på serverns operativ system volym.

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar Arc-aktiverade servrar över flera hybrid datorer kan du läsa [Översikt över anslutna dator agenter](agent-overview.md) för att förstå krav, teknisk information om agenten och distributions metoder.
