---
title: Dataåtkomststrategier
description: Azure Data Factory stöder nu statiska IP-adressintervall.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: edc773ec2db078b6c50b55c81ad6570758a3f5f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389254"
---
# <a name="data-access-strategies"></a>Dataåtkomststrategier

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ett viktigt mål för en organisation är att skydda sina data lager från slumpmässig åtkomst via Internet, och det kan vara ett lokalt eller ett moln/SaaS data lager. 

Normalt kontrollerar ett moln data lager åtkomst med hjälp av nedanstående mekanismer:
* Privat länk från en Virtual Network till data källor med privat slut punkt som är aktiverade
* Brand Väggs regler som begränsar anslutningarna med IP-adress
* Autentiseringsmekanismer som kräver att användare bekräftar sin identitet
* Auktoriserings metoder som begränsar användare till vissa åtgärder och data

> [!TIP]
> Med [introduktionen av statiska IP-](./azure-integration-runtime-ip-addresses.md)adressintervall kan du nu tillåta List-IP-intervall för en viss Azure integration runtime-region så att du inte behöver tillåta alla Azure IP-adresser i dina moln data lager. På så sätt kan du begränsa de IP-adresser som tillåts komma åt data lager.

> [!NOTE] 
> IP-adressintervall blockeras för Azure Integration Runtime och används för närvarande endast för data förflyttning, pipeline och externa aktiviteter. Data flöden och Azure Integration Runtime som aktiverar hanterade Virtual Network använder nu inte dessa IP-intervall. 

Detta bör fungera i många olika scenarier och vi förstår att en unik statisk IP-adress per integration runtime är önskvärd, men det skulle inte vara möjligt att använda Azure Integration Runtime för närvarande, som är Server lös. Vid behov kan du alltid konfigurera en egen värd Integration Runtime och använda din statiska IP-adress. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Data åtkomst strategier via Azure Data Factory

* **[Privat länk](../private-link/private-link-overview.md)** – du kan skapa en Azure Integration Runtime i Azure Data Factory hanterade Virtual Network så utnyttjar privata slut punkter för att på ett säkert sätt ansluta till data lager som stöds. Trafik mellan hanterade Virtual Network och data källor bevarar Microsoft stamnät nätverket och exponeras inte för offentliga nätverk.
* **[Trusted service](../storage/common/storage-network-security.md#exceptions)** -Azure Storage (Blob, ADLS Gen2) stöder brand Väggs konfiguration som gör det möjligt att välja betrodda Azure Platform Services för att komma åt lagrings kontot säkert. Betrodda tjänster upprätthåller hanterad identitetsautentisering, vilket innebär att ingen annan data fabrik kan ansluta till den här lagringen om den inte godkänns för att göra det med hjälp av den hanterade identiteten. Du hittar mer information i **[den här bloggen](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)**. Detta är därför mycket säkert och rekommenderat. 
* **Unik statisk IP** – du måste konfigurera en integration runtime med egen värd för att få en statisk IP-adress för Data Factory anslutningar. Den här mekanismen garanterar att du kan blockera åtkomst från alla andra IP-adresser. 
* **[Statiskt IP-intervall](./azure-integration-runtime-ip-addresses.md)** – du kan använda Azure integration RUNTIMES IP-adresser för att tillåta en lista i din lagring (t. ex. S3, Salesforce osv.). Det begränsar i sin tur IP-adresser som kan ansluta till data lager, men som också förlitar sig på autentiserings-/auktoriseringsregler.
* **[Service tag](../virtual-network/service-tags-overview.md)** – en service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst (t. ex. Azure Data Factory). Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler. Det är användbart när du filtrerar data åtkomst på IaaS värdbaserade data lager i Virtual Network.
* **Tillåt Azure-tjänster** – vissa tjänster låter dig tillåta alla Azure-tjänster att ansluta till den om du väljer det här alternativet. 

Mer information om de mekanismer för nätverks säkerhet som stöds på data lager i Azure Integration Runtime och Integration Runtime med egen värd finns under två tabeller.  
* **Azure Integration Runtime**

    | Datalager                  | Mekanism för nätverks säkerhet som stöds på data lager | Private Link     | Betrodd tjänst     | Statiskt IP-intervall | Tjänsttaggar | Tillåt Azure-tjänster |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Data lager för Azure PaaS       | Azure Cosmos DB                                     | Ja              | -                   | Ja             | -            | Ja                  |
    |                              | Öppna Azure-datautforskaren                                 | -                | -                   | Ja*            | Ja*         | -                    |
    |                              | Azure Data Lake gen1                                | -                | -                   | Ja             | -            | Ja                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | Ja             | -            | Ja                  |
    |                              | Azure File Storage                                  | Ja              | -                   | Ja             | -            | .                    |
    |                              | Azure Storage (BLOB, ADLS Gen2)                     | Ja              | Ja (endast MSI-autentisering) | Ja             | -            | .                    |
    |                              | Azure SQL DB, Azure Synapse Analytics), SQL ml  | Ja (endast Azure SQL DB/DW)        | -                   | Ja             | -            | Ja                  |
    |                              | Azure Key Vault (för hämtning av hemligheter/anslutnings sträng) | ja      | Ja                 | Ja             | -            | -                    |
    | Andra data lager för PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage osv.    | -                | -                   | Ja             | -            | -                    |
    | Azure-laaS                   | SQL Server, Oracle osv.                          | -                | -                   | Ja             | Ja          | -                    |
    | Lokala laaS              | SQL Server, Oracle osv.                          | -                | -                   | Ja             | -            | -                    |
    
    **Används endast när Azure Datautforskaren är inmatat i virtuella nätverk och IP-intervall kan tillämpas på NSG/brand vägg.* 

* **Egen värd Integration Runtime (i VNet/lokalt)**
    
    | Data lager                  | Mekanism för nätverks säkerhet som stöds på data lager         | Statisk IP | Betrodda tjänster  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Data lager för Azure PaaS       | Azure Cosmos DB                                               | Ja       | -                   |
    |                                | Öppna Azure-datautforskaren                                           | -         | -                   |
    |                                | Azure Data Lake gen1                                          | Ja       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Ja       | -                   |
    |                                | Azure File Storage                                            | Ja       | -                   |
    |                                | Azure Storage (blogg, ADLS Gen2)                             | Ja       | Ja (endast MSI-autentisering) |
    |                                | Azure SQL DB, Azure Synapse Analytics), SQL ml          | Ja       | -                   |
    |                                | Azure Key Vault (för hämtning av hemligheter/anslutnings sträng) | Ja       | Ja                 |
    | Andra data lager för PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage osv.              | Ja       | -                   |
    | Azure-laaS                     | SQL Server, Oracle osv.                                  | Ja       | -                   |
    | Lokala laaS              | SQL Server, Oracle osv.                                  | Ja       | -                   |    

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande relaterade artiklar:
* [Datalager som stöds](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Azure Key Vault betrodda tjänster](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Azure Storage betrodda Microsoft-tjänster](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Hanterad identitet för Data Factory](./data-factory-service-identity.md)