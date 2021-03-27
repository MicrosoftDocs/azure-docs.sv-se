---
title: Skydda en länkad tjänst
description: Lär dig hur du etablerar och skyddar en länkad tjänst med hanterat VNet
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: be7ee2f4b2b8472c5edeff63e143d99c958bfc5a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627225"
---
# <a name="secure-a-linked-service-with-private-links"></a>Skydda en länkad tjänst med privata länkar

I den här artikeln får du lära dig hur du skyddar en länkad tjänst i Synapse med en privat slut punkt.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage konto**: du använder Azure Data Lake gen 2 som *käll* data lager. Om du inte har ett lagrings konto kan du läsa [skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md) för att skapa ett. Kontrol lera att lagrings kontot har Synapse Studio IP-filtrering för att komma åt det och att du bara tillåter **valda nätverk** att komma åt lagrings kontot. Inställningen under bladets **brand väggar och virtuella nätverk** bör se ut som på bilden nedan.

![Skyddat lagrings konto](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Skapa en länkad tjänst med privata länkar

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutnings information till andra tjänster. I det här avsnittet ska du lägga till Azure Synapse Analytics och Azure Data Lake gen 2 som länkade tjänster.

1. Öppna Azure Synapse Studio och gå till fliken **Hantera** .
1. Under **externa anslutningar** väljer du **länkade tjänster**.
1. Om du vill lägga till en länkad tjänst väljer du **ny**.
1. Välj panelen Azure Data Lake Storage Gen2 i listan och välj **Fortsätt**.
1. Se till att aktivera **interaktiv redigering**. Det kan ta cirka 1 minut att aktive ras. 
1. Ange autentiseringsuppgifterna för autentisering. Konto nyckel, tjänstens huvud namn och hanterad identitet stöds för närvarande autentiseringstyper som stöds. Kontrol lera att dina autentiseringsuppgifter är korrekta genom att välja testa anslutning.
1. Välj **test anslutning**. det bör inte fungera eftersom lagrings kontot inte tillåter åtkomst till det utan att en privat slut punkt skapas och godkänns. I fel meddelandet bör du se en länk för att skapa en **privat slut punkt** som du kan följa för att gå till nästa del. Om du följer den länken hoppar du över nästa del.
1. Välj **Skapa** när du är klar.

## <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte har valt att använda hyperlänken när du testar anslutningen ovan följer du följande sökväg. Skapa en hanterad privat slut punkt som du ansluter till den länkade tjänsten som skapats ovan.

1. Gå till fliken **Hantera** .
1. Gå till avsnittet **hanterade virtuella nätverk** .
1. Välj **+ ny** under hanterad privat slut punkt.
1. Välj panelen Azure Data Lake Storage Gen2 i listan och välj **Fortsätt**.
1. Ange namnet på det lagrings konto som du skapade ovan.
1. Välj **Skapa**
1. Du bör se efter att ha väntat några sekunder på att den privata länken som skapas behöver ett godkännande.

## <a name="private-link-approval"></a>Godkännande av privat länk
1. Välj den privata slut punkt som du skapade ovan. Du kan se en hyperlänk som gör att du kan godkänna den privata slut punkten på lagrings konto nivån. *Ett alternativ är att gå direkt till Azure Portal lagrings konto och gå till bladet **anslutningar för privata slut punkter** .*
1. Kryssa för den privata slut punkten du skapade i Studio och välj **Godkänn**.
1. Lägg till en beskrivning och välj **Ja**
1. Gå tillbaka till Synapse Studio i avsnittet **hanterade virtuella nätverk** på fliken **Hantera** .
1. Det bör ta cirka 1 minut att få godkännandet återspeglas för din privata slut punkt.

## <a name="check-the-connection-works"></a>Kontrol lera att anslutningen fungerar
1. Gå till fliken **Hantera** och välj den länkade tjänst som du har skapat.
1. Se till att **interaktiv redigering** är aktiv.
1. Välj **Testanslutning**. Du bör se att anslutningen lyckades.

Nu har du upprättat en säker och privat anslutning mellan Synapse och den länkade tjänsten.

## <a name="next-steps"></a>Nästa steg


För att utveckla ytterligare förståelse för hanterad privat slut punkt i Azure Synapse Analytics, se [hanterade privata slut punkter](../security/synapse-workspace-managed-private-endpoints.md).


Mer information om data integrering för Azure Synapse Analytics finns i mata in [data i en data Lake](data-integration-data-lake.md) artikel.