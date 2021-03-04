---
title: Använda Azure Defender för behållar register
description: Läs om hur du använder Azure Defender för behållar register för att söka igenom Linux-avbildningar i dina Linux-värdbaserade register
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ee4992e41e792b570d8937edfe31efb4c651d742
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100737"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Använd Azure Defender för containerregister för att genomsöka dina avbildningar efter säkerhetsrisker

På den här sidan förklaras hur du använder den inbyggda sårbarhets-skannern för att skanna behållar avbildningar som lagras i Azure Resource Manager-baserade Azure Container Registry.

När **Azure Defender för containerregister** är aktiverat genomsöks alla avbildningar du överför till registret omedelbart. Dessutom genomsöks även alla bilder som hämtas under de senaste 30 dagarna. 

När skannern rapporterar sårbarheter för att Security Center, Security Center visar resultaten och relaterad information som rekommendationer. Dessutom innehåller undersökningarna relaterad information, till exempel reparations steg, relevanta CVEs, CVSS resultat och mycket annat. Du kan visa identifierade sårbarheter för en eller flera prenumerationer eller för ett enskilt register.


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identifiera sårbarheter i avbildningar i Azure Container register 

Aktivera sårbarhets ökningar av avbildningar som lagras i Azure Resource Manager-baserade Azure Container Registry:

1. Aktivera **Azure Defender för behållar register** för din prenumeration. Security Center är nu redo att skanna bilder i dina register.

    >[!NOTE]
    > Den här funktionen debiteras per avbildning.

1. Avbildnings genomsökningar utlöses vid varje push-överföring eller import, och om avbildningen har tagits emot under de senaste 30 dagarna. 

    När genomsökningen är klar (vanligt vis efter cirka 2 minuter, men kan vara upp till 15 minuter), är avgöranden tillgängliga som Security Center rekommendationer.

1. [Visa och åtgärda brister enligt beskrivningen nedan](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identifiera sårbarheter i avbildningar i andra behållar register 

1. Använd ACR-verktygen för att hämta avbildningar till registret från Docker Hub eller Microsoft Container Registry.  När importen är klar genomsöks de importerade bilderna av Azure Defender. 

    Läs mer i [Importera behållar avbildningar till ett behållar register](../container-registry/container-registry-import-images.md)

    När genomsökningen är klar (vanligt vis efter cirka 2 minuter, men kan vara upp till 15 minuter), är avgöranden tillgängliga som Security Center rekommendationer.

1. [Visa och åtgärda brister enligt beskrivningen nedan](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Visa och åtgärda resultat

1. Om du vill visa resultaten går du till sidan **rekommendationer** . Om problem påträffas visas rekommendationen om **säkerhets risker i Azure Container Registry avbildningar bör åtgärdas**

    ![Rekommendation för att åtgärda problem ](media/monitor-container-security/acr-finding.png)

1. Välj rekommendationen. 

    Sidan med rekommendations information öppnas med ytterligare information. Den här informationen omfattar listan över register med sårbara avbildningar ("resurser som påverkas") och åtgärds stegen. 

1. Välj ett enskilt register för att se de databaser i den som har sårbara databaser.

    ![Välj ett register](media/monitor-container-security/acr-finding-select-registry.png)

    Sidan register information öppnas med listan över berörda databaser.

1. Välj en annan lagrings plats för att se de databaser som har sårbara avbildningar i den.

    ![Välj en lagrings plats](media/monitor-container-security/acr-finding-select-repository.png)

    Sidan databas information öppnas. Den visar de sårbara bilderna tillsammans med en utvärdering av avgörandenas allvarlighets grad.

1. Välj en avbildning för att se sårbarheterna.

    ![Välj bilder](media/monitor-container-security/acr-finding-select-image.png)

    Listan över resultat för den valda bilden öppnas.

    ![Lista över resultat](media/monitor-container-security/acr-findings.png)

1. Om du vill veta mer om att hitta kan du välja hitta. 

    Fönstret med information om resultat visas.

    [![Informations fönstret för resultat](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    I det här fönstret finns en detaljerad beskrivning av problemet och länkar till externa resurser som hjälper dig att minimera hoten.

1. Följ stegen i reparations avsnittet i det här fönstret.

1. När du har vidtagit de steg som krävs för att åtgärda säkerhets problemet ersätter du avbildningen i registret:

    1. Skicka den uppdaterade avbildningen. Detta utlöser en sökning. 
    
    1. Se sidan rekommendationer för rekommendationen "säkerhets risker i Azure Container Registry avbildningar bör åtgärdas". 
    
        Om rekommendationen fortfarande visas och den avbildning som du har hanterat fortfarande visas i listan över sårbara avbildningar, kontrol lera reparations stegen igen.

    1. När du är säker på att den uppdaterade avbildningen har flyttats, genomsökts och inte längre visas i rekommendationen, tar du bort den "gamla" sårbara avbildningen från registret.


## <a name="disable-specific-findings-preview"></a>Inaktivera vissa resultat (förhands granskning)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

Om du har ett organisations behov av att ignorera en sökning i, i stället för att åtgärda det, kan du inaktivera det. Inaktiverade resultat påverkar inte din säkra poäng eller genererar oönskad brus.

När en sökning matchar de kriterier som du har definierat i inaktiverade regler visas de inte i listan över resultat. Vanliga scenarier är:

- Inaktivera undersöknings resultat med allvarlighets grad under medel
- Inaktivera resultat som inte kan korrigeras
- Inaktivera resultat med CVSS poäng under 6,5
- Inaktivera undersöknings resultat med viss text i säkerhets kontrollen eller kategorin (till exempel "RedHat", "CentOS Security Update for sudo")

> [!IMPORTANT]
> Om du vill skapa en regel måste du ha behörighet att redigera en princip i Azure Policy.
>
> Läs mer i [Azure RBAC-behörigheter i Azure policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Du kan använda något av följande kriterier: 

- Söker efter ID 
- Kategori
- Säkerhets kontroll 
- CVSS v3-resultat
- Allvarlighetsgrad 
- Uppdaterings bara status 

Så här skapar du en regel:

1. På informations sidan rekommendationer för **sårbarheter i Azure Container Registry avbildningar ska åtgärdas** väljer du **Inaktivera regel**.
1. Välj relevant omfattning.
1. Definiera dina kriterier.
1. Välj **tillämpa regel**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Skapa en Inaktivera regel för VA-undersökningar i registret":::

1. Visa, åsidosätta eller ta bort en regel: 
    1. Välj **Inaktivera regel**.
    1. Från listan omfång visas prenumerationer med aktiva regler som **tillämpad regel**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Ändra eller ta bort en befintlig regel":::
    1. Om du vill visa eller ta bort regeln väljer du ellips-menyn ("...").


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure Defender](azure-defender.md)