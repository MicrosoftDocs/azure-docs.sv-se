---
title: Utvecklings verktyg för Azure konfidentiella data behandling
description: Använd verktyg och bibliotek för att utveckla program för konfidentiell dator användning
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551393"
---
# <a name="application-development-on-intel-sgx"></a>Program utveckling på Intel SGX 


Konfidentiell dator infrastruktur kräver vissa verktyg och program. På den här sidan diskuteras särskilt begrepp som rör program utveckling för virtuella datorer med Azure konfidentiella data som körs på Intel SGX. Innan du läser den här sidan [läser du introduktionen av Intel SGX Virtual Machines och enclaves](confidential-computing-enclaves.md). 

För att dra nytta av kraften i enclaves och isolerade miljöer måste du använda verktyg som stöder konfidentiell dator användning. Det finns olika verktyg som stöder enklaven program utveckling. Du kan till exempel använda de här ramverken med öppen källkod: 

- [Open enklaven Software Development Kit (OE SDK)](#oe-sdk)
- [CCF (konfidentiellt konsortiet Framework)](#ccf)

## <a name="overview"></a>Översikt

Ett program som skapats med enclaves är partitionerat på två sätt:

1. En "ej betrodd" komponent (värden)
1. En "betrodd" komponent (enklaven)


![Apputveckling](media/application-development/oe-sdk.png)


**Värden** är den plats där ditt enklaven-program körs ovanpå och är en icke-betrodd miljö. Enklaven-koden som har distribuerats på värden kan inte nås av värden. 

**Enklaven** är den plats där program koden och dess cachelagrade data/minne körs. Säkra beräkningar bör ske i enclaves för att säkerställa hemligheter och känsliga data, förbli skyddade. 


Under program design är det viktigt att identifiera och fastställa vilken del av programmet som måste köras i enclaves. Den kod som du väljer att placera i den betrodda komponenten är isolerad från resten av ditt program. När enklaven har initierats och koden har lästs in i minnet, kan den koden inte läsas eller ändras från ej betrodda komponenter. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Öppna enklaven Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Använd ett bibliotek eller ramverk som stöds av din Provider om du vill skriva kod som körs i en enklaven. [Open ENKLAVEN SDK](https://github.com/openenclave/openenclave) (OE SDK) är en SDK med öppen källkod som tillåter abstraktion av olika konfidentiella data funktioner. 

OE SDK är konstruerat som ett enda abstraktions lager för all maskin vara på valfri KRYPTOGRAFIPROVIDER. Med OE SDK kan du använda virtuella datorer i Azure konfidentiella datorer för att skapa och köra program ovanpå enclaves.

## <a name="confidential-consortium-framework-ccf"></a>CCF (konfidentiellt konsortium Framework) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF) är ett distribuerat nätverk med noder, var och en som kör sin egen enclaves. Med det betrodda noden nätverk kan du köra en distribuerad redovisning. Redovisningen ger säkra, pålitliga komponenter för protokollet som ska användas. 

![CCF-noder](media/application-development/ccf.png)

Det här ramverket med öppen källkod möjliggör hög genom detaljerad sekretess och konsortiets styrning för blockchain. Med varje nod med TEEs kan du säkerställa en säker enighet och transaktions bearbetning.


## <a name="next-steps"></a>Nästa steg 
- [Distribuera en konfidentiell dator DCsv2-Series virtuell dator](quick-create-portal.md)
- [Hämta och installera OE SDK och börja utveckla program](https://github.com/openenclave/openenclave)