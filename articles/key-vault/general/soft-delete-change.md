---
title: Aktivera mjuk borttagning på alla Key Vault-objekt – Azure Key Vault | Microsoft Docs
description: Använd det här dokumentet för att tillämpa mjuk borttagning för alla nyckel valv och för att göra program-och administrations ändringar för att undvika konflikter.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572875"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Mjuk borttagning aktive ras på alla nyckel valv

> [!WARNING]
> Ändrad ändring: möjligheten att välja bort mjuk borttagning kommer snart att vara inaktuell. Azure Key Vault användare och administratörer bör aktivera mjuk borttagning av nyckel valven direkt.
>
> För Azure Key Vault hanterad HSM är mjuk borttagning aktiverat som standard och kan inte inaktive ras.

När en hemlighet tas bort från ett nyckel valv utan mjuka borttagnings skydd tas hemligheten bort permanent. Användare kan för närvarande inte välja mjuk borttagning när nyckel valvet skapas. Microsoft kommer dock snart att aktivera mjuk borttagnings skydd på alla nyckel valv för att skydda hemligheter från oavsiktlig eller skadlig borttagning av en användare. Användarna kommer inte längre att kunna välja bort eller inaktivera mjuk borttagning.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagram över hur ett nyckel valv tas bort med mjuk borttagnings skydd jämfört med utan borttagning av mjuka rader.":::

Fullständig information om funktionerna för mjuk borttagning finns i [Azure Key Vault översikt över mjuk borttagning](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Kan mitt program fungera med mjuk borttagning aktiverat?

> [!Important] 
> Granska följande information noggrant innan du aktiverar mjuk borttagning för dina nyckel valv.

Nyckel valvs namn är globalt unika. Namnen på hemligheter som lagras i ett nyckel valv är också unika. Du kan inte återanvända namnet på ett nyckel valv eller ett nyckel valvs objekt som finns i läget Soft-Deleted. 

Om ditt program till exempel skapar ett nyckel valv med namnet "valv A" och senare tar bort "valv A", kommer nyckel valvet att flyttas till läget Soft-Deleted. Programmet kommer inte att kunna återskapa ett annat nyckel valv med namnet "valv A" förrän nyckel valvet har rensats bort från det överförbara borttagna läget. 

Om ditt program skapar en nyckel med namnet `test key` "valv a" och senare tar bort nyckeln, kommer programmet inte att kunna skapa en ny nyckel med namnet `test key` "valv a" förrän `test key` objektet har rensats bort från det överförbara borttagna läget. 

Om du försöker ta bort ett nyckel valvs objekt och skapar det igen med samma namn utan att ta bort det från det Soft-borttagna läget kan det leda till motstridiga fel. De här felen kan orsaka att programmen eller automatiseringen Miss fungerar. Kontakta din dev-grupp innan du gör följande nödvändiga program-och administrations ändringar. 

### <a name="application-changes"></a>Program ändringar

Om ditt program förutsätter att mjuk borttagning inte är aktiverat och förväntar dig att borttagna hemliga eller nyckel valv är tillgängliga för omedelbar åter användning, måste du göra följande ändringar i din program logik.

1. Ta bort det ursprungliga nyckel valvet eller hemligheten.
1. Rensa nyckel valvet eller hemligheten i läget Soft-Deleted.
1. Vänta tills rensningen har slutförts. Att omedelbart skapa igen kan resultera i en konflikt.
1. Återskapa nyckel valvet med samma namn.
1. Om åtgärden Skapa fortfarande resulterar i ett namn konflikt fel, kan du försöka återskapa nyckel valvet igen. Det kan ta upp till 10 minuter att uppdatera Azure DNS poster i värsta fall scenariot.

### <a name="administration-changes"></a>Administrations ändringar

Säkerhets objekt som behöver åtkomst för att permanent ta bort hemligheter måste beviljas fler åtkomst princip behörigheter för att rensa dessa hemligheter och nyckel valvet.

Inaktivera eventuella Azure-principer på nyckel valven som bestämmer att mjuk borttagning är inaktive rad. Du kan behöva eskalera det här problemet till en administratör som kontrollerar vilka Azure-principer som tillämpas på din miljö. Om den här principen inte är inaktive rad kan du förlora möjligheten att skapa nya nyckel valv i omfånget för den tillämpade principen.

Om din organisation omfattas av juridiska krav för efterlevnad och det inte går att tillåta att borttagna nyckel valv och hemligheter behålls i ett återställnings Bart tillstånd under en längre tid, måste du justera kvarhållningsperioden för mjuk borttagning för att uppfylla organisationens standarder. Du kan konfigurera kvarhållningsperioden till senast 7 till 90 dagar.

## <a name="procedures"></a>Procedurer

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Granska dina nyckel valv för att kontrol lera om mjuk borttagning har Aktiver ATS

1. Logga in på Azure-portalen.
1. Sök efter **Azure policy**.
1. Välj **definitioner**.
1. Under **kategori** väljer du **Key Vault** i filtret.
1. Välj **Key Vault ska ha principen mjuk borttagnings aktive rad** .
1. Välj **Tilldela**.
1. Ange omfånget till din prenumeration.
1. Kontrol lera att principen är inställd på **granskning**.
1. Välj **Granska + skapa**. En fullständig genomsökning av din miljö kan ta upp till 24 timmar att slutföra.
1. I fönstret **Azure policy** väljer du **efterlevnad**.
1. Välj den princip som du använde.

Nu kan du filtrera och se vilka nyckel valv som har mjuk borttagning aktiverat (kompatibla resurser) och vilka nyckel valv som inte har mjuk borttagning aktiverat (icke-kompatibla resurser).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Aktivera mjuk borttagning för ett befintligt nyckel valv

1. Logga in på Azure-portalen.
1. Sök efter ditt nyckel valv.
1. Välj **Egenskaper** under **Inställningar**.
1. Under **mjuk borttagning** väljer du alternativet **Aktivera återställning av det här valvet och dess objekt** .
1. Ange kvarhållningsperiod för mjuk borttagning.
1. Välj **Spara**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Bevilja behörighet att rensa åtkomst princip till ett säkerhets objekt

1. Logga in på Azure-portalen.
1. Sök efter ditt nyckel valv.
1. Välj **åtkomst principer** under **Inställningar**.
1. Välj det tjänst huvud namn som du vill bevilja åtkomst till.
1. Gå igenom varje nedrullningsbar meny under **nyckel**-, **hemlighet**-och **certifikat behörigheter** tills du ser **privilegierade åtgärder**. Välj behörigheten **Rensa** .

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-this-change-affect-me"></a>Påverkar den här ändringen mig?

Om du redan har aktiverat mjuk borttagning eller om du inte tar bort och återskapar nyckel valvs objekt med samma namn, kommer du troligen inte att upptäcka några ändringar i funktions sättet för nyckel valvet.

Om du har ett program som tar bort och återskapar nyckel valvs objekt med samma namn konventioner, måste du göra ändringar i program logiken för att bibehålla förväntat beteende. Se avsnittet [program ändringar](#application-changes) i den här artikeln.

### <a name="how-do-i-benefit-from-this-change"></a>Hur gör jag för att fördelarna med den här ändringen?

Skydd mot mjuka borttagning ger din organisation ett annat skydds lager mot oavsiktlig eller skadlig borttagning. Som nyckel valvs administratör kan du begränsa åtkomsten till både Återställ behörigheter och rensa behörigheter.

Om en användare råkar ta bort ett nyckel valv eller en hemlighet, kan du ge dem åtkomst behörighet för att återställa hemligheten utan att skapa risken att de tar bort hemligheten eller nyckel valvet permanent. Den här självbetjänings processen minimerar drift stopp i din miljö och garanterar tillgängligheten för dina hemligheter.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hur gör jag för att ta reda på om jag behöver vidta åtgärder?

Följ stegen i avsnittet [Granska dina nyckel valv för att kontrol lera om mjuk borttagning har Aktiver ATS](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) i den här artikeln. Den här ändringen kommer att påverka eventuella nyckel valv som inte har mjuk borttagning aktiverat.

### <a name="what-action-do-i-need-to-take"></a>Vilken åtgärd behöver jag göra?

När du har bekräftat att du inte behöver göra ändringar i program logiken aktiverar du mjuk borttagning på alla nyckel valv.

### <a name="when-do-i-need-to-take-action"></a>När behöver jag vidta åtgärder?

För att se till att dina program inte påverkas aktiverar du mjuk borttagning på nyckel valven så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

- Kontakta oss med frågor om den här ändringen på [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Läs [översikten över mjuk borttagning](soft-delete-overview.md).
