---
title: Hantera brand Väggs regler – storskalig (citus)-Azure Database for PostgreSQL
description: Skapa och hantera brand Väggs regler för Azure Database for PostgreSQL-storskalig (citus) med hjälp av Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/11/2020
ms.openlocfilehash: dadd04497eae0e91bdf5ea3caad38beda35f7fa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91275429"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Hantera brand Väggs regler för Azure Database for PostgreSQL-storskalig (citus)
Brand Väggs regler på server nivå kan användas för att hantera åtkomst till en citus-koordinator (disscale) från en angiven IP-adress eller ett intervall med IP-adresser.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- En Server grupp [skapar en Server grupp för Azure Database for PostgreSQL – storskalig (citus)](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

> [!NOTE]
> De här inställningarna är också tillgängliga när du skapar en citus-servergrupp (Azure Database for PostgreSQL-storskalig). Klicka på **offentlig slut punkt** under fliken **nätverk** .

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Fliken Azure Portal-nätverk":::

1. Öppna brand Väggs reglerna genom att klicka på **nätverk** under säkerhets rubriken på sidan postgresql server Group.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal på nätverk":::

2. Klicka på **Lägg till aktuell klient-IP-adress** för att skapa en brand Väggs regel med den offentliga IP-adressen för din dator, som den uppfattas av Azure-systemet.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal-Klicka på Lägg till klient-IP":::

Alternativt kan du klicka på **+ Lägg till 0.0.0.0-255.255.255.255** (till höger om alternativ B) för att inte bara använda din IP-adress, men hela Internet för att få åtkomst till koordinator nodens port 5432. I den här situationen måste klienter fortfarande logga in med rätt användar namn och lösen ord för att använda klustret. Vi rekommenderar dock att du ger global åtkomst för endast korta tids perioder och endast för icke-produktions databaser.

3. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observerats av Azure Portal från den IP-adress som användes vid åtkomst till Internet-och Azure-servrar. Därför kan du behöva ändra Start-IP och slut-IP för att funktionen regel ska fungera som förväntat.
   Använd en sökmotor eller ett annat online verktyg för att kontrol lera din egen IP-adress. Sök till exempel efter "What ' min IP".

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Bing-sökning för vad är min IP":::

4. Lägg till ytterligare adress intervall. I brand Väggs reglerna kan du ange en enskild IP-adress eller ett adress intervall. Om du vill begränsa regeln till en enskild IP-adress skriver du samma adress i fältet för Start-IP och slut-IP. Genom att öppna brand väggen kan administratörer, användare och program komma åt koordinator-noden på port 5432.

5. Klicka på **Spara** i verktygsfältet för att spara brand Väggs regeln på server nivå. Vänta tills den bekräftelse att uppdateringen av brand Väggs reglerna har slutförts.

## <a name="connecting-from-azure"></a>Ansluta från Azure

Det finns ett enkelt sätt att bevilja storskalig (citus) databas åtkomst till program som finns på Azure (till exempel ett Azure Web Apps-program eller som körs i en virtuell Azure-dator). Ställ bara in alternativet **Tillåt Azure-tjänster och resurser för att komma åt denna server grupp** på **Ja** i portalen från fönstret **nätverk** och tryck på **Spara**.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå via Azure Portal
Upprepa stegen för att hantera brand Väggs reglerna.
* Lägg till den aktuella datorn genom att klicka på knappen till + **Lägg till aktuell klient-IP-adress**. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen. Klicka på **Spara** för att spara ändringarna.
* Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det. Klicka på **Spara** för att spara ändringarna.
* Ta bort en befintlig regel genom att klicka på ellipsen [...] och ta bort regeln genom att klicka på **ta bort** . Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [begrepp för brand Väggs regler](concepts-hyperscale-firewall-rules.md), inklusive fel sökning av anslutnings problem.
