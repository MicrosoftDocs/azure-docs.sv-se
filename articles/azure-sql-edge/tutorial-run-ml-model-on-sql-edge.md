---
title: Distribuera ML-modell på Azure SQL Edge med ONNX
description: I del tre av den här tre delen av Azure SQL Edge-självstudien för förutsägelse av järn Malms orenheter, kör du ONNX Machine Learning-modeller på SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422203"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Distribuera ML-modell på Azure SQL Edge med ONNX 

I del tre av den här självstudien i tre delar om förutsägelse av järn Malms orenheter i Azure SQL Edge, kommer du att:

1. Använd Azure Data Studio för att ansluta till SQL Database i Azure SQL Edge-instansen.
2. Förutsäga strykjärns orenheter med ONNX i Azure SQL Edge.

## <a name="key-components"></a>Viktiga komponenter

1. Lösningen använder en standard på 500 millisekunder mellan varje meddelande som skickas till Edge-hubben. Detta kan ändras i filen **program. cs** 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. Lösningen skapade ett meddelande med följande attribut. Lägg till eller ta bort attributen enligt kraven. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Anslut till SQL Database i Azure SQL Edge-instansen för att träna, distribuera och testa ML-modellen

1. Öppna Azure Data Studio.

2. På fliken **Välkommen** startar du en ny anslutning med följande information:

   |_Fält_|_Värde_|
   |-------|-------|
   |Anslutningstyp| Microsoft SQL Server|
   |Server|Offentlig IP-adress som anges i den virtuella datorn som skapades för den här demon|
   |Användarnamn|sa|
   |Lösenord|Det starka lösen ord som användes när Azure SQL Edge-instansen skapades|
   |Databas|Standardvärde|
   |Servergrupp|Standardvärde|
   |Namn (valfritt)|Ange ett valfritt namn|

3. Klicka på **Anslut**

4. I avsnittet **fil** öppnar du **/deploymentscripts/MiningProcess_ONNX. jpynb** från mappen där du har klonat projektfilerna på din dator.

5. Ställ in kerneln på python 3.


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ONNX-modeller i Azure SQL Edge finns i [Machine Learning och AI med ONNX i SQL Edge](onnx-overview.md).