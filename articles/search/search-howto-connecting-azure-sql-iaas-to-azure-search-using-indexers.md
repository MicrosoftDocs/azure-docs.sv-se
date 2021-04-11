---
title: VIRTUELL Azure SQL-anslutning för Sök indexering
titleSuffix: Azure Cognitive Search
description: Aktivera krypterade anslutningar och konfigurera brand väggen för att tillåta anslutningar till SQL Server på en virtuell Azure-dator från en indexerare på Azure Kognitiv sökning.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 23c5d138463a52f4ff4c52b4a919b71a87b7fd6d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802887"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurera en anslutning från en Azure Kognitiv sökning-indexerare till SQL Server på en virtuell Azure-dator

När du konfigurerar en [Azure SQL-indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) för att extrahera innehåll från en databas på en virtuell Azure-dator krävs ytterligare steg för säkra anslutningar. 

En anslutning från Azure Kognitiv sökning till SQL Server på en virtuell dator är en offentlig Internet anslutning. För att säkra anslutningar ska lyckas utför du följande steg:

+ Hämta ett certifikat från en [Provider för certifikat utfärdare](https://en.wikipedia.org/wiki/Certificate_authority#Providers) för det fullständigt kvalificerade domän namnet för SQL Server-instansen på den virtuella datorn

+ Installera certifikatet på den virtuella datorn och aktivera och konfigurera sedan krypterade anslutningar på den virtuella datorn med hjälp av anvisningarna i den här artikeln.

## <a name="enable-encrypted-connections"></a>Aktivera krypterade anslutningar

Azure Kognitiv sökning kräver en krypterad kanal för alla indexerare begär Anden via en offentlig Internet anslutning. Det här avsnittet innehåller stegen för att utföra det här arbetet.

1. Kontrol lera egenskaperna för certifikatet för att verifiera att ämnes namnet är det fullständigt kvalificerade domän namnet (FQDN) för den virtuella Azure-datorn. Du kan använda ett verktyg som CertUtil eller snapin-modulen certifikat för att visa egenskaperna. Du kan hämta det fullständiga domän namnet från bladet grundläggande för VM-tjänsten i fältet **offentlig IP-adress/DNS-namn etikett** i [Azure Portal](https://portal.azure.com/).
  
   + För virtuella datorer som skapats med den nyare **Resource Manager** -mallen formateras FQDN som `<your-VM-name>.<region>.cloudapp.azure.com`

   + För äldre virtuella datorer som skapats som en **klassisk** virtuell dator formateras FQDN som `<your-cloud-service-name.cloudapp.net>` .

1. Konfigurera SQL Server att använda certifikatet med Registereditorn (regedit). 

   Även om Konfigurationshanteraren för SQL Server ofta används för den här uppgiften kan du inte använda den i det här scenariot. Det går inte att hitta det importerade certifikatet eftersom det fullständiga domän namnet för den virtuella datorn i Azure inte matchar FQDN som bestäms av den virtuella datorn (det identifierar domänen som antingen den lokala datorn eller den nätverks domän som den är ansluten till). När namnen inte matchar använder du Regedit för att ange certifikatet.

   + I regedit bläddrar du till den här register nyckeln: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate` .

     `[MSSQL13.MSSQLSERVER]`Delen varierar beroende på version och instans namn. 

   + Ange värdet för **certifikat** nyckeln till **TUMAVTRYCKET** för det TLS/SSL-certifikat som du har IMPORTERAT till den virtuella datorn.

     Det finns flera sätt att hämta tumavtrycket, lite bättre än andra. Om du kopierar den från snapin-modulen **certifikat** i MMC kommer du förmodligen att hämta ett osynligt rad matnings tecken [enligt beskrivningen i den här Support artikeln](https://support.microsoft.com/kb/2023869/), vilket resulterar i ett fel när du försöker ansluta. Det finns flera lösningar för att åtgärda det här problemet. Det enklaste är att skriva över och sedan skriva det första tecken i tumavtrycket för att ta bort det inledande tecken i fältet nyckel värde i regedit. Alternativt kan du använda ett annat verktyg för att kopiera tumavtrycket.

1. Bevilja behörighet till tjänst kontot. 

    Kontrol lera att SQL Server tjänst kontot har beviljats rätt behörighet för den privata nyckeln för TLS/SSL-certifikatet. Om du förbise det här steget kommer SQL Server inte att starta. Du kan använda snapin-modulen **certifikat** eller **CertUtils** för den här uppgiften.

1. Starta om SQL Server-tjänsten.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurera SQL Server anslutning på den virtuella datorn

När du har konfigurerat den krypterade anslutningen som krävs av Azure Kognitiv sökning finns ytterligare konfigurations steg som är inbyggda för SQL Server på virtuella Azure-datorer. Om du inte redan har gjort det är nästa steg att slutföra konfigurationen med hjälp av någon av följande artiklar:

+ En virtuell **Resource Manager** -dator finns i [ansluta till en SQL Server virtuell dator på Azure med Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ En **klassisk** virtuell dator finns i [ansluta till en SQL Server virtuell dator på den klassiska Azure-datorn](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

Läs särskilt avsnittet i varje artikel för "ansluta via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurera nätverks säkerhets gruppen (NSG)

Det är inte ovanligt att konfigurera NSG och motsvarande Azure-slutpunkt eller Access Control List (ACL) för att göra din virtuella Azure-dator tillgänglig för andra parter. Du har gjort det här innan du kan använda din egen program logik för att ansluta till din SQL Azure virtuella dator. Den är ingen annan för en Azure Kognitiv sökning-anslutning till din SQL Azure virtuella dator. 

Länkarna nedan innehåller instruktioner om NSG-konfiguration för distribution av virtuella datorer. Använd de här anvisningarna för att ACL en Azure Kognitiv sökning-slutpunkt baserat på dess IP-adress.

> [!NOTE]
> I bakgrunden, se [Vad är en nätverks säkerhets grupp?](../virtual-network/network-security-groups-overview.md)

+ En virtuell **Resource Manager** -dator finns i [så här skapar du NSG: er för arm-distributioner](../virtual-network/tutorial-filter-network-traffic.md).

+ En **klassisk** virtuell dator finns i [så här skapar du NSG: er för klassiska distributioner](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

IP-adresser kan innebära några utmaningar som är lätta att lösa om du är medveten om problemet och möjliga lösningar. I de återstående avsnitten finns rekommendationer för att hantera problem som rör IP-adresser i åtkomst kontrol listan.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Begränsa åtkomsten till Azure-Kognitiv sökning

Vi rekommenderar starkt att du begränsar åtkomsten till IP-adressen för Sök tjänsten och IP-adressintervallet för `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) i ACL i stället för att göra dina SQL Azure virtuella datorer öppna för alla anslutnings begär Anden.

Du kan ta reda på IP-adressen genom att pinga FQDN (till exempel `<your-search-service-name>.search.windows.net` ) för Sök tjänsten. Även om det är möjligt att ändra IP-adressen för Sök tjänsten är det osannolikt att den kommer att ändras. IP-adressen är till för att vara statisk under tjänstens livs längd.

Du kan ta reda på IP-adressintervallet för `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) genom att antingen använda [nedladdnings bara JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) eller genom att använda [API: et för identifiering av service tag](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). IP-adressintervallet uppdateras varje vecka.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Ta med IP-adresser för Azure Kognitiv sökning-portalen

Om du använder Azure Portal för att skapa en indexerare måste du bevilja portalen inkommande åtkomst till din SQL Azure virtuella dator. En regel för inkommande trafik i brand väggen kräver att du anger portalens IP-adress.

För att hämta IP-adressen till portalen, Ping `stamp2.ext.search.windows.net` , som är domänen för Traffic Manager. Tids gränsen för begäran visas, men IP-adressen visas i status meddelandet. Exempel: i meddelandet "Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]" är IP-adressen "52.252.175.48".

> [!NOTE]
> Kluster i olika regioner ansluter till olika trafik hanterare. Oavsett domän namn, är IP-adressen som returnerades från ping den rätt som ska användas för att definiera en brand Väggs regel för inkommande trafik för Azure Portal i din region.

## <a name="next-steps"></a>Nästa steg

Med konfigurationen av kan du nu ange en SQL Server på den virtuella Azure-datorn som data källa för en Azure Kognitiv sökning-indexerare. Mer information finns i [ansluta Azure SQL Database till Azure kognitiv sökning med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).