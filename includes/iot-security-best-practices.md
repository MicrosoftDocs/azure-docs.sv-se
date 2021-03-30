---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "72793135"
---
Att skydda en Sakernas Internet (IoT)-infrastruktur kräver en omfattande säkerhets djupgående strategi. Den här strategin kräver att du skyddar data i molnet, skyddar data integriteten vid överföring via det offentliga Internet och etablerar enheter på ett säkert sätt. Varje lager skapar större säkerhets garantier i den övergripande infrastrukturen.

## <a name="secure-an-iot-infrastructure"></a>Skydda en IoT-infrastruktur

Den här säkerhets djupgående strategin kan utvecklas och köras med aktivt deltagande av olika aktörer som arbetar med tillverkning, utveckling och distribution av IoT-enheter och-infrastruktur. Nedan följer en beskrivning av de här spelarna på hög nivå.

* **IoT Hardware Manufacturer/Integrator**: dessa spelare är vanligt vis tillverkare av IoT-maskinvara som distribueras, integrerar maskin vara från olika tillverkare eller leverantörer som tillhandahåller maskin vara för en IoT-distribution som tillverkas eller integreras av andra leverantörer.

* **IoT Solution Developer**: utveckling av en IoT-lösning utförs vanligt vis av en lösnings utvecklare. Den här utvecklaren kan ingå i ett internt team eller en system integrerare (SI) som är specialiserad på den här aktiviteten. IoT-lösningens utvecklare kan utveckla olika komponenter i IoT-lösningen från grunden, integrera olika komponenter som inte är i hylla eller med öppen källkod, eller införa lösnings acceleratorer med mindre anpassning.

* **IoT Solution-distribution**: när en IoT-lösning har utvecklats måste den distribueras i fältet. Den här processen omfattar distribution av maskin vara, samtrafik av enheter och distribution av lösningar i maskin varu enheter eller molnet.

* **IoT-lösnings operator**: när IoT-lösningen har distribuerats kräver den långsiktiga åtgärder, övervakning, uppgraderingar och underhåll. Dessa uppgifter kan utföras av ett internt team som omfattar teknik specialister, maskin varu åtgärder och underhålls team och domän specialister som övervakar rätt beteende för övergripande IoT-infrastruktur.

I avsnitten som följer får du bästa praxis för var och en av de här spelarna som hjälper dig att utveckla, distribuera och driva en säker IoT-infrastruktur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-maskin varu tillverkare/Integrator

Här följer metod tips för IoT-maskinvarusystem och maskin varu integratorer.

* **Omfattnings maskin vara för minimi krav**: maskin varu designen bör innehålla de minsta funktioner som krävs för maskin varans drift och inget annat. Ett exempel är att bara inkludera USB-portar om det behövs för att enheten ska fungera. Dessa ytterligare funktioner öppnar enheten för oönskade angrepps vektorer som bör undvikas.

* **Gör maskin varu manipulering**: skapa i mekanismer för att identifiera fysisk manipulering, t. ex. genom att öppna enheten eller ta bort en del av enheten. Dessa Manipulerings signaler kan vara en del av data strömmen som laddats upp till molnet, vilket kan meddela operatörer om dessa händelser.

* **Bygg en säker maskin vara**: om KSV tillåter, bygger säkerhetsfunktioner som säker och krypterad lagring eller start funktioner baserat på Trusted Platform Module (TPM). Dessa funktioner gör enheterna säkrare och hjälper till att skydda den övergripande IoT-infrastrukturen.

* **Gör uppgraderingar säkra**: uppgraderingar av inbyggd program vara under enhetens livs längd är oundvikliga. Genom att skapa enheter med säkra sökvägar för uppgraderingar och kryptografiska versioner av inbyggd program vara kan enheten vara säker under och efter uppgraderingen.

## <a name="iot-solution-developer"></a>IoT Solution-utvecklare

Följande är metod tips för IoT Solution-utvecklare:

* **Använd säker metod för program utveckling**: utveckling av säker program vara kräver att du funderar på säkerheten, från projektets projekts början till dess implementering, testning och distribution. Alternativen för plattformar, språk och verktyg påverkas av den här metoden. Microsoft Security Development Lifecycle innehåller en stegvis metod för att skapa en säker program vara.

* **Välj program med öppen källkod med försiktighet**: program med öppen källkod ger möjlighet att snabbt utveckla lösningar. När du väljer program vara med öppen källkod bör du ta hänsyn till gruppens aktivitets nivå för varje komponent med öppen källkod. En aktiv community garanterar att program varan stöds och att problem upptäcks och åtgärdas. Det kan också hända att ett skymt och inaktivt program med öppen källkod inte stöds och att problem inte kan identifieras.

* **Integrera med försiktighet**: många program varu säkerhets fel finns på gränserna för bibliotek och API: er. Funktioner som kanske inte krävs för den aktuella distributionen kan fortfarande vara tillgängliga via ett API-lager. Säkerställ övergripande säkerhet genom att kontrol lera att alla gränssnitts komponenter integreras med säkerhets brister.

## <a name="iot-solution-deployer"></a>IoT-lösning, distribution

Följande är metod tips för IoT Solution-distributioner:

* **Distribuera maskin vara på ett säkert sätt**: IoT-distributioner kan kräva att maskin vara distribueras på osäkra platser, till exempel i offentliga utrymmen eller i ej övervakade språk. I sådana fall kontrollerar du att maskin varu distributionen är Manipulerings medveten om den maximala omfattningen. Om USB-enheter eller andra portar är tillgängliga på maskin varan, se till att de skyddas på ett säkert sätt. Många angrepps vektorer kan använda dessa som start punkter.

* **Se** till att nycklarna är säkra: under distributionen kräver varje enhet enhets-ID: n och associerade autentiseringsinställningar som genereras av moln tjänsten. Behåll dessa nycklar fysiskt säkra även efter distributionen. Eventuella komprometterade nycklar kan användas av en skadlig enhet för att maskeras som en befintlig enhet.

## <a name="iot-solution-operator"></a>IoT Solution-operator

Följande är metod tips för IoT Solution-operatörer:

* **Håll systemet uppdaterat**: kontrol lera att enhetens operativ system och alla enhets driv rutiner uppgraderas till de senaste versionerna. Om du aktiverar automatiska uppdateringar i Windows 10 (IoT eller andra SKU: er), är det dags att använda Microsoft och tillhandahålla ett säkert operativ system för IoT-enheter. Att behålla andra operativ system (till exempel Linux) uppdaterat hjälper till att säkerställa att de också skyddas mot skadliga attacker.

* **Skydda mot skadlig aktivitet**: om operativ systemet tillåter installerar du de senaste antivirus-och program mot skadlig kod-funktionerna på varje enhets operativ system. Den här metoden kan hjälpa till att minska de flesta externa hot. Du kan skydda de flesta moderna operativ system mot hot genom att vidta lämpliga åtgärder.

* **Granska ofta**: granskning av IoT-infrastruktur för säkerhetsrelaterade problem är nyckel vid svar på säkerhets incidenter. De flesta operativ system har inbyggd händelse loggning som bör granskas ofta för att se till att ingen säkerhets överträdelse har inträffat. Gransknings information kan skickas som en separat telemetri-dataström till moln tjänsten där den kan analyseras.

* **Skydda IoT-infrastrukturen fysiskt**: de värsta säkerhets angrepp mot IoT-infrastrukturen startas med fysisk åtkomst till enheter. En viktig säkerhets rutin är att skydda mot skadlig användning av USB-portar och annan fysisk åtkomst. En nyckel för att hämta överträdelser som kan ha inträffat är loggning av fysisk åtkomst, t. ex. USB-port användning. Windows 10 (IoT och andra SKU: er) aktiverar utförlig loggning av de här händelserna.

* **Skydda autentiseringsuppgifter för moln**: autentiseringsuppgifter för moln autentisering som används för att konfigurera och använda en IoT-distribution är förmodligen det enklaste sättet att få åtkomst till och kompromettera ett IoT-system. Skydda autentiseringsuppgifterna genom att ändra lösen ordet ofta och avstå från att använda dessa autentiseringsuppgifter på offentliga datorer.

Funktioner i olika IoT-enheter varierar. Vissa enheter kan vara datorer som kör vanliga Skriv bords operativ system och vissa enheter kan köra mycket lätt vikt operativ system. De rekommenderade säkerhets metoderna som beskrivs ovan kan vara tillämpliga på dessa enheter i varierande grad. Om det här alternativet anges bör ytterligare säkerhets-och distributions metod för de här enheternas tillverkare följas.

Vissa äldre och begränsade enheter kanske inte har utformats specifikt för IoT-distribution. Dessa enheter kan sakna möjlighet att kryptera data, ansluta till Internet eller tillhandahålla avancerad granskning. I dessa fall kan en modern och säker fält-Gateway samla in data från äldre enheter och tillhandahålla den säkerhet som krävs för att ansluta dessa enheter via Internet. Fält-gatewayer kan tillhandahålla säker autentisering, förhandling av krypterade sessioner, ta emot kommandon från molnet och många andra säkerhetsfunktioner.