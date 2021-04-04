---
title: ta med fil
description: ta med fil
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001580"
---
1. Kör det enhetliga installationsprogrammet.
2. I **innan du börjar** väljer du **Installera konfigurations servern och processervern**.

    ![Skärm bild av skärmen innan du börjar i enhetligt läge.](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.

    ![Skärm bild av skärmen för Third Party Software licens i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. I **Registration** (Registrering) väljer du den registreringsnyckel som du hämtade från valvet.

    ![Skärm bild av registrerings skärmen i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. I **Internet inställningar** anger du hur providern som körs på konfigurations servern ansluter till Azure Site Recovery via Internet. Kontrol lera att du har tillåtit de nödvändiga URL: erna.

    - Om du vill ansluta till den proxyserver som är konfigurerad på datorn väljer **du Anslut för att Azure Site Recovery med hjälp av en proxyserver**.
    - Om du vill att providern ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
    - Om den befintliga proxyn kräver autentisering, eller om du vill använda en anpassad proxy för anslutnings tjänsten, väljer du **Anslut med anpassade proxyinställningar** och anger adressen, porten och autentiseringsuppgifterna.
     ![Skärm bild av skärmen Internet inställningar i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Skärm bild av skärmen krav kontroll i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![Skärm bild av konfigurations skärmen för MySQL i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. I **miljö information** väljer du Nej om du replikerar Azure Stack virtuella datorer eller fysiska servrar. 
9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Skärm bild av skärmen installations plats i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. I **Val av nätverk** väljer du först det nätverkskort som den inbyggda processervern använder för identifiering och push-installation av mobilitets tjänsten på käll datorer. Välj sedan det nätverkskort som konfigurations servern använder för anslutning med Azure. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

    ![Skärm bild av skärmen nätverks val i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.

    ![Skärm bild av sammanfattnings skärmen i enhetlig installation.](./media/site-recovery-add-configuration-server/combined-wiz10.png)

När registreringen är klar visas servern på bladet **Inställningar**  >  **servrar** i valvet.
