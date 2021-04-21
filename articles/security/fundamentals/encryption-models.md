---
title: Datakrypteringsmodeller i Microsoft Azure
description: Den här artikeln innehåller en översikt över datakrypteringsmodeller i Microsoft Azure.
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
ms.openlocfilehash: 95ab5917779a73b7221a5b431126164aef88b494
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812128"
---
# <a name="data-encryption-models"></a>Datakrypteringsmodeller

En förståelse för de olika krypteringsmodellerna och deras för- och nackdelar är viktig för att förstå hur de olika resursproviders i Azure implementerar kryptering i vila. Dessa definitioner delas mellan alla resursproviders i Azure för att säkerställa gemensamt språk och taxonomi.

Det finns tre scenarier för kryptering på serversidan:

- Kryptering på serversidan med hjälp Service-Managed nycklar
  - Azure-resursproviders utför krypterings- och dekrypteringsåtgärder
  - Microsoft hanterar nycklarna
  - Fullständig molnfunktionalitet

- Kryptering på serversidan med kund hanterade nycklar i Azure Key Vault
  - Azure-resursproviders utför krypterings- och dekrypteringsåtgärder
  - Kunden styr nycklar via Azure Key Vault
  - Fullständig molnfunktionalitet

- Kryptering på serversidan med kundstyrda nycklar på kundkontrollerad maskinvara
  - Azure-resursproviders utför krypterings- och dekrypteringsåtgärder
  - Kunden kontrollerar nycklar på kundkontrollerad maskinvara
  - Fullständig molnfunktionalitet

Krypteringsmodeller på serversidan avser kryptering som utförs av Azure-tjänsten. I den modellen utför resursprovidern åtgärderna för att kryptera och dekryptera. Till exempel Azure Storage ta emot data i klartextåtgärder och utför kryptering och dekryptering internt. Resursprovidern kan använda krypteringsnycklar som hanteras av Microsoft eller av kunden beroende på den angivna konfigurationen.

![Server](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Var och en av modellerna för kryptering på serversidan i vila innebär egenskaper hos nyckelhantering. Detta omfattar var och hur krypteringsnycklar skapas och lagras samt åtkomstmodellerna och procedurerna för nyckelrotation.  

Tänk på följande för kryptering på klientsidan:

- Azure-tjänster kan inte se dekrypterade data
- Kunder hanterar och lagrar nycklar lokalt (eller i andra säkra butiker). Nycklar är inte tillgängliga för Azure-tjänster
- Minskad molnfunktionalitet

Krypteringsmodellerna som stöds i Azure är uppdelade i två huvudgrupper: "Klientkryptering" och "Kryptering på serversidan" som nämnts tidigare. Oavsett vilken kryptering i vila-modellen som används rekommenderar Azure-tjänster alltid att du använder en säker transport, till exempel TLS eller HTTPS. Kryptering inom transport bör därför hanteras av transportprotokollet och bör inte vara en viktig faktor för att avgöra vilken krypteringsmodell i vila som ska användas.

## <a name="client-encryption-model"></a>Klientkrypteringsmodell

Klientkrypteringsmodellen avser kryptering som utförs utanför resursprovidern eller Azure av tjänsten eller det anropande programmet. Krypteringen kan utföras av tjänstprogrammet i Azure eller av ett program som körs i kundens datacenter. I båda fallen, när den här krypteringsmodellen används, tar Azure-resursprovidern emot en krypterad blob med data utan möjlighet att dekryptera data på något sätt eller ha åtkomst till krypteringsnycklarna. I den här modellen utförs nyckelhanteringen av den anropande tjänsten/programmet och är täckande för Azure-tjänsten.

![Klient](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Kryptering på serversidan med tjänst hanterade nycklar

För många kunder är det viktigaste kravet att säkerställa att data krypteras när de är i vila. Kryptering på serversidan med tjänst hanterade nycklar möjliggör den här modellen genom att låta kunder markera den specifika resursen (lagringskonto, SQL DB osv.) för kryptering och låta alla nyckelhanteringsaspekter som utfärdande, rotation och säkerhetskopiering av nycklar vara kvar på Microsoft. De flesta Azure-tjänster som stöder kryptering i vila stöder vanligtvis den här modellen för avlastning av hanteringen av krypteringsnycklarna till Azure. Azure-resursprovidern skapar nycklarna, placerar dem i säker lagring och hämtar dem när det behövs. Det innebär att tjänsten har fullständig åtkomst till nycklarna och att tjänsten har fullständig kontroll över livscykelhanteringen av autentiseringsuppgifter.

![Hanterade](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Kryptering på serversidan med tjänst hanterade nycklar åtgärdar därför snabbt behovet av kryptering i vila med låga kostnader för kunden. När det är tillgängligt öppnar en kund Azure Portal för målprenumerationen och resursprovidern och kontrollerar en ruta som anger att de vill att data ska krypteras. I vissa Resource Managers är kryptering på serversidan med tjänst hanterade nycklar på som standard.

Kryptering på serversidan med Microsoft-hanterade nycklar innebär att tjänsten har fullständig åtkomst till att lagra och hantera nycklarna. Vissa kunder kanske vill hantera nycklarna eftersom de anser att de får bättre säkerhet, men kostnaden och risken med en anpassad nyckellagringslösning bör beaktas vid utvärdering av den här modellen. I många fall kan en organisation fastställa att resursbegränsningar eller risker i en lokal lösning kan vara större än risken för molnhantering av kryptering vid vilonycklar.  Den här modellen kanske dock inte är tillräcklig för organisationer som har krav på att styra skapandet eller livscykeln för krypteringsnycklarna eller för att ha annan personal som hanterar en tjänsts krypteringsnycklar än de som hanterar tjänsten (d.v.s. uppdelning av nyckelhantering från den övergripande hanteringsmodellen för tjänsten).

### <a name="key-access"></a>Nyckelåtkomst

När kryptering på serversidan med tjänst hanterade nycklar används, hanteras alla nyckelskapande, lagring och tjänståtkomst av tjänsten. Normalt lagrar de grundläggande Azure-resursproviders datakrypteringsnycklarna i ett arkiv som ligger nära data och som snabbt är tillgängligt och tillgängligt medan nyckelkrypteringsnycklarna lagras i ett säkert internt arkiv.

**Fördelar**

- Enkel installation
- Microsoft hanterar nyckelrotation, säkerhetskopiering och redundans
- Kunden har inte den kostnad som är kopplad till implementeringen eller risken för ett anpassat nyckelhanteringsschema.

**Nackdelar**

- Ingen kundkontroll över krypteringsnycklarna (nyckelspecifikation, livscykel, återkallelse osv.)
- Ingen möjlighet att åtse nyckelhantering från den övergripande hanteringsmodellen för tjänsten

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kryptering på serversidan med kund hanterade nycklar i Azure Key Vault

I scenarier där kravet är att kryptera vilodata och kontrollera krypteringsnycklarna kan kunderna använda kryptering på serversidan med kund hanterade nycklar i Key Vault. Vissa tjänster kan bara lagra rotnyckelns krypteringsnyckel i Azure Key Vault och lagra den krypterade datakrypteringsnyckeln på en intern plats närmare data. I det scenariot kan kunder ta med sina egna nycklar till Key Vault (BYOK – Bring Your Own Key) eller generera nya och använda dem för att kryptera de önskade resurserna. Medan resursprovidern utför krypterings- och dekrypteringsåtgärder använder den den konfigurerade nyckelkrypteringsnyckeln som rotnyckel för alla krypteringsåtgärder.

Förlust av nyckelkrypteringsnycklar innebär dataförlust. Därför bör nycklar inte tas bort. Nycklar bör säkerhetskopieras när de skapas eller roteras. [Mjuk borttagning ska](../../key-vault/general/soft-delete-overview.md) vara aktiverat på alla valv som lagrar nyckelkrypteringsnycklar. I stället för att ta bort en nyckel anger du aktiverat till falskt eller anger förfallodatumet.

### <a name="key-access"></a>Nyckelåtkomst

Krypteringsmodellen på serversidan med kund hanterade nycklar i Azure Key Vault innebär att tjänsten får åtkomst till nycklarna för att kryptera och dekryptera efter behov. Kryptering i vila-nycklar görs tillgängliga för en tjänst via en princip för åtkomstkontroll. Den här principen ger tjänstidentiteten åtkomst till att ta emot nyckeln. En Azure-tjänst som körs för en associerad prenumeration kan konfigureras med en identitet i prenumerationen. Tjänsten kan utföra Azure Active Directory och ta emot en autentiseringstoken som identifierar sig som den tjänst som agerar för prenumerationen. Denna token kan sedan visas för Key Vault för att hämta en nyckel som den har fått åtkomst till.

För åtgärder som använder krypteringsnycklar kan en tjänstidentitet beviljas åtkomst till någon av följande åtgärder: dekryptera, kryptera, unwrapKey, wrapKey, verifiera, signera, hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera och återställa.

För att hämta en nyckel för användning vid kryptering eller dekryptering av vilodata, måste tjänstidentiteten som Resource Manager-tjänstinstansen kör som ha UnwrapKey (för att hämta nyckeln för dekryptering) och WrapKey (för att infoga en nyckel i nyckelvalvet när du skapar en ny nyckel).

>[!NOTE]
>Mer information om hur Key Vault auktorisering finns på sidan skydda ditt nyckelvalv i [Azure Key Vault dokumentationen](../../key-vault/general/security-features.md).

**Fördelar**

- Fullständig kontroll över de nycklar som används – krypteringsnycklar hanteras i kundens Key Vault under kundens kontroll.
- Möjlighet att kryptera flera tjänster till en huvudtjänst
- Kan åtsegrera nyckelhantering från den övergripande hanteringsmodellen för tjänsten
- Kan definiera tjänst- och nyckelplats mellan regioner

**Nackdelar**

- Kunden har det fullständiga ansvaret för nyckelåtkomsthantering
- Kunden har fullständigt ansvar för nyckellivscykelhantering
- Ytterligare konfiguration & konfigurationskostnader

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kryptering på serversidan med kundstyrda nycklar i kundkontrollerad maskinvara

Vissa Azure-tjänster aktiverar HYOK-nyckelhanteringsmodellen (Host Your Own Key). Det här hanteringsläget är användbart i scenarier där det finns ett behov av att kryptera vilodata och hantera nycklarna i en upphovsrättsskyddad lagringsplats utanför Microsofts kontroll. I den här modellen måste tjänsten hämta nyckeln från en extern plats. Prestanda- och tillgänglighetsgarantier påverkas och konfigurationen är mer komplex. Eftersom tjänsten dessutom har åtkomst till DEK under krypterings- och dekrypteringsåtgärder liknar de övergripande säkerhetsgarantierna för den här modellen när nycklarna hanteras av kunden i Azure Key Vault.  Därför är den här modellen inte lämplig för de flesta organisationer om de inte har särskilda nyckelhanteringskrav. På grund av dessa begränsningar stöder de flesta Azure-tjänster inte kryptering på serversidan med hjälp av server-hanterade nycklar i kundkontrollerad maskinvara.

### <a name="key-access"></a>Nyckelåtkomst

När kryptering på serversidan med tjänst hanterade nycklar i kundkontrollerad maskinvara används, underhålls nycklarna i ett system som konfigurerats av kunden. Azure-tjänster som stöder den här modellen är ett sätt att upprätta en säker anslutning till ett nyckelarkiv som kunden tillhandahåller.

**Fördelar**

- Fullständig kontroll över den rotnyckel som används – krypteringsnycklar hanteras av ett butik som tillhandahålls av kunden
- Möjlighet att kryptera flera tjänster till en huvudtjänst
- Kan åtse nyckelhantering från den övergripande hanteringsmodellen för tjänsten
- Kan definiera tjänst- och nyckelplats mellan regioner

**Nackdelar**

- Fullständigt ansvar för nyckellagring, säkerhet, prestanda och tillgänglighet
- Fullständigt ansvar för nyckelåtkomsthantering
- Fullständigt ansvar för nyckellivscykelhantering
- Betydande kostnader för installation, konfiguration och löpande underhåll
- Ökat beroende av nätverkstillgänglighet mellan kundens datacenter och Azure-datacenter.

## <a name="supporting-services"></a>Stödtjänster
De Azure-tjänster som stöder varje krypteringsmodell:

| Produkt, funktion eller tjänst | Server-Side använda Service-Managed nyckel   | Server-Side använda Customer-Managed nyckel | Client-Side använda Client-Managed nyckel  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI och maskininlärning**      |                    |                    |                    |
| Azure Cognitive Search           | Ja                | Ja                | -                  |
| Azure Cognitive Services         | Ja                | Ja                | -                  |
| Azure Machine Learning           | Ja                | Ja                | -                  |
| Azure Machine Learning Studio (klassisk) | Yes         | Förhandsversion, RSA 2048-bitars | -               |
| Content Moderator                | Ja                | Ja                | -                  |
| Ansikte                             | Ja                | Ja                | -                  |
| Language Understanding           | Ja                | Ja                | -                  |
| Personanpassning                     | Ja                | Ja                | -                  |
| QnA Maker                        | Ja                | Ja                | -                  |
| Speech Services                  | Ja                | Ja                | -                  |
| Translator Text                  | Ja                | Ja                | -                  |
| Power BI                         | Yes                | Ja, RSA 4096-bitars  | -                  |
| **Analys**                    |                    |                    |                    |
| Azure Stream Analytics           | Yes                | Ja\*\*            | -                  |
| Event Hubs                       | Ja                | Ja                | -                  |
| Functions                        | Ja                | Ja                | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Yes                | -                  | -                  |
| Azure HDInsight                  | Yes                | Alla                | -                  |
| Azure Monitor Application Insights | Ja                | Ja                | -                  |
| Azure Monitor Log Analytics      | Ja                | Ja                | -                  |
| Öppna Azure-datautforskaren              | Ja                | Ja                | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bitars  | -                  |
| **Containrar**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | Ja                | -                  |
| Container Instances              | Ja                | Ja                | -                  |
| Container Registry               | Ja                | Ja                | -                  |
| **Beräkning**                      |                    |                    |                    |
| Virtual Machines                 | Ja                | Ja                | -                  |
| VM-skalningsuppsättning        | Ja                | Ja                | -                  |
| SAP HANA                         | Ja                | Ja                | -                  |
| App Service                      | Yes                | Ja\*\*            | -                  |
| Automation                       | Yes                | Ja\*\*            | -                  |
| Azure Functions                  | Yes                | Ja\*\*            | -                  |
| Azure Portal                     | Yes                | Ja\*\*            | -                  |
| Logic Apps                       | Ja                | Ja                | -                  |
| Azure-hanterade program       | Yes                | Ja\*\*            | -                  |
| Service Bus                      | Ja                | Ja                | -                  |
| Site Recovery                    | Ja                | Ja                | -                  |
| **Databaser**                    |                    |                    |                    |
| SQL Server på virtuella datorer   | Ja                | Ja                | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 3072-bitars  | Yes                |
| Azure SQL Database för MariaDB   | Yes                | -                  | -                  |
| Azure SQL Database for MySQL     | Ja                | Ja                | -                  |
| Azure SQL Database för PostgreSQL | Ja               | Ja                | -                  |
| Azure Synapse Analytics          | Yes                | Ja, RSA 3072-bitars  | -                  |
| SQL Server Stretch Database      | Yes                | Ja, RSA 3072-bitars  | Yes                |
| Table Storage                    | Ja                | Ja                | Ja                |
| Azure Cosmos DB                  | Ja                | Ja                | -                  |
| Azure Databricks                 | Ja                | Ja                | -                  |
| Azure Database Migration Service | Yes                | Ej A\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Yes                | -                  | -                  |
| Azure-lagringsplatser                      | Yes                | -                  | -                  |
| **Identitet**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja                | -                  |
| **Integrering**                  |                    |                    |                    |
| Service Bus                      | Ja                | Ja                | Ja                |
| Event Grid                       | Yes                | -                  | -                  |
| API Management                   | Yes                | -                  | -                  |
| **IoT-tjänster**                 |                    |                    |                    |
| IoT Hub                          | Ja                | Ja                | Ja                |
| IoT Hub Device Provisioning      | Ja                | Ja                | -                  |
| **Hantering och styrning**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | -                  | -                  |
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
| Ultra Disk Storage               | Ja                | Ja                | -                  |
| Hanterade Disklagring             | Ja                | Ja                | -                  |
| File Storage                     | Ja                | Ja                | -                  |
| Fil Premium Storage             | Ja                | Ja                | -                  |
| File Sync                         | Ja                | Ja                | -                  |
| Queue Storage                    | Ja                | Ja                | Ja                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Azure Cache for Redis            | Yes                | Ej A\*              | -                  |
| Azure NetApp Files               | Ja                | Ja                | -                  |
| Arkivlagring                  | Ja                | Ja                | -                  |
| StorSimple                       | Ja                | Ja                | Ja                |
| Azure Backup                     | Ja                | Ja                | Ja                |
| Data Box                         | Ja                | -                  | Ja                |
| Data Box Edge                    | Ja                | Ja                | -                  |

\* Den här tjänsten bevarar inte data. Tillfälliga cacheminnen, om några, krypteras med en Microsoft-nyckel.

\*\* Den här tjänsten stöder lagring av data i din Key Vault, lagringskonto eller annan tjänst för datalagring som redan stöder Server-Side kryptering med Customer-Managed nyckel.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur kryptering används i Azure](encryption-overview.md).
- Lär dig hur Azure [använder dubbel](double-encryption.md) kryptering för att minimera hot som kommer med kryptering av data.
