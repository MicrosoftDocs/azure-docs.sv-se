---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 31e61069c95be9bd1c7a684bb83ebcd93bcb14be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019163"
---
1. Starta Azure Site Recovery UnifiedSetup.exe
2. I **Before you begin** (Innan du börjar) väljer du **Add additional process servers to scale out deployment** (Lägg till ytterligare processervrar till utskalningsdistribution).

   ![Lägga till processerver](./media/site-recovery-add-process-server/ps-page-1.png)

3. I **Configuration Server Details** (Information om konfigurationsserver) anger du IP-adressen för konfigurationsservern samt lösenfrasen.

   ![Lägga till processerver 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. I **Internet Settings** (Internetinställningar) anger du hur providern som körs på konfigurationsservern ska ansluta till Azure Site Recovery via internet.

   ![Lägga till processerver 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Om du vill ansluta till den befintliga proxyservern som är konfigurerad på datorn väljer du **Anslut med befintliga proxyinställningar**.
   * Om du vill att providern ska ansluta direkt väljer du **Anslut direkt utan en proxy**.
   * Om den befintliga proxyservern kräver autentisering, eller om du vill använda en anpassad proxyserver för provideranslutningen, väljer du **Anslut med anpassade proxyinställningar**.

     * Om du använder en anpassad proxyserver måste du ange adressen, porten och autentiseringsuppgifterna.
     * Om du använder en proxyserver bör du redan ha tillåtit åtkomst till URL:erna för tjänsten.

5. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

     ![Lägga till processerver 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. I så fall konfigurerar du kontroller som kontrollerar att PowerCLI 6.0 är installerat.

     ![Lägga till processerver 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.
     ![Skärm bild som visar installations platsen för binärfiler och cache-lagring.](./media/site-recovery-add-process-server/ps-page-6.png)

8. I **Val av nätverk** anger du lyssnare (nätverkskort och SSL-port) där konfigurationsservern skickar och tar emot replikeringsdata. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte Port 443 för att skicka eller ta emot replikeringstrafik.

     ![Lägga till processerver 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.

     ![Lägga till processerver 7](./media/site-recovery-add-process-server/ps-page-8.png)
