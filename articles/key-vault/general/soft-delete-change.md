---
title: Aktivera mjuk borttagning för alla nyckelvalvsobjekt – Azure Key Vault | Microsoft Docs
description: Använd det här dokumentet för att införa mjuk borttagning för alla nyckelvalv och för att göra ändringar i program och administration för att undvika konfliktfel.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 7e1b2ee95864affa6e5e72e1f8354767dc95bdb1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753334"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Mjuk borttagning aktiveras för alla nyckelvalv

> [!WARNING]
> Icke-ändring: Möjligheten att avanmäla sig från mjuk borttagning kommer snart att bli inaktuell. Azure Key Vault bör användare och administratörer aktivera mjuk borttagning för sina nyckelvalv omedelbart.
>
> För Azure Key Vault Managed HSM är mjuk borttagning aktiverat som standard och kan inte inaktiveras.

När en hemlighet tas bort från ett nyckelvalv utan skydd med mjuk borttagning tas hemligheten bort permanent. Användarna kan för närvarande välja bort mjuk borttagning när nyckelvalvet skapas. Microsoft kommer dock snart att aktivera skydd med mjuk borttagning för alla nyckelvalv för att skydda hemligheter från oavsiktlig eller skadlig borttagning av en användare. Användarna kommer inte längre att kunna välja bort eller inaktivera mjuk borttagning.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagram som visar hur ett nyckelvalv tas bort med skydd med mjuk borttagning jämfört med utan skydd mot mjuk borttagning.":::

Fullständig information om funktionen för mjuk borttagning finns i Azure Key Vault [översikt över mjuk borttagning.](soft-delete-overview.md)

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Kan mitt program fungera med mjuk borttagning aktiverat?

> [!Important] 
> Granska följande information noggrant innan du slår på mjuk borttagning för dina nyckelvalv.

Namn på nyckelvalv är globalt unika. Namnen på hemligheter som lagras i ett nyckelvalv är också unika. Du kommer inte att kunna återanvända namnet på ett nyckelvalv eller nyckelvalvsobjekt som finns i läget för mjuk borttagning. 

Om ditt program till exempel programmässigt skapar ett nyckelvalv med namnet "Valv A" och senare tar bort "Valv A" flyttas nyckelvalvet till läget för mjuk borttagning. Programmet kommer inte att kunna skapa ett nytt nyckelvalv med namnet "Valv A" förrän nyckelvalvet rensas från det mjukt borttagna tillståndet. 

Om ditt program skapar en nyckel med namnet i "Valv A" och senare tar bort nyckeln kan programmet inte skapa en ny nyckel med namnet i `test key` "Valv A" förrän objektet har tagits bort från det mjukt borttagna `test key` `test key` tillståndet. 

Om du försöker ta bort ett nyckelvalvsobjekt och skapa det på nytt med samma namn utan att rensa det från det mjukt borttagna tillståndet kan det först orsaka konfliktfel. De här felen kan leda till att dina program eller automatisering misslyckas. Kontakta utvecklingsteamet innan du gör följande nödvändiga program- och administrationsändringar. 

### <a name="application-changes"></a>Programändringar

Om programmet förutsätter att mjuk borttagning inte är aktiverat och förväntar sig att borttagna hemlighets- eller nyckelvalvsnamn är tillgängliga för omedelbar återanvändning, måste du göra följande ändringar i programlogiken.

1. Ta bort det ursprungliga nyckelvalvet eller hemligheten.
1. Rensa nyckelvalvet eller hemligheten i läget för mjuk borttagning.
1. Vänta tills rensningen har slutförts. Omedelbar återskapning kan resultera i en konflikt.
1. Skapa nyckelvalvet på nytt med samma namn.
1. Om åtgärden create fortfarande resulterar i ett namnkonfliktfel kan du försöka att återskapa nyckelvalvet igen. Azure DNS poster kan ta upp till 10 minuter att uppdatera i värsta fall.

### <a name="administration-changes"></a>Administrationsändringar

Säkerhetsobjekt som behöver åtkomst för att permanent ta bort hemligheter måste beviljas fler åtkomstprincipbehörigheter för att rensa hemligheterna och nyckelvalvet.

Inaktivera alla Azure-policyer på dina nyckelvalv som uppmanar till att mjuk borttagning ska inaktiveras. Du kan behöva eskalera det här problemet till en administratör som styr Vilka Azure-principer som tillämpas på din miljö. Om den här principen inte är inaktiverad kan du förlora möjligheten att skapa nya nyckelvalv i omfånget för den tillämpade principen.

Om din organisation omfattas av juridiska efterlevnadskrav och inte kan tillåta att borttagna nyckelvalv och hemligheter förblir i ett återställningsbart tillstånd under en längre tid, måste du justera kvarhållningsperioden för mjuk borttagning för att uppfylla organisationens standarder. Du kan konfigurera att kvarhållningsperioden ska vara mellan 7 och 90 dagar.

## <a name="procedures"></a>Procedurer

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Granska dina nyckelvalv för att kontrollera om mjuk borttagning är aktiverat

1. Logga in på Azure-portalen.
1. Sök efter **Azure Policy**.
1. Välj **Definitioner.**
1. Under **Kategori** väljer **Key Vault** i filtret.
1. Välj den **Key Vault ska ha mjuk borttagning aktiverad** princip.
1. Välj **Tilldela**.
1. Ange omfånget till din prenumeration.
1. Kontrollera att principens effekt är inställd på **Granska**.
1. Välj **Granska + skapa**. En fullständig genomsökning av din miljö kan ta upp till 24 timmar att slutföra.
1. I fönstret **Azure Policy** väljer du **Efterlevnad.**
1. Välj den princip som du har tillämpat.

Nu kan du filtrera och se vilka nyckelvalv som har mjuk borttagning aktiverat (kompatibla resurser) och vilka nyckelvalv som inte har mjuk borttagning aktiverat (icke-kompatibla resurser).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Aktivera mjuk borttagning för ett befintligt nyckelvalv

1. Logga in på Azure-portalen.
1. Sök efter ditt nyckelvalv.
1. Välj **Egenskaper** under **Inställningar.**
1. Under **Mjuk borttagning väljer** du alternativet Aktivera återställning av det här **valvet och dess** objekt.
1. Ange kvarhållningsperiod för mjuk borttagning.
1. Välj **Spara**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Bevilja behörigheter för rensning av åtkomstprincip till ett säkerhetsobjekt

1. Logga in på Azure-portalen.
1. Sök efter ditt nyckelvalv.
1. Välj **Åtkomstprinciper** under **Inställningar.**
1. Välj tjänstens huvudnamn som du vill bevilja åtkomst till.
1. Gå igenom varje listmeny under behörigheter **för Nyckel,** **Hemlighet** och Certifikat **tills** du ser **Privilegierade åtgärder.** Välj **behörigheten Rensa.**

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-this-change-affect-me"></a>Påverkar den här ändringen mig?

Om du redan har aktiverat mjuk borttagning eller om du inte tar bort och skapar nyckelvalvsobjekt med samma namn igen kommer du förmodligen inte att märka någon ändring i nyckelvalvet.

Om du har ett program som tar bort och skapar nyckelvalvsobjekt på nytt med samma namngivningskonventioner ofta, måste du göra ändringar i programlogiken för att upprätthålla förväntat beteende. Se avsnittet [Programändringar](#application-changes) i den här artikeln.

### <a name="how-do-i-benefit-from-this-change"></a>Hur gör jag för att dra nytta av den här ändringen?

Skydd mot mjuk borttagning ger din organisation ytterligare ett skyddslager mot oavsiktlig eller skadlig borttagning. Som nyckelvalvsadministratör kan du begränsa åtkomsten till både återställningsbehörigheter och rensningsbehörigheter.

Om en användare av misstag tar bort ett nyckelvalv eller en hemlighet kan du ge dem åtkomstbehörighet att själva återställa hemligheten utan att skapa en risk för att de permanent tar bort hemligheten eller nyckelvalvet. Den här självbetjäningsprocessen minimerar stilleståndstiden i din miljö och garanterar tillgängligheten för dina hemligheter.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hur gör jag för att ta reda på om jag behöver vidta åtgärder?

Följ stegen i avsnittet [Granska dina nyckelvalv för att kontrollera om mjuk borttagning är aktiverat i](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) den här artikeln. Den här ändringen påverkar alla nyckelvalv som inte har mjuk borttagning aktiverat.

### <a name="what-action-do-i-need-to-take"></a>Vilken åtgärd behöver jag vidta?

När du har bekräftat att du inte behöver göra ändringar i programlogiken aktiverar du mjuk borttagning för alla dina nyckelvalv.

### <a name="when-do-i-need-to-take-action"></a>När behöver jag vidta åtgärder?

För att säkerställa att dina program inte påverkas aktiverar du mjuk borttagning på dina nyckelvalv så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

- Kontakta oss om du har frågor om den här ändringen på [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Läs [översikten för mjuk borttagning.](soft-delete-overview.md)
