---
title: Anpassad nätverks härdning i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder faktiska trafik mönster för att förstärka reglerna för nätverks säkerhets grupper (NSG) och ytterligare förbättra din säkerhets position.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: 23cbabf1c4fb1433fa1201f77528db9d9f393ed5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712155"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Förbättra din nätverks säkerhets position med anpassningsbar nätverks härdning
Lär dig hur du konfigurerar anpassad nätverks härdning i Security Center.

## <a name="availability"></a>Tillgänglighet
|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Nödvändiga roller och behörigheter:|Skriv behörighet för datorns NSG: er|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Vad är anpassad nätverks härdning?
Genom att använda [nätverks säkerhets grupper (NSG)](../virtual-network/network-security-groups-overview.md) för att filtrera trafik till och från resurser förbättras nätverks säkerhets position. Det kan dock fortfarande finnas fall där den faktiska trafiken som flödar genom NSG är en del av de NSG-regler som definierats. I dessa fall kan ytterligare förbättra säkerheten position genom att skärpa NSG-reglerna, baserat på de faktiska trafik mönstren.

Anpassad nätverks härdning ger rekommendationer för ytterligare härdning av NSG-reglerna. Den använder en Machine Learning-algoritm som faktorer i faktisk trafik, känd betrodd konfiguration, Hot information och andra risk indikatorer och ger rekommendationer för att endast tillåta trafik från specifika IP/port-tupler.

Anta till exempel att den befintliga NSG-regeln är att tillåta trafik från 140.20.30.10/24 på port 22. Anpassad nätverks härdning, baserat på trafik analys, kan rekommendera att begränsa intervallet för att tillåta trafik från 140.23.30.10/29 och neka all annan trafik till porten.

>[!Note]
> Rekommendationer för anpassningsbar nätverks härdning stöds endast på följande angivna portar (för både UDP och TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Visa och hantera härdning av aviseringar och regler

1. Från Security Center menyn öppnar du **Azure Defender** -instrumentpanelen och väljer panelen för anpassad nätverks härdning (1) eller Insights-panelen som är relaterad till anpassad nätverks härdning (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Åtkomst till verktyg för anpassad nätverks härdning" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Panelen insikter visar den procentuella andelen av dina virtuella datorer som för närvarande är förvarad med anpassningsbar nätverks härdning. 

1. Informations sidan för de **anpassningsbara nätverks härdnings rekommendationerna bör tillämpas på Internet** -rekommendation för virtuella datorer som öppnas med de virtuella datorerna i nätverket som är grupperade i tre flikar:
   * **Felaktiga resurser**: virtuella datorer som för närvarande har rekommendationer och aviseringar som har utlösts genom att köra algoritmen för anpassad nätverks härdning. 
   * **Felfria resurser**: virtuella datorer utan aviseringar och rekommendationer.
   * Ej **genomsökta resurser**: virtuella datorer som den anpassningsbara algoritmen för nätverks härdning inte kan köras på på grund av någon av följande orsaker:
      * **Virtuella datorer är klassiska virtuella** datorer: endast Azure Resource Manager virtuella datorer stöds.
      * Det finns **inte tillräckligt med data**: för att skapa korrekta rekommendationer för trafik härdning måste Security Center minst 30 dagars trafik data.
      * Den **virtuella datorn skyddas inte av Azure Defender**: endast virtuella datorer som skyddas med [Azure Defender för servrar](defender-for-servers-introduction.md) är berättigade till den här funktionen.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Informations sidan i rekommendationen för anpassningsbara nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet":::

1. På fliken **felaktiga resurser** väljer du en virtuell dator för att visa aviseringar och de rekommenderade regler för härdning som ska tillämpas.

    - Fliken **regler** visar en lista över regler som anpassar nätverks härdning rekommenderar att du lägger till
    - Fliken **aviseringar** visar en lista över de aviseringar som har genererats på grund av trafik, som flödar till resursen, som inte ligger inom det tillåtna IP-intervallet i de rekommenderade reglerna.

1. Du kan också redigera reglerna:

    - [Ändra en regel](#modify-rule)
    - [Ta bort en regel](#delete-rule) 
    - [Lägg till en regel](#add-rule)

3. Välj de regler som du vill tillämpa på NSG och klicka på **tillämpa**.

    > [!TIP]
    > Om de tillåtna käll-IP-intervallen visas som none, innebär det att den rekommenderade regeln är en regel för *neka* , annars är det en *Tillåt* -regel.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Hantera regler för regler för anpassad nätverks härdning":::

      > [!NOTE]
      > De tvingade reglerna läggs till i NSG (s) som skyddar den virtuella datorn. (En virtuell dator kan skyddas av en NSG som är kopplad till dess nätverkskort, eller under nätet där den virtuella datorn finns, eller båda)

### <a name="modify-a-rule"></a>Ändra en regel <a name ="modify-rule"></a>

Du kanske vill ändra parametrarna för en regel som har rekommenderats. Till exempel kanske du vill ändra de rekommenderade IP-intervallen.

Några viktiga rikt linjer för att ändra en regel för anpassad nätverks härdning:

* Du kan bara ändra parametrarna för "Tillåt"-regler. 
* Du kan inte ändra reglerna för "Tillåt" för att bli "Neka"-regler. 

  > [!NOTE]
  > Att skapa och ändra "Neka"-regler görs direkt på NSG. Mer information finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md).

* En regel för att **neka all trafik** är den enda typen av "Neka"-regel som visas här, och den kan inte ändras. Du kan dock ta bort den (se [ta bort en regel](#delete-rule)).
  > [!NOTE]
  > En **neka all trafik** regel rekommenderas när Security Center, som ett resultat av algoritmen, inte identifierar trafik som ska tillåtas, baserat på den befintliga NSG-konfigurationen. Därför är den rekommenderade regeln att neka all trafik till den angivna porten. Namnet på den här typen av regel visas som "*systemgenererat*". Efter att ha tillämpat den här regeln kommer det faktiska namnet i NSG att vara en sträng som består av protokollet, trafik riktningen, "Neka" och ett slumpmässigt nummer.

*Så här ändrar du en regel för anpassad nätverks härdning:*

1. Om du vill ändra några av parametrarna för en regel går du till fliken **regler** , klickar på de tre punkterna (...) i slutet av regelns rad och klickar på **Redigera**.

   ![Redigerar s-regel](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. I fönstret **Redigera regel** uppdaterar du informationen som du vill ändra och klickar på **Spara**.

   > [!NOTE]
   > När du har klickat på **Spara** har du ändrat regeln. *Men du har inte tillämpat den på NSG.* Om du vill tillämpa det måste du välja regeln i listan och välja **Använd** (som förklaras i nästa steg).

   ![Välja Spara](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Om du vill tillämpa den uppdaterade regeln väljer du den uppdaterade regeln i listan och klickar på **Använd**.

    ![tillämpa regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Lägg till en ny <a name ="add-rule"></a> regel

Du kan lägga till en regel för "Tillåt" som inte rekommenderades av Security Center.

> [!NOTE]
> Det går bara att lägga till regler för "Tillåt" här. Om du vill lägga till regler för neka kan du göra det direkt på NSG. Mer information finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md).

*Så här lägger du till en regel för anpassad nätverks härdning:*

1. Klicka på **Lägg till regel** (finns i det övre vänstra hörnet).

   ![lägg till regel](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. I fönstret **ny regel** anger du informationen och klickar på **Lägg till**.

   > [!NOTE]
   > När du har klickat på **Lägg till** har du lagt till regeln och den visas med de andra rekommenderade reglerna. Men du har inte använt den på NSG. Om du vill aktivera det måste du välja regeln i listan och klicka på **tillämpa** (som förklaras i nästa steg).

3. Om du vill använda den nya regeln väljer du den nya regeln i listan och klickar på **Använd**.

    ![tillämpa regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Ta bort en <a name ="delete-rule"></a> regel

Vid behov kan du ta bort en rekommenderad regel för den aktuella sessionen. Du kan till exempel bestämma att tillämpa en föreslagen regel kan blockera legitim trafik.

*Så här tar du bort en regel för anpassad nätverks härdning för den aktuella sessionen:*

1. Klicka på de tre punkterna (...) i slutet av regelns rad på fliken **regler** och klicka sedan på **ta bort**.  

    ![Ta bort en regel](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)