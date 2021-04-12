---
title: Vanliga scenarier och användnings fall för Azures konfidentiella data behandling
description: Förstå hur du använder konfidentiell data behandling i ditt scenario.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: jencook
ms.openlocfilehash: 47938f3a44c3a47f8b444b59d7e2f0867a274f33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566625"
---
# <a name="common-scenarios-for-azure-confidential-computing"></a>Vanliga scenarier för Azures konfidentiella data behandling

Den här artikeln innehåller en översikt över flera vanliga scenarier för Azures konfidentiella data behandling. Rekommendationerna i den här artikeln fungerar som en utgångs punkt när du utvecklar ditt program med hjälp av konfidentiella data behandlings tjänster och ramverk. 

När du har läst den här artikeln kan du svara på följande frågor:

- Vilka är några scenarier för Azures konfidentiella data behandling? –
- Vilka är fördelarna med att använda Azures konfidentiella data behandling för scenarier med flera parter?
- Vilka är fördelarna med att använda Azures konfidentiella data behandling i ett blockchain-nätverk?


## <a name="secure-multi-party-computation"></a>Säker beräkning av flera parter
Med Azures konfidentiella data behandling kan du bearbeta data från flera källor utan att exponera indata till andra parter. Den här typen av säker beräkning möjliggör många scenarier som: tvättning av pengar, bedrägeri identifiering och säker analys av sjukvårds data.

Flera källor kan överföra sina data till en enklaven på en virtuell dator. En part instruerar enklaven att utföra beräkning eller bearbetning av data. Inga parter (inte ens den som utför analysen) kan faktiskt se andra parters data som överfördes till enklaven. 

I säkra multi-parts-databehandlingar går krypterade data till enklaven, enklaven dekrypterar data med hjälp av en nyckel, utför analys, erhåller ett resultat och skickar tillbaka ett krypterat resultat som en part kan dekryptera med den angivna nyckeln. 

**Skydda data som används**: 
- Använd en DCsv2-Series virtuell dator (VM) i Azure med aktiverat stöd för Intel SGX. Dessa virtuella datorer är aktiverade med betrodda körnings miljöer (TEEs) som skyddar och isolerar delar av program data och kod.
- Använd en enklaven-medveten SDK för att skapa en enklaven inuti den virtuella datorn. I enklaven kommer data inte att exponeras för någon, även VM-providern. Data i enklaven kommer att krypteras av maskin varu supporten.
    - Du kan till exempel använda [OE SDK](https://github.com/openenclave/openenclave) för bearbetning på Server sidan. 

**Skydda data under överföring** 
- Använd attestering av TLS som en säker kanal för att garantera säkerheten för data som överförs
- Klienten ser till att data bara skickas till samma server som skyddas av enklaven. 

**Skydda data i vila**
- Använd skyddade och säkra data lager för att säkerställa säkerheten för data vid vila 

### <a name="anti-money-laundering"></a>Tvättning av pengar
I det här säkra exemplet för beräkning av flera parter delar flera banker data med varandra utan att exponera person uppgifter för sina kunder. Banker kör överenskomna analyser på den kombinerade känsliga data uppsättningen. Analysen på den sammanställda data uppsättningen kan upptäcka hur pengarna fördelas av en användare mellan flera banker, utan att bankerna har åtkomst till var and ras data.

Med hjälp av konfidentiell data behandling kan dessa finansiella institutioner öka identifierings frekvensen för bedrägerier, minska antalet falska positiva identifieringar och fortsätta att lära sig från större data mängder. 

### <a name="drug-development-in-healthcare"></a>Narkotika utveckling i sjukvården
Samarbets hälso anläggningar bidrar med privata hälso data uppsättningar för att träna en ML-modell. Varje funktion kan bara se sin egen data uppsättning. Ingen annan lokal eller moln leverantör kan se data-eller utbildnings modellen. 

![Hälso analys av patienter](./media/use-cases-scenarios/patient-data.png)

Alla anläggningar drar nytta av den tränade modellen. Genom att skapa modellen med mer data blev modellen mer korrekt. Varje anläggning som bidragit till att träna modellen kan använda den och få användbara resultat. 

## <a name="blockchain"></a>Blockkedja

Ett blockchain-nätverk är ett decentraliserat nätverk av noder. Dessa noder körs och underhålls av operatörer eller verifierare som vill säkerställa integriteten och uppnå enighet om nätverkets tillstånd. Själva noderna är repliker av redovisningar och används för att spåra blockchain-transaktioner. Varje nod har en fullständig kopia av transaktions historiken, vilket säkerställer integritet och tillgänglighet i ett distribuerat nätverk.

Blockchain Technologies som bygger på konfidentiell data behandling kan använda maskinvarubaserad sekretess för att aktivera datakonfidentialitet och säkra beräkningar. I vissa fall krypteras hela redovisningen för att skydda data åtkomsten. Ibland kan själva transaktionen ske i en Compute-modul i enklaven i noden.

### <a name="confidential-consortium-framework-ccf"></a>CCF (konfidentiellt konsortium Framework)
[CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) är ett exempel på ett distribuerat blockchain-ramverk som bygger på Azures konfidentiella data behandling. Spearheaded av Microsoft Research utnyttjar det här ramverket kraften i betrodda körnings miljöer (TEEs) för att skapa ett nätverk av fjärr-enclaves för attestering. Noder kan köras ovanpå Azure Virtual Machines ([DCsv2-serien](confidential-computing-enclaves.md)) och dra nytta av enklaven-infrastrukturen. Med attesterings protokoll kan användare av blockchain verifiera integriteten för en CCF-nod och effektivt kontrol lera hela nätverket. 

![Ett nätverk av noder](./media/use-cases-scenarios/ccf.png)

I CCF består den decentraliserade redovisningen av registrerade ändringar i ett Key-Value arkiv som replikeras över alla noder i nätverket. Var och en av dessa noder kör en transaktions motor som kan utlösas av användare av blockchain över TLS. När du utlöser en slut punkt tar du i Key-Value Store. Innan den krypterade ändringen registreras i den decentraliserade redovisningen måste den överenskommas av ett visst antal noder för att uppnå konsensus. 

## <a name="next-steps"></a>Nästa steg
[Distribuera](quick-create-marketplace.md) en DCsv2-Series virtuell dator.


