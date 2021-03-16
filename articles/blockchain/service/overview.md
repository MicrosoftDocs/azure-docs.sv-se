---
title: Översikt över Azure blockchain service
description: Översikt över Azure blockchain-tjänsten
ms.date: 03/15/2021
ms.topic: overview
ms.reviewer: patricka
ms.openlocfilehash: 035e2c1df5912f291ad241ade25361f9cc098b22
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573633"
---
# <a name="what-is-azure-blockchain-service"></a>Vad är Azure Blockchain Service?

Azure blockchain service är en fullständigt hanterad redovisnings tjänst som ger användarna möjlighet att växa och hantera blockchain-nätverk i stor skala i Azure. Genom att tillhandahålla enhetlig kontroll för både infrastruktur hantering och blockchain Network-styrning tillhandahåller Azure blockchain service:

* Enkel nätverks distribution och åtgärder
* Inbyggd konsortiums hantering
* Utveckla smarta kontrakt med välbekanta utvecklingsverktyg

Azure blockchain-tjänsten har utformats för att stödja flera redovisnings protokoll. För närvarande ger det stöd för Ethereum- [kvorumet](https://www.goquorum.com/) med den samliggande mekanismen för [IBFT () för fel tolerans](https://docs.goquorum.consensys.net/en/stable/Concepts/Consensus/IBFT/) .

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. Du kan fokusera på utveckling och affärs logik i appar i stället för att allokera tid och resurser för att hantera virtuella datorer och infrastruktur. Dessutom kan du fortsätta att utveckla ditt program med de verktyg med öppen källkod och plattform som du väljer för att leverera dina lösningar utan att behöva lära dig nya kunskaper.

## <a name="network-deployment-and-operations"></a>Nätverksdistribution och åtgärder

Distribution av Azure blockchain-tjänsten görs via Azure Portal, Azure CLI eller via Visual Studio Code med Azure blockchain-tillägget. Distributionen är förenklad, inklusive etablering av både Transaction-och validator-noder, virtuella Azure-nätverk för säkerhets isolering samt hanterad lagring.  Dessutom, när du distribuerar en ny blockchain-medlem, skapar användare även eller ansluter till en konsortium.  Konsortiet gör det möjligt för flera parter i olika Azure-prenumerationer att kunna kommunicera på ett säkert sätt med varandra på en delad blockchain.  Den här förenklade distributionen minskar blockchain nätverks distribution från dagar till minuter.

### <a name="performance-and-service-tiers"></a>Prestanda-och tjänst nivåer

Azure blockchain-tjänsten erbjuder två service nivåer: *Basic* och *standard*. Varje nivå erbjuder olika prestanda och funktioner för att stödja enkla utvecklings-och test arbets belastningar upp till storskaligt skalade produktions blockchain-distributioner. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Båda nivåerna inkluderar minst en Transaction-nod och en validator-nod (Basic) eller två verifierade noder (standard). 

![Prisnivåer](./media/overview/pricing-tiers.png)

Förutom att erbjuda två validator-noder tillhandahåller *standard* nivån två *virtuella kärnor* för varje transaktion och validator-nod medan *Basic* -nivån erbjuder en 1 vCore-konfiguration.  Genom att erbjuda 2 virtuella kärnor för Transaction-och validator-noder kan 1 vCore vara dedikerad till kvorumet medan de återstående 1 vCore kan användas för andra infrastrukturbaserade tjänster, vilket säkerställer optimala prestanda för produktion blockchain-arbetsbelastningar. Mer information om pris information finns i [priser för Azure blockchain-tjänster](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Säkerhet och underhåll

När du har slutfört din första blockchain-medlem har du möjlighet att lägga till ytterligare transaktionsloggfiler till din medlem.  Som standard skyddas transaktions noder genom brand Väggs regler och kräver konfiguration för åtkomst.  Dessutom kan alla transaktionsloggfiler kryptera data i rörelse via TLS.  Det finns flera alternativ för att skydda åtkomst till Transaction Node, inklusive brand Väggs regler, grundläggande autentisering, åtkomst nycklar och Azure Active Directory-integrering. Mer information finns i [Konfigurera Transaction Nodes](configure-transaction-nodes.md) och [Konfigurera Azure Active Directory åtkomst](configure-aad.md).

Som en hanterad tjänst ser Azure blockchain-tjänsten till att din blockchain-medlems noder korrigeras med de senaste uppdateringarna för värd operativ systemet och program stacken i redovisningen, konfigurerade för hög tillgänglighet (endast standard-nivå), vilket eliminerar mycket av de DevOps som krävs för traditionella IaaS blockchain-noder.  Mer information om korrigeringar och uppdateringar finns i [Azure blockchain service Ledger-versioner som stöds](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Övervakning och loggning

Dessutom tillhandahåller Azure blockchain-tjänsten omfattande mått genom Azure Monitor tjänst som ger insikter om noders CPU, minne och lagrings användning.  Azure Monitor ger också till gång till insikter om blockchain nätverks aktivitet, till exempel transaktioner och block används, transaktions köns djup och aktiva anslutningar.  Mått kan anpassas för att ge vyer i de insikter som är viktiga för ditt blockchain-program.  Dessutom kan tröskelvärden definieras via aviseringar som gör det möjligt för användare att utlösa åtgärder som att skicka ett e-postmeddelande eller SMS, köra en Logic app, Azure Function eller skicka till en anpassad, webhook.

![Skärm dum par visar övervakning, med värden för block, transaktioner, väntande transaktioner och hanterade begär Anden.](./media/overview/metrics.png)

Via Azure Log Analytics kan användare visa loggar relaterade till kvorum-redovisningen eller annan viktig information, till exempel försöks anslutningar till Transaction-noderna.

## <a name="built-in-consortium-management"></a>Inbyggd konsortiums hantering

När du distribuerar din första blockchain-medlem kan du antingen ansluta eller skapa en ny konsortium.  Ett konsortium är en logisk grupp som används för att hantera styrningen och anslutningen mellan blockchain-medlemmar som Transact i en process med flera parter.  Azure blockchain-tjänsten tillhandahåller inbyggda styrnings kontroller genom fördefinierade smarta kontrakt som avgör vilka åtgärder som medlemmar i konsortiet kan vidta.  Dessa styrnings kontroller kan anpassas efter behov av konsortiets administratör. När du skapar ett nytt konsortium är din blockchain-medlem standard administratör för konsortiet, vilket gör det möjligt att bjuda in andra parter att delta i konsortiet.  Du kan bara ansluta till ett konsortium om du har bjudits in tidigare.  När du ansluter till ett konsortium är din blockchain-medlem underkastad styrnings kontrollerna som införts av konsortiets administratör.

![Hantering av konsortiet](./media/overview/consortium.png)

Hanterings åtgärder för konsortiet, till exempel att lägga till och ta bort medlemmar från ett konsortium, kan nås via PowerShell och en REST API. Du kan hantera ett konsortium program mässigt med hjälp av vanliga gränssnitt i stället för att ändra och skicka solidy-baserade smarta kontrakt. Mer information finns i [hantering av konsortier](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Utveckla med välbekanta utvecklingsverktyg

Baserat på Ethereum-redovisningen med öppen källkod kan du utveckla program för Azure blockchain-tjänsten på samma sätt som du gör för befintliga Ethereum-program. Med ledande bransch partner arbetar Azure blockchain Development Kit Visual Studio Code-tillägget och gör det möjligt för utvecklare att använda välbekanta verktyg som Truffle Suite för att bygga smarta kontrakt. Med hjälp av kod tillägget för Azure blockchain Visual Studio kan du skapa eller ansluta till ett befintligt konsortium så att du kan skapa och distribuera dina smarta kontrakt från en IDE. Mer information finns i [Azure blockchain Development Kit på vs Code Marketplace](https://aka.ms/vscodebcextension) och [Användar handbok för Azure blockchain Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Publicera blockchain-data

Blockchain Data Manager för Azure blockchain-tjänst fångar, transformerar och levererar Azure blockchain service Transaction-data till Azure Event Grid ämnen som tillhandahåller tillförlitlig och skalbar blockchain redovisnings integrering med Azure-tjänster. Du kan använda blockchain Data Manager för att integrera program i andra kedjan och data lager. Mer information finns i [Blockchain Data Manager for Azure blockchain service](data-manager.md).

## <a name="support-and-feedback"></a>Support och feedback

För Azure blockchain News går du till [Azure blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain service-erbjudanden och information från Azures teknik team för blockchain.

För att ge feedback på produkter eller för att begära nya funktioner, post eller rösta för en idé via [Azure feedback-forumet för blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Engagera med Microsoft-tekniker och Azure blockchain community-experter.

* [Microsoft Q&en fråge sida för Azure blockchain-tjänsten](/answers/topics/azure-blockchain-service.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Nästa steg

Kom igång genom att testa en snabb start eller få mer information från de här resurserna.
* [Skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [skapa en blockchain-medlem med Azure CLI](create-member-cli.md)
* Följ Microsoft Learn Sök väg [Kom igång med blockchain-utveckling](/learn/paths/ethereum-blockchain-development)
* Titta på [nybörjar serien till blockchain](https://channel9.msdn.com/Series/Beginners-Series-to-Blockchain)
* För kostnads jämförelser och räknare, se sidan med [priser](https://azure.microsoft.com/pricing/details/blockchain-service)
* Bygg din första app med [Azure blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Användar handbok](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) för Azure blockchain VSCode-tillägg
