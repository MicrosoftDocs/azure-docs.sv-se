---
title: Utvecklingsverktyg för konfidentiell databehandling i Azure
description: Använda verktyg och bibliotek för att utveckla program för konfidentiell databehandling
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 571c1a4ce545976db09f46a07d963d5344c02c29
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791021"
---
# <a name="application-development-on-intel-sgx"></a>Programutveckling på Intel SGX 


Infrastruktur för konfidentiell databehandling kräver specifika verktyg och programvara. På den här sidan beskrivs begrepp som rör programutveckling för virtuella Datorer med konfidentiell databehandling i Azure som körs på Intel SGX. Innan du läser den här sidan [bör du läsa introduktionen av virtuella Intel SGX-datorer och enklaver](confidential-computing-enclaves.md). 

Om du vill utnyttja kraften hos enklaver och isolerade miljöer måste du använda verktyg som stöder konfidentiell databehandling. Det finns olika verktyg som stöder enklavprogramutveckling. Du kan till exempel använda dessa ramverk med öppen källkod: 

- [The Open Enclave Software Development Kit (OE SDK)](#oe-sdk)
- [The EGo Software Development Kit](#ego)
- [Ccf (Confidential Consortium Framework)](#ccf)

## <a name="overview"></a>Översikt

Ett program som skapats med enklaver partitioneras på två sätt:

1. En "ej betrodd" komponent (värden)
1. En "betrodd" komponent (enklaven)


![Apputveckling](media/application-development/oe-sdk.png)


**Värden är** den plats där enklavprogrammet körs ovanpå och är en ej betrodd miljö. Enklavkoden som distribueras på värden kan inte nås av värden. 

**Enklaven är** den plats där programkoden och dess cachelagrade data/minne körs. Säkra beräkningar bör ske i enklaverna för att se till att hemligheter och känsliga data förblir skyddade. 


Under programdesignen är det viktigt att identifiera och avgöra vilken del av programmet som ska köras i enklaverna. Den kod som du väljer att placera i den betrodda komponenten är isolerad från resten av ditt program. När enklaven har initierats och koden har lästs in i minnet kan koden inte läsas in eller ändras från de ej betrodda komponenterna. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Använd ett bibliotek eller ramverk som stöds av din leverantör om du vill skriva kod som körs i en enklav. [OPEN Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) är en SDK med öppen källkod som möjliggör abstraktion över annan konfidentiell datorbearbetningsaktiverad maskinvara. 

OE SDK är byggt för att vara ett enda abstraktionslager över all maskinvara på valfri CSP. OE SDK kan användas ovanpå azures virtuella datorer för konfidentiell databehandling för att skapa och köra program ovanpå enklaver.

## <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) är en SDK med öppen källkod som gör att du kan köra program som skrivits i programmeringsspråket Go i enklaver. EGo bygger på OE SDK och levereras med ett in-enclave Go-bibliotek för atterering och försegling. Många befintliga Go-program körs på EGo utan ändringar.  

## <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

[CCF är](https://github.com/Microsoft/CCF) ett distribuerat nätverk med noder som var och en kör sina egna enklaver. Med det betrodda nodnätverket kan du köra ett distribuerat redovisningsregister. Huvudboken tillhandahåller säkra, tillförlitliga komponenter som protokollet kan använda. 

![CCF-noder](media/application-development/ccf.png)

Det här ramverket med öppen källkod ger hög insyn i den övergripande konfidentialiteten och konsortiumstyrningen för blockkedjan. När varje nod använder TE:er kan du säkerställa säker konsensus- och transaktionsbearbetning.


## <a name="next-steps"></a>Nästa steg 
- [Distribuera en konfidentiell databehandling DCsv2-Series virtuell dator](quick-create-portal.md)
- [Ladda ned och installera OE SDK och börja utveckla program](https://github.com/openenclave/openenclave)
