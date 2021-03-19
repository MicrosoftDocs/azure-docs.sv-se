---
title: Vanliga frågor om Analysis Services nätverks anslutning | Microsoft Docs
description: Den här artikeln innehåller svar på några av de vanligaste frågorna om Analysis Services nätverks anslutning.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82838509"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Vanliga frågor om Analysis Services-nätverksanslutning

Den här artikeln innehåller svar på vanliga frågor om att ansluta till lagrings konton, data källor, brand väggar och IP-adresser.

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

**Fråga** – hur kan jag säkerhetskopiera och återställa med ett lagrings konto som ligger bakom en brand vägg?   
**Svars** Azure Analysis Services använder inte fasta IP-adresser eller service märken. Intervallet av IP-adresser som Analysis Services servrar använder kan vara vad som helst i intervallet av IP-adresser för *Azure-regionen*. Eftersom serverns IP-adresser är variabla och kan ändras över tid, måste brand Väggs reglerna tillåtas för hela intervallet av IP-adresser för Azure-regioner som servern finns i.

**Fråga** – mitt Azure Storage-konto finns i en annan region än min Analysis Services-server. Hur gör jag för att konfigurera brand Väggs inställningar för lagrings kontot?   
**Svar** – om lagrings kontot finns i en annan region konfigurerar du inställningarna för lagrings konto brand väggen för att tillåta åtkomst från **valda nätverk**. I brand Väggs **adress intervall** anger du IP-adressintervallet för den region som Analysis Services-servern finns i. För att hämta IP-intervall för Azure-regioner, se [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519). Det finns stöd för att konfigurera brand Väggs inställningar för lagrings konto för att tillåta åtkomst från alla nätverk, men att välja valda nätverk och ange ett IP-adressintervall rekommenderas. 

**Fråga** – mitt Azure Storage-konto finns i samma region som min Analysis Services-server. Hur kan jag konfigurera brand Väggs inställningar för lagrings kontot?   
**Svar** – eftersom din Analysis Services-server och lagrings konto finns i samma region, använder kommunikationen mellan dem interna IP-adressintervall, vilket innebär att en brand vägg kan använda valda nätverk och ange ett IP-adressintervall som inte stöds. Om organisations principer kräver en brand vägg måste den konfigureras för att tillåta åtkomst från alla nätverk.


## <a name="data-source-connections"></a>Anslutningar till data Källa

**Fråga** – jag har ett VNet för mitt data käll system. Hur kan jag tillåta att mina Analysis Services-servrar kommer åt databasen från VNET?   
**Svar** – Azure Analysis Services kan inte ansluta till ett VNet. Den bästa lösningen här är att installera och konfigurera en lokal datagateway i VNET och sedan konfigurera dina Analysis Services-servrar med egenskapen **AlwaysUseGateway** -Server. Läs mer i [använda Gateway för data källor på ett Azure-Virtual Network (VNet)](analysis-services-vnet-gateway.md).

**Fråga** – jag har en käll databas bakom en brand vägg. Hur konfigurerar jag brand väggen så att Analysis Services-servern kan komma åt den?   
**Svars** Azure Analysis Services använder inte fasta IP-adresser eller service märken. Intervallet av IP-adresser som Analysis Services servrar använder kan vara vad som helst i intervallet av IP-adresser för *Azure-regionen*. Du måste ange ett *fullständigt utbud* av IP-adresser för Azure-regionen för din server i brand Väggs reglerna för käll databasen. Ett annat och eventuellt mer säkert alternativ är att konfigurera en lokal datagateway. Du kan sedan konfigurera dina Analysis Services-servrar med [egenskapen AlwaysUseGateway-Server](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)och se till att den lokala datagatewayen har en IP-adress som tillåts av data källans brand Väggs regler.

## <a name="azure-apps-with-ip-address"></a>Azure-appar med IP-adress

**Fråga** – jag använder ett Azure-baserat program (till exempel Azure Functions, Azure Data Factory) med en IP-adress som ändras i farten. Hur kan jag hantera Azure Analysis Services brand Väggs regler för att dynamiskt tillåta IP-adressen dit min app körs?   
**Svars** Azure Analysis Services stöder inte privata länkar, virtuella nätverk eller service märken. Det finns vissa lösningar med öppen källkod (till exempel https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) att identifiera IP-adressen för klient programmet och automatiskt och tillfälligt uppdatera brand Väggs reglerna.


## <a name="next-steps"></a>Nästa steg

[Installera och konfigurera en lokal datagateway](analysis-services-gateway-install.md)   
[Ansluta till lokala data källor med lokal datagateway](analysis-services-gateway.md)   
[Använda gateway för datakällor på ett virtuellt Azure-nätverk](analysis-services-vnet-gateway.md)
