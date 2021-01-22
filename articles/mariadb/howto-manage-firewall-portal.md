---
title: Hantera brand Väggs regler – Azure Portal-Azure Database for MariaDB
description: Skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure Portal
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: a708326db1a782cf3ded5f9108e1b87c6d53fe20
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665063"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure Portal
Brand Väggs regler på server nivå kan användas för att hantera åtkomst till en Azure Database for MariaDB-Server från en angiven IP-adress eller ett intervall med IP-adresser.

Virtual Network-regler (VNet) kan också användas för att skydda åtkomsten till servern. Lär dig mer om [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure Portal](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

1. På sidan MariaDB-Server under inställnings rubrik klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhet för Azure Database for MariaDB.

   ![Azure Portal på anslutnings säkerhet](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Klicka på **Lägg till min IP** i verktygsfältet. Detta skapar automatiskt en brand Väggs regel med den offentliga IP-adressen för datorn, som den uppfattas av Azure-systemet.

   ![Azure Portal på Lägg till min IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observerats av Azure Portal från den IP-adress som användes vid åtkomst till Internet-och Azure-servrar. Därför kan du behöva ändra Start-IP och slut-IP för att funktionen regel ska fungera som förväntat.

   Använd en sökmotor eller ett annat online verktyg för att kontrol lera din egen IP-adress. Sök t. ex. "Vad är min IP-adress".

4. Lägg till ytterligare adress intervall. I brand Väggs reglerna för Azure Database for MariaDB kan du ange en enskild IP-adress eller ett adress intervall. Om du vill begränsa regeln till en enskild IP-adress skriver du samma adress i fälten Start-IP och slut-IP. Genom att öppna brand väggen kan administratörer, användare och program få åtkomst till alla databaser på MariaDB-servern som de har giltiga autentiseringsuppgifter för.

   ![Azure Portal-brand Väggs regler](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Klicka på **Spara** i verktygsfältet för att spara brand Väggs regeln på server nivå. Vänta tills en bekräftelse på att uppdateringen av brand Väggs reglerna har slutförts.

   ![Azure Portal på Spara](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure Database for MariaDB-Server måste Azure-anslutningar vara aktiverade. Till exempel, för att vara värd för ett Azure Web Apps-program eller ett program som körs på en virtuell Azure-dator eller för att ansluta från en Azure Data Factory Data Management Gateway. Resurserna behöver inte finnas i samma Virtual Network (VNet) eller resurs gruppen för brand Väggs regeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett par metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in alternativet **Tillåt åtkomst till Azure-tjänster** på **i portalen** från fönstret **anslutnings säkerhet** och klicka på **Spara**. Om anslutnings försöket inte är tillåtet når begäran inte Azure Database for MariaDB servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Hantera befintliga brand Väggs regler i Azure Portal
Upprepa stegen för att hantera brand Väggs reglerna.
* Klicka på **+ Lägg till min IP** för att lägga till den aktuella datorn. Klicka på **Spara** för att spara ändringarna.
* Om du vill lägga till ytterligare IP-adresser skriver du in **regel namn**, **Start-IP** och **slut-IP**. Klicka på **Spara** för att spara ändringarna.
* Om du vill ändra en befintlig regel klickar du på något av fälten i regeln och ändrar sedan. Klicka på **Spara** för att spara ändringarna.
* Om du vill ta bort en befintlig regel klickar du på ellipsen [...] och klickar sedan på **ta bort**. Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
 - På samma sätt kan du använda skript för att [skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure CLI](howto-manage-firewall-cli.md).
 - Mer säker åtkomst till servern genom [att skapa och hantera Virtual Network tjänst slut punkter och regler med hjälp av Azure Portal](howto-manage-vnet-portal.md).