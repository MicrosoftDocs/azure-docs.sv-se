---
title: Aktivera mjuk borttagning på alla Azure Key Vault | Microsoft Docs
description: Använd det här dokumentet för att tillämpa mjuk borttagning för alla nyckel valv.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: e512cccdbfdc56500fa7c69372ca38f59d3195c2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590094"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Mjuk borttagning aktive ras på alla nyckel valv

> [!WARNING]
> **Ändrad ändring**: möjligheten att välja bort mjuk borttagning kommer snart att vara inaktuell. Azure Key Vault användare och administratörer bör aktivera mjuk borttagning av nyckel valven direkt.
>
> För hanterad HSM är mjuk borttagning aktiverat som standard och kan inte inaktive ras.

När en hemlighet tas bort från ett nyckel valv utan mjuka borttagnings skydd tas hemligheten bort permanent. Användare kan för närvarande inte ta bort mjuk borttagning när nyckel valvet skapas, men för att skydda dina hemligheter från oavsiktlig eller skadlig borttagning av en användare, kommer Microsoft snart att aktivera mjuk borttagnings skydd på **alla** nyckel valv och användare kommer inte längre att kunna välja eller inaktivera mjuk borttagning.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<alternativ text>":::

Fullständig information om funktionerna för mjuk borttagning finns i [Azure Key Vault översikt över mjuk borttagning](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Kan mitt program fungera med mjuk borttagning aktiverat?

> [!Important] 
> **Läs igenom följande information noggrant innan du aktiverar mjuk borttagning för dina nyckel valv**

Key Vault namn är globalt unika. Namnen på hemligheter som lagras i ett nyckel valv är också unika. Du kan inte återanvända namnet på ett nyckel valv eller ett nyckel valvs objekt som finns i läget Soft Deleted. 

**Exempel #1** Om programmet program mässigt skapar ett nyckel valv med namnet "valv A" och senare tar bort "valv A". Nyckel valvet kommer att flyttas till läget Soft Deleted. Programmet kommer inte att kunna återskapa ett annat nyckel valv med namnet "valv A" förrän nyckel valvet har rensats från det överförbara borttagna läget. 

**Exempel #2** Om ditt program skapar en nyckel med namnet `test key` i Key Vault a, och senare tar bort nyckeln från valv a, kommer programmet inte att kunna skapa en ny nyckel som heter `test key` i Key Vault a förrän `test key` objektet rensas från det Soft Deleted-läget. 

Detta kan resultera i motstridiga fel om du försöker ta bort ett nyckel valvs objekt och återskapar det med samma namn utan att ta bort det från läget Soft-Deleted först. Detta kan orsaka att programmen eller automatiseringen Miss lyckas. Kontakta din dev-grupp innan du gör nödvändiga program-och administrations ändringar nedan. 

### <a name="application-changes"></a>Program ändringar

Om ditt program förutsätter att mjuk borttagning inte är aktiverat och förväntar att borttagna hemliga eller nyckel valv är tillgängliga för omedelbar åter användning, måste program logiken göra följande ändringar för att kunna tillämpa den här ändringen.

1. Ta bort det ursprungliga nyckel valvet eller hemligheten
2. Rensa nyckel valvet eller hemligheten i läget Soft-Deleted.
3. Vänta – omedelbar återskapande kan resultera i en konflikt.
4. Återskapa nyckel valvet med samma namn.
5. Implementera försök igen om åtgärden Skapa fortfarande resulterar i ett namn konflikt fel, kan det ta upp till 10 minuter innan DNS-poster uppdateras i värsta fall scenario.

### <a name="administration-changes"></a>Administrations ändringar

Säkerhets objekt som behöver åtkomst för att permanent ta bort hemligheter måste beviljas ytterligare åtkomst princip behörigheter för att rensa dessa hemligheter och nyckel valvet.

Om du har en Azure Policy på nyckel valven som bestämmer att mjuk borttagning är inaktive rad måste den här principen inaktive ras.  Du kan behöva eskalera det här problemet till en administratör som styr vilka Azure-principer som tillämpas på din miljö. Om den här principen inte är inaktive rad kan du förlora möjligheten att skapa nya nyckel valv i omfånget för den tillämpade principen.

Om din organisation omfattas av krav för juridisk efterlevnad och inte tillåter att borttagna nyckel valv och hemligheter behålls i ett återställnings Bart tillstånd, måste du under en längre tid ändra kvarhållningsperioden för mjuk borttagning, vilket kan konfigureras mellan 7 – 90 dagar för att uppfylla organisationens standarder.

## <a name="procedures"></a>Procedurer

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Granska dina nyckel valv för att kontrol lera om mjuk borttagning har Aktiver ATS

1. Logga in på Azure Portal.
2. Sök efter "Azure Policy".
3. Välj definitioner.
4. Under kategori väljer du Key Vault i filtret.
5. Välj principen "Key Vault ska ha mjuk borttagning aktive rad".
6. Klicka på tilldela.
7. Ange omfånget till din prenumeration.
8. Kontrol lera att principen har angetts till "audit".
9. Välj "granska + skapa".
10. Det kan ta upp till 24 timmar för en fullständig genomsökning av din miljö att slutföras.
11. Klicka på "efterlevnad" på bladet Azure Policy.
12. Välj den princip som du använde.

Nu bör du kunna filtrera och se vilka av dina nyckel valv som har mjuk borttagning aktiverat (kompatibla resurser) och vilka nyckel valv som inte har mjuk borttagning aktiverat (icke-kompatibla resurser).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Aktivera mjuk borttagning för ett befintligt nyckel valv

1. Logga in på Azure Portal.
2. Sök efter din Key Vault.
3. Välj "egenskaper" under Inställningar.
4. Under mjuk borttagning väljer du alternativ knappen som motsvarar "Aktivera återställning av det här valvet och dess objekt".
5. Ange kvarhållningsperiod för mjuk borttagning.
6. Välj "Spara".

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Bevilja behörighet att rensa åtkomst princip till ett säkerhets objekt

1. Logga in på Azure Portal.
2. Sök efter din Key Vault.
3. Välj "åtkomst principer" under Inställningar.
4. Välj det tjänst huvud namn som du vill ge åtkomst till.
5. Rulla ned till "Privileged Operations" och välj "Rensa"-behörighet för varje listruta under nyckel-, hemlighet-och certifikat behörigheter.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-this-change-affect-me"></a>Påverkar den här ändringen mig?

Om du redan har aktiverat mjuk borttagning eller om du inte tar bort och återskapar Key Vault-objekt med samma namn kommer du förmodligen inte att märka några ändringar i funktions sättet för Key Vault.

Om du har ett program som tar bort och återskapar nyckel valvs objekt med samma namn konventioner, måste du göra ändringar i program logiken för att bibehålla förväntat beteende. Mer information finns i avsnittet "Hur gör jag för att svara på att avbryta ändringar?" avsnittet ovan.

### <a name="how-do-i-benefit-from-this-change"></a>Hur gör jag för att fördelarna med den här ändringen?

Med det mjuka borttagnings skyddet får din organisation ytterligare ett skydds lager mot oavsiktlig eller skadlig borttagning. Som nyckel valvs administratör kan du begränsa åtkomsten till både Återställ behörigheter och rensa behörigheter.

Om en användare råkar ta bort ett nyckel valv eller en hemlighet, kan du ge dem åtkomst behörighet för att återställa hemligheten utan att skapa en risk att de tar bort hemligheten eller nyckel valvet permanent. Den här självbetjänings processen minimerar drift tiden i din miljö och garanterar tillgängligheten för dina hemligheter.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hur gör jag för att ta reda på om jag behöver vidta åtgärder?

Följ stegen ovan i avsnittet "procedur för att granska dina nyckel valv för att kontrol lera om Soft-Delete är aktiverat". Alla nyckel valv som inte har mjuk borttagning aktiverat kommer att påverkas av den här ändringen. Ytterligare verktyg för att granska kommer snart att vara tillgängliga och det här dokumentet kommer att uppdateras.

### <a name="what-action-do-i-need-to-take"></a>Vilken åtgärd behöver jag göra?

Kontrol lera att du inte behöver göra några ändringar i din program logik. När du har bekräftat att aktiverar du mjuk borttagning på alla nyckel valv.

### <a name="by-when-do-i-need-to-take-action"></a>När måste jag vidta åtgärder?

För att se till att dina program inte påverkas aktiverar du mjuk borttagning på nyckel valven så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

- Kontakta oss med eventuella frågor om den här ändringen på [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Läs [översikten över mjuk borttagning](soft-delete-overview.md)
