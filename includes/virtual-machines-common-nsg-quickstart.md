---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187313"
---
Du öppnar en port eller skapar en slut punkt för en virtuell dator (VM) i Azure genom att skapa ett nätverks filter i ett undernät eller ett nätverks gränssnitt för virtuella datorer. Du placerar dessa filter, som styr både inkommande och utgående trafik, i en nätverks säkerhets grupp som är kopplad till den resurs som tar emot trafiken.

Exemplet i den här artikeln visar hur du skapar ett nätverks filter som använder TCP-standardporten 80 (det antas att du redan har startat lämpliga tjänster och öppnat eventuella brand Väggs regler för OS på den virtuella datorn).

När du har skapat en virtuell dator som har kon figurer ATS för att betjäna webb förfrågningar på TCP-standardporten 80 kan du:

1. Skapa en nätverkssäkerhetsgrupp.

2. Skapa en inkommande säkerhets regel som tillåter trafik och tilldela värden till följande inställningar:

   - **Mål Port intervall**: 80

   - **Käll port intervall**: * (tillåter alla käll portar)

   - **Prioritets värde**: Ange ett värde som är mindre än 65 500 och högre prioritet än standard regeln catch-all neka inkommande.

3. Koppla nätverks säkerhets gruppen till nätverks gränssnittet eller under nätet för den virtuella datorn.

Även om det här exemplet använder en enkel regel för att tillåta HTTP-trafik, kan du även använda nätverks säkerhets grupper och regler för att skapa mer komplexa nätverkskonfigurationer. 




