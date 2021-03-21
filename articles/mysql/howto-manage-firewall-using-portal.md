---
title: Hantera brand Väggs regler – Azure Portal-Azure Database for MySQL
description: Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540492"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Skapa och hantera brandväggsregler för Azure Database for MySQL med hjälp av Azure-portalen
Brand Väggs regler på server nivå kan användas för att hantera åtkomst till en Azure Database for MySQL-server från en angiven IP-adress eller ett intervall med IP-adresser. 

Virtual Network-regler (VNet) kan också användas för att skydda åtkomsten till servern. Lär dig mer om [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure Portal](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. På sidan MySQL server under inställnings rubrik klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhet för Azure Database for MySQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal på anslutnings säkerhet":::

2. Klicka på **Lägg till min IP** i verktygsfältet. Detta skapar automatiskt en brand Väggs regel med den offentliga IP-adressen för datorn, som den uppfattas av Azure-systemet.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal på Lägg till min IP":::

3. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observerats av Azure Portal från den IP-adress som användes vid åtkomst till Internet-och Azure-servrar. Därför kan du behöva ändra Start-IP och slut-IP för att funktionen regel ska fungera som förväntat.

   Använd en sökmotor eller ett annat online verktyg för att kontrol lera din egen IP-adress. Sök t. ex. "Vad är min IP-adress".

4. Lägg till ytterligare adress intervall. I brand Väggs reglerna för Azure Database for MySQL kan du ange en enskild IP-adress eller ett adress intervall. Om du vill begränsa regeln till en enskild IP-adress skriver du samma adress i fälten Start-IP och slut-IP. Genom att öppna brand väggen kan administratörer, användare och program få åtkomst till alla databaser på MySQL-servern som de har giltiga autentiseringsuppgifter för.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal-brand Väggs regler":::

5. Klicka på **Spara** i verktygsfältet för att spara brand Väggs regeln på server nivå. Vänta tills en bekräftelse på att uppdateringen av brand Väggs reglerna har slutförts.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal på Spara":::

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure Database for MySQL-server måste Azure-anslutningar vara aktiverade. Till exempel, för att vara värd för ett Azure Web Apps-program eller ett program som körs på en virtuell Azure-dator eller för att ansluta från en Azure Data Factory Data Management Gateway. Resurserna behöver inte finnas i samma Virtual Network (VNet) eller resurs gruppen för brand Väggs regeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett par metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in alternativet **Tillåt åtkomst till Azure-tjänster** på **i portalen** från fönstret **anslutnings säkerhet** och trycka på **Spara**. Om anslutnings försöket inte är tillåtet når begäran inte Azure Database for MySQL servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Hantera befintliga brand Väggs regler på server nivå med hjälp av Azure Portal
Upprepa stegen för att hantera brand Väggs reglerna.
* Klicka på **+ Lägg till min IP** för att lägga till den aktuella datorn. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du in **regel namn**, **Start-IP** och **slut-IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill ändra en befintlig regel klickar du på något av fälten i regeln och ändrar sedan. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel klickar du på ellipsen [...] och klickar sedan på **ta bort**. Klicka på **Spara** för att spara ändringarna.


## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du använda skript för att [skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md).
- Mer säker åtkomst till servern genom [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure Portal](howto-manage-vnet-using-portal.md).
- Information om hur du ansluter till en Azure Database for MySQL-Server finns i [anslutnings bibliotek för Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
