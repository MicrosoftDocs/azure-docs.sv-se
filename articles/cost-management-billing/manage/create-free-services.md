---
title: Skapa kostnadsfria tjänster med kostnadsfritt Azure-konto
description: Lär dig hur du skapar tjänster som ingår i det kostnadsfria Azure-kontot. Du kan skapa dessa tjänster i valfri region där de är tillgängliga.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: banders
ms.openlocfilehash: 15c97640ab1f4e862aab0aff4b3965822daafb2b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219832"
---
# <a name="create-services-included-with-azure-free-account"></a>Skapa tjänster som ingår i det kostnadsfria Azure-kontot

Under de första 30 dagarna efter att du har skapat ett kostnads fritt Azure-konto har du 200 USD I kredit i din fakturerings valuta som du kan använda på vilken tjänst som helst, förutom köp från tredje parts Marketplace. Den kostnadsfria krediten ger dig möjlighet att testa Azure genom att experimentera med olika nivåer och typer av Azure-tjänster. Om du under perioden använder tjänster eller Azure-resurser som inte är kostnadsfria, dras dessa avgifter från din kredit.

Om du inte har utnyttjat hela krediten när 30-dagarsperioden är slut, går den återstående delen förlorad. Efter de första 30 dagarna och upp till 12 månader efter registreringen kan du bara använda en begränsad mängd av *vissa tjänster* – inte alla Azure-tjänster är kostnadsfria. Om du uppgraderar innan de 30 dagarna har löpt ut och du har kredit kvar, kan du utnyttja resten av krediten med en ”betala per användning”-prenumeration under de återstående dagarna. Om du till exempel registrerar dig för det kostnadsfria kontot den 1 november och uppgraderar den 5 november, kan du använda din kredit med den nya ”betala per användning”-prenumerationen fram till och med den 30 november. 

Med det kostnadsfria Azure-kontot får du tillgång till en *bestämd mängd* kostnadsfria tjänster i 12 månader samt en uppsättning tjänster som alltid är kostnadsfria. Endast vissa nivåer av tjänster är tillgängliga kostnadsfritt och i begränsad mängd. Azure har till exempel många virtuella datorer som är avsedda för olika behov. Det kostnadsfria kontot ger bara tillgång till en typ av virtuell dator utan kostnad – B1S Burstable B-serien som kan användas i upp till 750 timmar per månad. Om du håller dig inom gränserna för det kostnadsfria kontot kan du använda de kostnadsfria tjänsterna i olika konfigurationer. Mer information om det kostnadsfria Azure-kontot och vilka produkter som är tillgängliga utan kostnad finns i avsnittet med [vanliga frågor och svar om det kostnadsfria Azure-kontot](https://azure.microsoft.com/free/free-account-faq/).

## <a name="create-free-services-in-the-azure-portal"></a>Skapa kostnadsfria tjänster i Azure-portalen

Vi rekommenderar att du skapar kostnadsfria tjänster på [sidan för kostnadsfria tjänster](https://go.microsoft.com/fwlink/?linkid=859151) på Azure-portalen. Eller så kan du logga in i [Azure-portalen](https://portal.azure.com) och söka efter **kostnadsfria tjänster**. Om du inte skapar resurser på sidorna för kostnadsfria tjänster är det inte säkert att konfigurationsalternativen för kostnadsfria nivåer och resurser tillämpas automatiskt. Skapa resurser på sidan för kostnadsfria tjänster för att undvika avgifter. När du sedan skapar en resurs ser du till att välja den nivå som är kostnadsfri.

![Skärmbild som visar sidan med kostnadsfria tjänster](./media/create-free-services/billing-freeservices-grid.png)

## <a name="services-can-be-created-in-any-region"></a>Tjänster kan skapas i vilken region som helst

Så länge som du är inom gränserna kan du skapa tjänster kostnadsfritt i valfri region där tjänsterna är tillgängliga. Du får till exempel 750 timmar med en virtuell B1S Windows-dator kostnadsfritt varje månad med det kostnadsfria Azure-kontot. Du kan skapa den virtuella datorn i valfri region där virtuella datorer i B-serien är tillgängliga. Azure debiterar inte dig såvida du inte överskrider 750 timmar. Till exempel kan en kund i USA etablera en virtuell B1S Windows-dator i Europa, västra och använda den i 750 timmar utan kostnad.

Information om tillgänglighet för Azure-tjänster per region finns i [Produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).

## <a name="create-multiple-service-instances-in-allowed-limits"></a>Skapa flera tjänstinstanser inom tillåtna gränser

Du kan skapa flera instanser av tjänster utan kostnad, förutsatt att den sammanlagda förbrukningen inte överskrider användningsgränsen. Du får till exempel 750 timmar med en virtuell B1S Windows-dator kostnadsfritt varje månad med ditt kostnadsfria Azure-konto. Använd 750 timmar i valfri kombination. Du kan skapa 5 virtuella B1S Windows-datorer och använda dem i 150 timmar vardera.

## <a name="next-steps"></a>Nästa steg

- Läs mer om att [Kontrollera användningen av kostnadsfria tjänster som ingår i ditt kostnadsfria Azure-konto](check-free-service-usage.md).
- Lär dig hur du [undviker att debiteras för ditt kostnadsfria Azure-konto](avoid-charges-free-account.md).
