---
title: Data krypterings modeller i Microsoft Azure
description: Den här artikeln innehåller en översikt över data krypterings modeller i Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 9194f38ddea5570d94ba6c87ea6a537cb41fb98c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051386"
---
# <a name="data-encryption-models"></a>Datakrypteringsmodeller

Det är viktigt att förstå de olika krypterings modellerna och deras-och nack delar med att förstå hur olika resurs leverantörer i Azure implementerar kryptering i vila. Dessa definitioner delas mellan alla resurs leverantörer i Azure för att säkerställa vanliga språk och taxonomier.

Det finns tre scenarier för kryptering på Server sidan:

- Kryptering på Server sidan med hjälp av Service-Managed nycklar
  - Azure-resurs-providers utför krypterings-och dekrypterings åtgärder
  - Microsoft hanterar nycklarna
  - Fullständig moln funktion

- Kryptering på Server sidan med Kundhanterade nycklar i Azure Key Vault
  - Azure-resurs-providers utför krypterings-och dekrypterings åtgärder
  - Kund styr nycklar via Azure Key Vault
  - Fullständig moln funktion

- Kryptering på Server sidan med Kundhanterade nycklar på kundkontrollerad maskin vara
  - Azure-resurs-providers utför krypterings-och dekrypterings åtgärder
  - Kund styr nycklar på kundkontrollerad maskin vara
  - Fullständig moln funktion

Krypterings modeller på Server sidan avser kryptering som utförs av Azure-tjänsten. I den modellen utför resurs leverantören åtgärderna kryptera och dekryptera. Azure Storage kan till exempel ta emot data i klartext och utföra kryptering och dekryptering internt. Resurs leverantören kan använda krypterings nycklar som hanteras av Microsoft eller av kunden beroende på den angivna konfigurationen.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Var och en av krypteringen på Server sidan i rest-modeller innebär olika egenskaper för nyckel hantering. Detta inkluderar var och hur krypterings nycklar skapas och lagras samt åtkomst modeller och nyckel rotations procedurer.  

För kryptering på klient sidan bör du tänka på följande:

- Azure-tjänster kan inte se dekrypterade data
- Kunder hanterar och lagrar nycklar lokalt (eller i andra säkra lager). Nycklar är inte tillgängliga för Azure-tjänster
- Minskad moln funktion

De krypterings modeller som stöds i Azure delas in i två huvud grupper: "klient kryptering" och "kryptering på Server sidan" som tidigare nämnts. Oberoende av vilken kryptering som används i rest-modellen rekommenderar Azure-tjänster alltid användningen av en säker transport, till exempel TLS eller HTTPS. Därför bör kryptering i transporten åtgärdas av transport protokollet och bör inte vara en viktig faktor för att fastställa vilken kryptering i rest-modellen som ska användas.

## <a name="client-encryption-model"></a>Klient krypterings modell

Klient krypterings modell syftar på kryptering som utförs utanför resurs leverantören eller Azure av tjänsten eller det anropande programmet. Krypteringen kan utföras av tjänst programmet i Azure eller av ett program som körs i kundens Data Center. I båda fallen tar Azure Resource providern emot en krypterad BLOB med data utan möjlighet att dekryptera data på något sätt eller ha åtkomst till krypterings nycklarna när du använder den här krypterings modellen. I den här modellen görs nyckel hanteringen av den anropande tjänsten/programmet och är ogenomskinlig för Azure-tjänsten.

![Klient](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Kryptering på Server sidan med tjänst-hanterade nycklar

För många kunder är det grundläggande kravet att se till att data krypteras när de är i vila. Kryptering på Server sidan med hjälp av hanterade nycklar aktiverar den här modellen genom att kunderna kan markera den angivna resursen (lagrings konto, SQL DB osv.) för kryptering och lämna alla viktiga hanterings aspekter som nyckel utfärdande, rotation och säkerhets kopiering till Microsoft. De flesta Azure-tjänster som har stöd för kryptering i vila stöder vanligt vis den här modellen för att avlasta hanteringen av krypterings nycklarna till Azure. Azure-adressresursen skapar nycklar, placerar dem i säkert lagrings utrymme och hämtar dem när de behövs. Det innebär att tjänsten har fullständig åtkomst till nycklarna och att tjänsten har fullständig kontroll över livs cykel hanteringen av autentiseringsuppgifter.

![leda](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Kryptering på Server sidan med hjälp av service-hanterade nycklar tar därför snabbt itu över behovet av att ha kryptering i vila med låg kostnad för kunden. När det finns en kund som vanligt vis öppnar Azure Portal för mål prenumerationen och resurs leverantören och kontrollerar en ruta som visar att data ska krypteras. I vissa resurs hanterare kryptering på Server sidan med tjänst hanterade nycklar som standard.

Kryptering på Server sidan med Microsoft-hanterade nycklar innebär att tjänsten har fullständig åtkomst till lagring och hantering av nycklarna. Vissa kunder kanske vill hantera nycklarna eftersom de känner till att de får högre säkerhet, kostnaden och risken som är kopplade till en anpassad nyckel lagrings lösning bör övervägas när den här modellen utvärderas. I många fall kan en organisation avgöra om resurs begränsningar eller risker i en lokal lösning kan vara större än risken för moln hantering i rest-nycklar.  Den här modellen kanske inte räcker för organisationer som har krav på att kontrol lera skapandet eller livs cykeln för krypterings nycklarna eller ha olika personal som hanterar en tjänsts krypterings nycklar än de som hanterar tjänsten (det vill säga uppdelning av nyckel hantering från den övergripande hanterings modellen för tjänsten).

### <a name="key-access"></a>Nyckel åtkomst

När Server sidans kryptering med hanterade nycklar används, hanteras nyckeln, lagringen och tjänst åtkomsten av tjänsten. Normalt lagras data krypterings nycklarna i en butik som ligger nära data och som är tillgängliga samtidigt som nyckel krypterings nycklarna lagras i ett säkert internt arkiv.

**Fördelar**

- Enkel installation
- Microsoft hanterar nyckel rotation, säkerhets kopiering och redundans
- Kunden har ingen kostnad som är kopplad till implementeringen eller risken för ett anpassat nyckel hanterings schema.

**Nackdelar**

- Ingen kund kontroll över krypterings nycklarna (nyckel specifikation, livs cykel, återkallning osv.)
- Det går inte att särskilja nyckel hantering från den övergripande hanterings modellen för tjänsten

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kryptering på Server sidan med Kundhanterade nycklar i Azure Key Vault

För scenarier där kravet är att kryptera data i vila och kontrol lera krypterings nycklarna kan kunder använda kryptering på Server sidan med Kundhanterade nycklar i Key Vault. Vissa tjänster kan bara lagra rot nyckelns krypterings nyckel i Azure Key Vault och lagra krypterings nyckeln för krypterade data på en intern plats närmare data. I det scenariot kan kunder ta med sina egna nycklar för att Key Vault (BYOK – Bring Your Own Key) eller generera nya och använda dem för att kryptera de önskade resurserna. Medan resurs leverantören utför krypterings-och dekrypterings åtgärder, använder den den konfigurerade nyckel krypterings nyckeln som rot nyckel för alla krypterings åtgärder.

Förlust av nyckel krypterings nycklar innebär förlust av data. Därför bör nycklarna inte tas bort. Nycklar ska säkerhets kopie ras när de skapas eller roteras. [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md) ska vara aktiverat på alla valv som lagrar nyckel krypterings nycklar. I stället för att ta bort en nyckel ställer du in på falskt eller anger förfallo datum.

### <a name="key-access"></a>Nyckel åtkomst

Krypterings modellen på Server sidan med Kundhanterade nycklar i Azure Key Vault innebär att tjänsten använder nycklarna för att kryptera och dekryptera efter behov. Kryptering i rest-nycklar görs tillgängligt för en tjänst via en princip för åtkomst kontroll. Den här principen ger åtkomst till tjänst identiteten för att ta emot nyckeln. En Azure-tjänst som körs på uppdrag av en associerad prenumeration kan konfigureras med en identitet i den prenumerationen. Tjänsten kan utföra Azure Active Directory autentisering och ta emot en autentiseringstoken som identifierar sig själv som den tjänst som agerar på uppdrag av prenumerationen. Denna token kan sedan visas för Key Vault för att få en nyckel som har getts åtkomst till.

För åtgärder som använder krypterings nycklar kan en tjänst identitet beviljas åtkomst till någon av följande åtgärder: dekryptera, kryptera, unwrapKey, wrapKey, verifiera, signera, Hämta, Visa, uppdatera, skapa, importera, ta bort, säkerhetskopiera och Återställ.

För att få en nyckel som används vid kryptering eller dekryptering av data i vila den tjänst identitet som Resource Manager-tjänstinstans ska köra som måste ha UnwrapKey (för att hämta nyckeln för dekryptering) och WrapKey (för att kunna infoga en nyckel i Key Vault när du skapar en ny nyckel).

>[!NOTE]
>Mer information om Key Vault auktorisering finns på sidan skydda ditt nyckel valv i Azure Key Vault- [dokumentationen](../../key-vault/general/secure-your-key-vault.md).

**Fördelar**

- Fullständig kontroll över de nycklar som används – krypterings nycklar hanteras i kundens Key Vault under kundens kontroll.
- Möjlighet att kryptera flera tjänster till en huvud server
- Kan åtskilja nyckel hantering från den övergripande hanterings modellen för tjänsten
- Kan definiera tjänst-och nyckel plats mellan regioner

**Nackdelar**

- Kunden har fullt ansvar för hantering av nyckel åtkomst
- Kunden har fullt ansvar för hantering av nyckel livs cykeln
- Ytterligare konfiguration & konfigurations omkostnader

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kryptering på Server sidan med Kundhanterade nycklar i kundkontrollerad maskin vara

Vissa Azure-tjänster aktiverar nyckel hanterings modellen värd för din egen nyckel (HYOK). Det här hanterings läget är användbart i scenarier där det finns behov av att kryptera data i vila och hantera nycklarna i en egen lagrings plats utanför Microsofts kontroll. I den här modellen måste tjänsten hämta nyckeln från en extern plats. Prestanda-och tillgänglighets garantier påverkas och konfigurationen är mer komplex. Eftersom tjänsten har åtkomst till DEK under kryptering och dekryptering, liknar de övergripande säkerhets garantirna för den här modellen när nycklarna är kund hanterade i Azure Key Vault.  Därför är den här modellen inte lämplig för de flesta organisationer om de inte har särskilda krav på nyckel hantering. På grund av dessa begränsningar stöder de flesta Azure-tjänster inte kryptering på Server sidan med hjälp av Server-hanterade nycklar i kundkontrollerad maskin vara.

### <a name="key-access"></a>Nyckel åtkomst

När kryptering på Server sidan med tjänst hanterade nycklar i kundkontrollerad maskin vara används, bevaras nycklarna på ett system som kon figurer ATS av kunden. Azure-tjänster som stöder den här modellen ger ett sätt att upprätta en säker anslutning till en kund som har angett nyckel lager.

**Fördelar**

- Fullständig kontroll över den rot nyckel som används – krypterings nycklar hanteras av en kund som har tillhandahållit ett arkiv
- Möjlighet att kryptera flera tjänster till en huvud server
- Kan åtskilja nyckel hantering från den övergripande hanterings modellen för tjänsten
- Kan definiera tjänst-och nyckel plats mellan regioner

**Nackdelar**

- Fullständigt ansvar för nyckel lagring, säkerhet, prestanda och tillgänglighet
- Fullständigt ansvar för hantering av nyckel åtkomst
- Fullständigt ansvar för nyckel livs cykel hantering
- Viktiga kostnader för konfiguration, konfiguration och löpande underhåll
- Ökat beroende på nätverks tillgänglighet mellan kundens Data Center och Azure-datacenter.

## <a name="supporting-services"></a>Stöd tjänster
De Azure-tjänster som har stöd för varje krypterings modell:

| Produkt, funktion eller tjänst | Server-Side att använda Service-Managed nyckel   | Server-Side att använda Customer-Managed nyckel | Client-Side att använda Client-Managed nyckel  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI och maskininlärning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio (klassisk) | Ja         | För hands version, RSA 2048-bitars | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Ansikte                             | Ja                | Ja                | -                  |
| Language Understanding           | Ja                | Ja                | -                  |
| Personanpassning                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Speech Services                  | Ja                | Ja                | -                  |
| Translator Text                  | Ja                | Ja                | -                  |
| Power BI                         | Ja                | Ja, RSA 4096-bitars  | -                  |
| **Analys**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | Ja\*\*            | -                  |
| Event Hubs                       | Ja                | Ja                | -                  |
| Functions                        | Ja                | Ja                | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Azure HDInsight                  | Ja                | Alla                | -                  |
| Azure Monitor Application Insights | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics      | Ja                | Ja                | -                  |
| Azure-datautforskaren              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bitars  | -                  |
| **Containrar**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Container Registry               | Ja                | Ja                | -                  |
| **Beräkning**                      |                    |                    |                    |
| Virtual Machines                 | Ja                | Ja                | -                  |
| Skalnings uppsättning för virtuell dator        | Ja                | Ja                | -                  |
| SAP HANA                         | Ja                | Ja                | -                  |
| App Service                      | Ja                | Ja\*\*            | -                  |
| Automation                       | Ja                | Ja\*\*            | -                  |
| Azure Functions                  | Ja                | Ja\*\*            | -                  |
| Azure Portal                     | Ja                | Ja\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Azure-hanterade program       | Ja                | Ja\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Site Recovery                    | Ja                | Ja                | -                  |
| **Databaser**                    |                    |                    |                    |
| SQL Server på virtuella datorer   | Ja                | Ja                | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 3072-bitars  | Ja                |
| Azure SQL Database för MariaDB   | Ja                | -                  | -                  |
| Azure SQL Database för MySQL     | Ja                | Ja                | -                  |
| Azure SQL Database för PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Ja                | Ja, RSA 3072-bitars  | -                  |
| SQL Server Stretch Database      | Ja                | Ja, RSA 3072-bitars  | Ja                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration Service | Ja                | Ej tillämpligt\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Ja                | -                  | -                  |
| Azure-lagringsplatser                      | Ja                | -                  | -                  |
| **Identitet**                     |                    |                    |                    |
| Azure Active Directory           | Ja                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja                | -                  |
| **Integrering**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Ja                | -                  | -                  |
| API Management                   | Ja                | -                  | -                  |
| **IoT-tjänster**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| IoT Hub Device Provisioning      | Ja                | Ja                | -                  |
| **Hantering och styrning**    |                    |                    |                    |
| Azure Site Recovery              | Ja                | -                  | -                  |
| Azure Migrate                    | Ja                | Ja                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Ja                | Ja                | Ja                |
| **Säkerhet**                     |                    |                    |                    |
| Azure Security Center för IoT    | Ja                | Ja                | -                  |
| Azure Sentinel                   | Ja                | Ja                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja                | Ja                |
| Premium-Blob Storage             | Ja                | Ja                | Ja                |
| Disklagring                     | Ja                | Ja                | -                  |
| Ultra Disklagring               | Ja                | Ja                | -                  |
| Hanterade Disklagring             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja                | -                  |
| Fil Premium Storage             | Ja                | Ja                | -                  |
| File Sync                         | Ja                | Ja                | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure Cache for Redis            | Ja                | Ej tillämpligt\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Arkivlagring                  | Ja                | Ja                | -                  |
| StorSimple                       | Ja                | Ja                | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\* Den här tjänsten bevarar inte data. Tillfälliga cacheminnen, om det finns, krypteras med en Microsoft-nyckel.

\*\* Den här tjänsten har stöd för att lagra data i din egen Key Vault, lagrings konto eller annan data tjänst som redan stöder Server-Side kryptering med Customer-Managed nyckel.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [kryptering används i Azure](encryption-overview.md).
- Lär dig hur Azure använder [Double Encryption](double-encryption.md) för att minimera hot som kommer att kryptera data.
