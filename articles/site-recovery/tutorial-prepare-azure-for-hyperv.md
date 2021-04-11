---
title: Förbered Azure för haveri beredskap för Hyper-V med Azure Site Recovery
description: Lär dig hur du förbereder Azure för haveri beredskap för lokala virtuella Hyper-V-datorer med hjälp av Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.custom: MVC
ms.openlocfilehash: ec1c41935d200ad220716940f898549d835510ba
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579287"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Förbereda Azure-resurser för haveri beredskap för Hyper-V

 [Azure Site Recovery](site-recovery-overview.md) hjälper affärs kontinuitet och haveri beredskap (BCDR) genom att köra affärsappar som körs under planerade och oplanerade drifts avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

Den här självstudien är den första i en serie som beskriver hur du konfigurerar haveri beredskap för lokala virtuella Hyper-V-datorer.

> [!NOTE]
> Vi utformar självstudier för att visa den enklaste distributions Sök vägen för ett scenario. De här självstudierna använder standard alternativ när det är möjligt och visar inte alla möjliga inställningar och sökvägar. Mer information finns i avsnittet "så här" för varje motsvarande scenario.

Den här självstudien visar hur du förbereder Azure-komponenter när du vill replikera lokala virtuella datorer (Hyper-V) till Azure. Du lär dig följande:

> [!div class="checklist"]
> * Kontrollera att ditt Azure-konto har replikeringsbehörighet.
> * Skapa ett Azure Storage-konto som lagrar avbildningar av replikerade datorer.
> * Skapa ett Recovery Services-valv som lagrar metadata och konfigurations information för virtuella datorer och andra replikeringspartner.
> * Skapa ett Azure-nätverk. När virtuella Azure-datorer skapas efter en redundansväxling är de anslutna till det här nätverket.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="sign-in"></a>Logga in

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verifiera kontobehörighet

Om du precis har skapat ett kostnads fritt Azure-konto är du administratör för den prenumerationen. Om du inte är administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator måste du ha behörighet för att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriva till det valda lagringskontot.

För att slutföra dessa uppgifter bör ditt konto tilldelas den inbyggda rollen för den virtuella datorn Contributor. Om du vill hantera Site Recovery åtgärder i ett valv måste ditt konto tilldelas den inbyggda rollen Site Recovery Contributor.

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure-lagringen. Virtuella Azure-datorer skapas från minnet vid redundansväxling från lokalt till Azure. Lagringskontot måste finnas i samma region som Recovery Services-valvet.

1. På [Azure Portal](https://portal.azure.com) -menyn väljer du **skapa ett** lagrings konto för resurs  >  **lagring**  >  **-BLOB, fil, tabell, kö**.
2. I **Skapa lagringskonto** anger du ett namn för kontot.  Det namn du väljer måste vara unikt inom Azure, vara mellan 3 och 24 tecken långt och endast använda gemener och siffror. I den här självstudien använder du **contosovmsacct1910171607**.
3. För **Distributionsmodell** väljer du **Resource Manager**.
4. I **typ av konto** väljer du **lagring (generell användning v1)**. Välj inte blobblagring.
5. I **Replikering** väljer du standardinställningen **Read-access geo-redundant storage** för lagringsredundans.
6. I **Prestanda** väljer du **Standard**. Välj sedan standard alternativet **het** i **åtkomst nivå**.
7. I **prenumeration** väljer du den prenumeration där du vill skapa det nya lagrings kontot.
8. Ange ett nytt namn på resursgruppen i **Resursgrupp**. En Azure-resurs grupp är en logisk behållare där Azure-resurser distribueras och hanteras. I den här självstudien använder du **conto sorg**.
9. I **plats** väljer du den geografiska platsen för ditt lagrings konto. I den här självstudien använder du **Västeuropa**.
10. Skapa lagringskontot genom att välja **Skapa**.

   ![Skärm bild av alternativen för att skapa ett lagrings konto.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. I Azure Portal väljer du **+ skapa en resurs** och söker sedan efter Recovery Services på Azure Marketplace.
2. Välj **backup och Site Recovery (OMS)**. Sedan väljer du **skapa** på sidan **säkerhets kopiering och Site Recovery** .
1. I **Recovery Services-valv > namn** anger du ett eget namn som identifierar valvet. I den här självstudien använder du **ContosoVMVault**.
2. I **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. I den här självstudien använder du **conto sorg**.
3. I **plats** väljer du den region där valvet ska placeras. I den här självstudien använder du **Västeuropa**.
4. Om du snabbt vill komma åt valvet från instrument panelen väljer du **Fäst på instrument panelen**  >  **skapa**.

![Skärm bild av sidan Skapa Recovery Services valv.](./media/tutorial-prepare-azure/new-vault-settings.png)

Det nya valvet visas på **instrument panelen**  >  **alla resurser** och på huvud sidan **Recovery Services valv** .

## <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna har skapats från minnet efter redundansen, är de anslutna till det här nätverket.

1. I [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**. Låt Resource Manager vara valt som distributionsmodell.
2. I **Namn** anger du ett nätverksnamn. Namnet måste vara unikt inom Azure-resursgruppen. I den här självstudien använder du **ContosoASRnet**.
3. Ange den resurs grupp där du vill skapa nätverket. I den här självstudien använder du den befintliga resurs gruppen **conto sorg**.
4. I **adress intervall** anger du **10.0.0.0/24** som intervall för nätverket. Det finns inget undernät för det här nätverket.
5. I **Prenumeration** väljer du den prenumeration där du vill skapa nätverket.
6. I **plats** väljer du **Europa, västra**. Nätverket måste finnas i samma region som Recovery Services-valvet.
7. Låt standard alternativen för grundläggande DDoS-skydd vara kvar, utan tjänst slut punkt i nätverket.
8. Välj **Skapa**.

![Skärm bild av alternativen för att skapa virtuella nätverk.](media/tutorial-prepare-azure/create-network.png)

Det tar några sekunder att skapa ditt virtuella nätverk. När den har skapats visas den i Azure Portal instrument panelen.

## <a name="useful-links"></a>Användbara länkar

Läs om:
- [Azure-nätverk](../virtual-network/virtual-networks-overview.md)
- [Hanterade diskar](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förbered den lokala Hyper-V-infrastrukturen för haveri beredskap till Azure](hyper-v-prepare-on-premises-tutorial.md)
