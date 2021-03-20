---
title: Hantera brand Väggs regler – Azure Portal-Azure Database for PostgreSQL – flexibel Server
description: Skapa och hantera brand Väggs regler för Azure Database for PostgreSQL-flexibel server med hjälp av Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936924"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Skapa och hantera brand Väggs regler för Azure Database for PostgreSQL-flexibel server med hjälp av Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är för närvarande en offentlig för hands version.

Azure Database for PostgreSQL-flexibel Server stöder två typer av ömsesidigt exklusiva nätverks anslutnings metoder för att ansluta till din flexibla Server. De två alternativen är:

* Offentlig åtkomst (tillåtna IP-adresser)
* Privat åtkomst (VNet-integrering)

I den här artikeln fokuserar vi på att skapa en PostgreSQL-server med **offentlig åtkomst (tillåtna IP-adresser)** med hjälp av Azure Portal och ger en översikt över att hantera brand Väggs regler när du har skapat en flexibel Server. Med *offentlig åtkomst (tillåtna IP-adresser)* begränsas anslutningarna till postgresql-servern till endast tillåtna IP-adresser. Klientens IP-adresser måste vara tillåtna i brand Väggs regler. Mer information om det finns i [offentlig åtkomst (tillåtna IP-adresser)](./concepts-networking.md#public-access-allowed-ip-addresses). Brand Väggs reglerna kan definieras när servern skapas (rekommenderas) men kan även läggas till senare. I den här artikeln får du en översikt över hur du skapar och hanterar brand Väggs regler med hjälp av offentlig åtkomst (tillåtna IP-adresser).

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Skapa en brand Väggs regel när du skapar en server

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.
2. Välj **databaser**  >  **Azure Database for PostgreSQL**. Du kan också ange **postgresql** i sökrutan för att hitta tjänsten.
3. Välj **flexibel Server** som distributions alternativ.
4. Fyll i formuläret **grundläggande** information.
5. Gå till fliken **nätverk** för att konfigurera hur du vill ansluta till servern.
6. I **anslutnings metoden** väljer du *offentlig åtkomst (tillåtna IP-adresser)*. Om du vill skapa **brand Väggs regler** anger du brand Väggs regelns namn och en enskild IP-adress eller ett adress intervall. Om du vill begränsa regeln till en enskild IP-adress skriver du samma adress i fältet för Start-IP-adress och slut-IP-adress. Genom att öppna brand väggen kan administratörer, användare och program få åtkomst till alla databaser på PostgreSQL-servern som de har giltiga autentiseringsuppgifter för.
   > [!Note]
   > Azure Database for PostgreSQL-flexibel Server skapar en brand vägg på server nivå. Den förhindrar att externa program och verktyg ansluter till servern eller databaser på servern, om du inte konfigurerar en regel som öppnar brandväggen för specifika IP-adresser.
7. Välj **Granska + skapa** för att granska din flexibla Server konfiguration.
8.  Välj **Skapa** för att etablera servern. Etableringen kan ta några minuter.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Skapa en brand Väggs regel när servern har skapats

1. I [Azure Portal](https://portal.azure.com/)väljer du den Azure Database for PostgreSQL-flexibla server där du vill lägga till brand Väggs regler.
2. På sidan flexibel Server under **inställnings** rubrik klickar du på **nätverk** för att öppna sidan nätverk för flexibel Server.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. Klicka på **Lägg till aktuell klient-IP-adress** i brand Väggs reglerna. Detta skapar automatiskt en brand Väggs regel med den offentliga IP-adressen för datorn, som den uppfattas av Azure-systemet.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. Verifiera din IP-adress innan du sparar konfigurationen. I vissa situationer skiljer sig IP-adressen som observerats av Azure Portal från den IP-adress som användes vid åtkomst till Internet-och Azure-servrar. Därför kan du behöva ändra den första IP-adressen och den sista IP-adressen för att regeln ska fungera som förväntat.

   Du kan använda en sökmotor eller ett annat online verktyg för att kontrol lera din egen IP-adress. Sök till exempel efter "What ' min IP".

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. Lägg till ytterligare adress intervall. I brand Väggs reglerna för den Azure Database for PostgreSQL-flexibla servern kan du ange en enskild IP-adress eller ett adress intervall. Om du vill begränsa regeln till en enskild IP-adress skriver du samma adress i fältet för Start-IP-adress och slut-IP-adress. Genom att öppna brand väggen kan administratörer, användare och program få åtkomst till alla databaser på PostgreSQL-servern som de har giltiga autentiseringsuppgifter för.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. Klicka på **Spara** i verktygsfältet för att spara brand Väggs regeln. Vänta tills den bekräftelse att uppdateringen av brand Väggs reglerna har slutförts.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Ansluta från Azure

Du kanske vill aktivera resurser eller program som distribueras i Azure för att ansluta till din flexibla Server. Detta inkluderar webb program som finns i Azure App Service, som körs på en virtuell Azure-dator, en Azure Data Factory data hanterings-gateway och många fler. 

När ett program i Azure försöker ansluta till servern, verifierar brand väggen att Azure-anslutningar är tillåtna. Du kan aktivera den här inställningen genom att markera alternativet **Tillåt offentlig åtkomst från Azure-tjänster och-resurser i Azure till den här servern** i portalen från fliken **nätverk** och tryck på **Spara**.

Resurserna behöver inte finnas i samma virtuella nätverk (VNet) eller resurs gruppen för brand Väggs regeln för att aktivera dessa anslutningar. Om anslutnings försöket inte är tillåtet når inte begäran den Azure Database for PostgreSQL flexibla servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
>
> Vi rekommenderar att du väljer **privat åtkomst (VNet-integrering)** för säker åtkomst till flexibel Server.
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brand Väggs regler via Azure Portal

Upprepa följande steg för att hantera brand Väggs reglerna.

- Lägg till den aktuella datorn genom att klicka på + **Lägg till aktuell klient-IP-adress** i brand Väggs reglerna. Klicka på **Spara** för att spara ändringarna.
- Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen. Klicka på **Spara** för att spara ändringarna.
- Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det. Klicka på **Spara** för att spara ändringarna.
- Ta bort en befintlig regel genom att klicka på ellipsen [...] och ta bort regeln genom att klicka på **ta bort** . Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk i Azure Database for PostgreSQL-flexibel Server](./concepts-networking.md)
- Lär dig mer om regler för att [Azure Database for PostgreSQL flexibla Server brand vägg](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure CLI](./how-to-manage-firewall-cli.md).