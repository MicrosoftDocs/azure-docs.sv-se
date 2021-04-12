---
title: Distribuera RD Gateway-roll Windows Virtual Desktop – Azure
description: Distribuera RD Gateway-rollen i det virtuella Windows-skrivbordet.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 37d859bccd7fea082e059c7ab19c789276974cd1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445694"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Distribuera RD Gateway-rollen i Windows Virtual Desktop (för hands version)

> [!IMPORTANT]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln får du lära dig hur du använder RD Gateway-rollen (för hands version) för att distribuera servrar för fjärrskrivbordsgateway i din miljö. Du kan installera Server rollerna på fysiska datorer eller virtuella datorer beroende på om du skapar en lokal, molnbaserad eller hybrid miljö.

## <a name="install-the-rd-gateway-role"></a>Installera RD Gateway-rollen

1. Logga in på mål servern med administratörs behörighet.

2. I **Serverhanteraren** väljer du **Hantera** och väljer sedan **Lägg till roller och funktioner**. Installations programmet för att **lägga till roller och funktioner** öppnas.

3. I **innan du börjar** väljer du **Nästa**.

4. I **Välj Installations typ** väljer du **rollbaserad eller funktions baserad installation** och väljer sedan **Nästa**.

5. För **Välj mål server** väljer du **Välj en server från** serverpoolen. För **serverpoolen** väljer du namnet på den lokala datorn. När du är klar väljer du **Nästa**.

6. I **Välj roller för Server roller**  >  väljer du **Fjärrskrivbordstjänster**. När du är klar väljer du **Nästa**.

7. I **Fjärrskrivbordstjänster** väljer du **Nästa.**

8. För **Välj roll tjänster** väljer du endast **Fjärrskrivbordsgateway** när du uppmanas att lägga till nödvändiga funktioner, Välj **Lägg till funktioner**. När du är klar väljer du **Nästa**.

9. För **nätverks princip-och nätverks åtkomst tjänster** väljer du **Nästa**.

10. För **webb server roll (IIS)** väljer du **Nästa**.

11. För **Välj roll tjänster** väljer du **Nästa**.

12. För **Bekräfta installations inställningarna** väljer du **Installera**. Stäng inte installations programmet medan installations processen sker.

## <a name="configure-rd-gateway-role"></a>Konfigurera RD Gateway-roll

När du har installerat RD Gateway-rollen måste du konfigurera den.

Konfigurera RD Gateway-rollen:

1. Öppna **Serverhanteraren** och välj **Fjärrskrivbordstjänster**.

2. Gå till **servrar**, högerklicka på namnet på servern och välj **RD Gateway Manager**.

3. I Hanteraren för fjärrskrivbordsgateway högerklickar du på namnet på din gateway och väljer sedan **Egenskaper**.

4. Öppna fliken **SSL-certifikat** , Välj fliken **Importera ett certifikat till din RD Gateway** -bubbla och välj sedan **Bläddra och importera certifikat.**...

5. Välj namnet på PFX-filen och välj sedan **Öppna**.

6. Ange lösen ordet för PFX-filen när du uppmanas till det.

7. När du har importerat certifikatet och dess privata nyckel ska visningen Visa certifikatets nyckelattribut.

>[!NOTE]
>Eftersom RD Gateway-rollen ska vara offentlig rekommenderar vi att du använder ett offentligt utfärdat certifikat. Om du använder ett privat utfärdat certifikat måste du se till att konfigurera alla klienter med certifikatets förtroende kedja i förväg.

## <a name="next-steps"></a>Nästa steg

Om du vill lägga till hög tillgänglighet till din RD Gateway-roll kan du läsa mer i [lägga till hög tillgänglighet på webb sidan för fjärr skrivbord webb och gateway](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).