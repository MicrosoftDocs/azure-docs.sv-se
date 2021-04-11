---
title: Vad är Dedikerad HSM? – Dedikerad HSM i Azure| Microsoft Docs
description: Lär dig hur Azure Dedicated HSM är en Azure-tjänst som tillhandahåller lagring av kryptografiska nycklar i Azure.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0e07839c3c5ce542335eeadc92e6a3c98fe87856
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606988"
---
# <a name="what-is-azure-dedicated-hsm"></a>Vad är Dedikerad HSM i Azure?

Dedikerad HSM i Azure är en Azure-tjänst som tillhandahåller lagring av kryptografiska nycklar i Azure. Dedikerad HSM uppfyller de strängaste säkerhetskraven. Det är den perfekta lösningen för kunder som behöver FIPS 140-2 Level 3-validerade enheter och fullständig och exklusiv kontroll över HSM-apparaten. 

 HSM enheter distribueras globalt över flera Azure-regioner. De kan enkelt etableras som ett par av enheter och konfigureras för hög tillgänglighet. HSM-enheter kan även etableras över regioner för att säkerställa redundans på regionsnivå. Microsoft levererar en dedikerad HSM-tjänst genom att använda [Thales-A790 för Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -enheter. Den här enheten erbjuder prestanda och alternativ för kryptografisk integrering på högsta nivå. 

När de är etablerade så är HSM-enheter direkt anslutna till en kunds virtuella nätverk. De kan även användas av lokala program och hanteringsverktyg när du konfigurerar en VPN-anslutning från punkt-till-plats eller plats-till-plats. Kunderna får program varan och dokumentationen för att konfigurera och hantera HSM-enheter från [Thales kund support Portal](https://supportportal.thalesgroup.com/csm).

## <a name="why-use-azure-dedicated-hsm"></a>Varför bör du använda Azure Dedikerad HSM?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 Level-3-efterlevnad

Många organisationer har stränga bransch bestämmelser som styr att de kryptografiska nycklarna måste lagras i [FIPS 140-2-nivå 3-](https://csrc.nist.gov/publications/detail/fips/140/2/final) verifierade HSM: er. Azures dedikerad HSM och ett nytt erbjudande för enskild klient, [Azure Key Vault hanterad HSM (för hands version)](https://docs.microsoft.com/azure/key-vault/managed-hsm), hjälper kunder från olika bransch segment, till exempel ekonomi tjänster, myndigheter och andra som uppfyller FIPS 140-2-nivå 3-krav. Även om Microsofts [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) tjänst för flera innehavare använder för närvarande FIPS 140-2 nivå 2-verifierade HSM: er. 

### <a name="single-tenant-devices"></a>Enheter för en enda klient

Många av våra kunder har ett krav för enskilt innehav av den kryptografiska lagringsenheten. Azure Dedikerad HSM-tjänsten låter dem etablera en fysisk enhet från någon av Microsofts globalt distribuerade datacenter. När enheten har etablerats till en kund kan endast den kunden komma åt enheten.

### <a name="full-administrative-control"></a>Fullständig administrativ kontroll

Många kunder behöver fullständig administrativ kontroll och enskild åtkomst till sin enhet i administrativt syfte. Efter att en enhet har etablerats så har bara den kunden åtkomst till enheten på administrativ eller programnivå.

 Microsoft har ingen administrativ kontroll efter att kunden anslutit till enheten för första gången och ändrar lösenordet. Därefter är kunden en äkta enskild klient med fullständig administrativ kontroll och programhanteringsfunktion. Microsoft har kvar behörighet på övervakningsnivå (inte en administratörsroll) för telemetri via den seriella portanslutningen. Den här åtkomsten omfattar maskinvaruövervakning som temperatur, hälsotillstånd för strömförsörjning och fläkt. 
 
 Det står kunden fritt att inaktivera den här övervakningen vid behov. Om de inaktiverar den så kommer de dock inte att få proaktiva hälsovarningar från Microsoft.

### <a name="high-performance"></a>Höga prestanda

Thales-enheten valdes för den här tjänsten av olika orsaker. Den har ett brett stöd för krypteringsalgoritmer, en mängd olika operativsystem och ett brett API-stöd. Den specifika modell som distribueras erbjuder utmärkta prestanda med 10 000 åtgärder per sekund för RSA-2048. Den har stöd för 10 partitioner som kan användas för unika programinstanser. Det här är en enhet med låg svarstid, hög kapacitet och högt dataflöde.

### <a name="unique-cloud-based-offering"></a>Unikt molnbaserat erbjudande

Microsoft identifierade ett specifikt behov för en unik uppsättning kunder. Det är den enda molnleverantör som erbjuder nya kunder en dedikerad HSM-tjänst som är validerad för FIPS 140-2 Level-3 och erbjuder en så omfattande molnbaserad och lokal programintegrering.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Är Dedikerad HSM i Azure rätt val för dig?

Azure dedikerad HSM är en specialiserad tjänst som uppfyller unika krav hos en specifik typ av storskaliga organisationer. Därför förväntas de flesta Azure-kunder inte passa profilen för användning av den här tjänsten. För många är Azure Key Vault-tjänsten ett lämpligare och mer kostnadseffektivt alternativ. Vi har identifierat följande kriterier för att hjälpa dig avgöra huruvida tjänsten passar dig.

### <a name="best-fit"></a>Passar bäst

Azure dedikerad HSM lämpar sig bäst för ”lift and shift”-scenarier som kräver direkt och enskild åtkomst till HSM-enheter. Exempel:

- Migrera program från en lokal plats till virtuella Azure-datorer
- Migrera program från Amazon AWS EC2 till virtuella datorer som använder tjänsten AWS Cloud HSM Classic (Amazon erbjuder inte denna tjänst för nya kunder)
- Köra kommersiell programvara på virtuella Azure-datorer såsom Apache/Ngnix SSL Offload, Oracle TDE och ADCS 

### <a name="not-a-fit"></a>Passar inte

Azure Dedicated HSM passar inte bra för följande typer av scenario: Microsofts moln tjänster som stöder kryptering med Kundhanterade nycklar (till exempel Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database och kund nyckel för Office 365) som inte är integrerade med Azure Dedicated HSM.

### <a name="it-depends"></a>Det beror på

Om Azure dedikerad HSM fungerar för dig beror på en potentiellt komplex blandning av krav och kompromisser som du kan eller inte kan göra. Ett exempel är FIPS 140-2 Level-3-kravet. Detta krav är gemensamt och Azures dedikerad HSM och en ny klient erbjudande [Azure Key Vault hanterad HSM (för hands version)](https://docs.microsoft.com/azure/key-vault/managed-hsm) är för närvarande de enda alternativen för att uppfylla den. Om dessa krav inte är relevanta är det ofta ett val mellan Azure Key Vault och Azures dedikerad HSM. Utvärdera dina krav innan du fattar ett beslut.

Situationer där du måste väga dina alternativ är: 

- Ny kod som körs på en kunds virtuella Azure-dator
- SQL Server TDE på en virtuell Azure-dator
- Kryptering på klientsidan med Azure Storage
- SQL Server och Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Nästa steg

Det här är en höggradigt specialiserad tjänst. Därför rekommenderar vi att du förstår nyckelbegreppen i den här dokumentationen, inklusive prissättning, support och servicenivåavtal. 

[Thales-integrerings guiderna](https://cpl.thalesgroup.com/partners/overview) hjälper dig att under lätta etableringen av HSM: er i en befintlig virtuell nätverks miljö. Det finns också instruktions guider som hjälper dig att avgöra hur du konfigurerar distributions arkitekturen.

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
