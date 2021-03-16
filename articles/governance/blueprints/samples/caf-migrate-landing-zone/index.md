---
title: Exempel på skiss för CAF Migration-landningszon – översikt
description: Översikt över och arkitektur för Cloud Adoption Framework (CAF) for Azure Migration-landningszonskissexemplet.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 9cbf1c1a4ac4cf7a58ed27ec05cc77997da1366d
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472194"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Översikt över skissexemplet för Microsoft Cloud Adoption Framework for Azure Migration-landningszonen

Skissen för Microsoft Cloud Adoption Framework for Azure (CAF) Migration-landningszonen är en infrastrukturuppsättning som hjälper dig att förbereda migreringen av din första arbetsbelastning och att hantera din molnegendom i enlighet med CAF.

[CAF Foundation](../caf-foundation/index.md)-skissexemplet bygger vidare på det här exemplet.

## <a name="architecture"></a>Arkitektur

Skissexemplet för CAF Migration-landningszonen distribuerar grundläggande infrastrukturresurser i Azure som kan användas av organisationer till att förbereda sin prenumeration för migrering av virtuella datorer. Det hjälper även till att etablera de styrningskontroller som krävs för att hantera molnegendomen. Det här exemplet distribuerar och framtvingar resurser, principer och mallar som gör det möjligt för en organisation att på ett säkert sätt komma igång med Azure.

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="C A F Migration-landningszon, bild som beskriver vad som installeras som en del av C A F-vägledningen för den inledande landningszonen." border="false":::
   Beskriver en Azure-arkitektur som uppnås genom att man distribuerar C A F-migreringsskissen.  Det är tillämpligt för en prenumeration med resursgrupper som består av ett virtuellt Azure-nätverk, lagringskonto för lagring av loggar, Log Analytics som är konfigurerat för att lagras i lagringskontot.  Den visar också Azure Key Vault konfigurerat och Azure Migrates första installation skapad.  Alla dessa kärninfrastrukturer nås via Azure Active Directory.     
:::image-end:::

Den här miljön består av flera Azure-tjänster som används för att tillhandahålla säker, fullständigt övervakad och företagsfärdig styrning. Den här miljön består av:

- En [Azure Key Vault](../../../../key-vault/general/overview.md)-instans som värdhanterar de hemligheter som används för de certifikat, nycklar och hemligheter som distribueras i miljön för delade tjänster
- [Log Analytics](../../../../azure-monitor/overview.md) distribueras för att se till att alla åtgärder och tjänster loggas till en central plats direkt när du påbörjar din migrering
- Distribution av [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) tillhandahåller ett isolerat nätverk och undernät för din virtuella dator.
- Distribution av [Azure Migrate-projekt](../../../../migrate/migrate-services-overview.md) för identifiering och utvärdering. Vi lägger till verktygen för serverutvärdering, servermigrering, databasutvärdering samt databasmigrering.  


Alla dessa element följer beprövade metoder som finns publicerade i [Referensarkitekturer i Azure Architecture Center](/azure/architecture/reference-architectures/).

> [!NOTE]
> CAF Migration-skissen utgör en landningszon för dina arbetsbelastningar. Du behöver fortfarande utföra utvärderingen och migreringen av dina virtuella datorer/databaser ovanpå den här grundläggande arkitekturen.

Mer information finns i [Microsoft Cloud Adoption Framework for Azure – migrera](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Nästa steg

Du har läst översikten och arkitekturen för skissexemplet för CAF Migrate-landningszonen.

> [!div class="nextstepaction"]
> [Skiss för CAF Migration-landningszon – distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
