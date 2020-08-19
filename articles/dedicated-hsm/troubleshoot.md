---
title: Felsöka dedikerad HSM-Azure Dedicated HSM | Microsoft Docs
description: Översikten över dedikerad HSM i Azure tillhandahåller funktioner för lagring av nycklar i Azure som uppfyller FIPS 140-2 Level 3-certifiering
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 622ead2ab58075fe6edbe2c013f14391624fd2b7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590463"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Felsöka Azure Dedicated HSM-tjänsten

Azure Dedicated HSM-tjänsten har två distinkta facets. För det första är registreringen och distributionen i Azure av HSM-enheterna med underliggande nätverks komponenter. För det andra är konfigurationen av HSM-enheterna som förberedelse för användning/integrering med en specifik arbets belastning eller ett program. Även om Thales Luna-nätverks HSM-enheter är desamma i Azure eftersom du skulle köpa direkt från Thales, så är det faktum att de är en resurs i Azure som skapar unika överväganden. Dessa överväganden och eventuella fel söknings insikter eller bästa praxis finns dokumenterade här för att säkerställa hög insyn och åtkomst till viktig information. När tjänsten används är den slutgiltiga informationen tillgänglig via support förfrågningar till antingen Microsoft eller Thales direkt. 

> [!NOTE]
> Det bör noteras att innan en konfiguration utförs på en nyligen distribuerad HSM-enhet, bör den uppdateras med eventuella relevanta korrigeringar. En nödvändig korrigering är [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) i Thales support portal som åtgärdar ett problem där systemet slutar svara under omstarten.

## <a name="hsm-registration"></a>HSM-registrering

Dedikerad HSM är inte tillgängligt för användning eftersom den levererar maskin varu resurser i molnet och är därför en värdefull resurs som behöver skydda sig. Vi använder därför en vit listning-process via e-post med hjälp av HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Få åtkomst till dedikerad HSM

Om du tror att dedikerad HSM passar dina nyckel lagrings krav kan du skicka e-post HSMrequest@microsoft.com för att begära åtkomst. Disponera ditt program, de regioner som du vill ha som HSM: er och den volym av HSM: er som du letar efter. Om du arbetar med en Microsoft-representant, till exempel en konto ansvarig eller en moln lösnings arkitekt, kan du till exempel ta med dem i alla förfrågningar.

## <a name="hsm-provisioning"></a>HSM-etablering

Etablering av en HSM-enhet i Azure kan göras via CLI eller PowerShell. När du registrerar dig för tjänsten anges en ARM-mall och hjälp ges för inledande anpassning. 

### <a name="hsm-deployment-failure-information"></a>Information om problem med HSM-distribution

Dedikerad HSM stöder CLI och PowerShell för distribution, så Portal-baserad fel information är begränsad och inte utförlig. Du hittar bättre information med hjälp av Resursläsaren. Portalens start sida har en ikon för den här och mer detaljerad fel information finns tillgänglig. Den här informationen hjälper ett parti om det är inklistrat i när en supportbegäran skapas som rör distributions problem.

![Information om haveri](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegering av HSM-undernät
Antalet en orsak till distributions felen är forgetting för att ange lämplig delegering för det kunddefinierade under nätet där HSM: er ska tillhandahållas. Att ställa in delegering är en del av kraven för VNet och undernät för distribution och mer information finns i självstudierna.

![Under näts delegering](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Konkurrens villkor för HSM-distribution

Standard ARM-mallen som tillhandahålls för distribution har HSM-och ExpressRoute-Gateway-relaterade resurser. Nätverks resurser är ett beroende för lyckad HSM-distribution och tids inställningen kan vara avgörande.  Ibland har vi upptäckt att distributions fel som rör beroende problem och kör om distributionen ofta löser problemet. Om inte, att ta bort resurser och sedan distribuera om är det ofta klart. När du har försökt med detta och fortfarande hittat problemet, kan du generera en supportbegäran i Azure Portal att välja problem typen "problem med att konfigurera Azure-installationen".

### <a name="hsm-deployment-using-terraform"></a>HSM-distribution med terraform

Ett fåtal kunder har använt terraform som en automatiserings miljö i stället för ARM-mallar som anges vid registrering för den här tjänsten. HSM: er kan inte distribueras på det här sättet, men beroende nätverks resurser kan. Terraform har en modul för att anropa en minimal ARM-mall som JUT har HSM-distributionen.  I den här situationen bör det vara viktigt att se till att nätverks resurserna, till exempel den begärda ExpressRoute-gatewayen, är fullständigt distribuerade innan du distribuerar HSM: er. Följande CLI-kommando kan användas för att testa slutförd distribution och integrerad som krävs. Ersätt vinkel paren och placera innehavarna för din egen namn. Du bör söka efter resultatet "provisioningState lyckades"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Distributions problem baserat på kvot
Distributioner kan inte utföras om du överskrider 2 HSM: er per Stamp och 4 HSM: er per region. Undvik den här situationen genom att se till att du har tagit bort resurser från tidigare misslyckade distributioner innan du distribuerar igen. Se "Hur gör jag för att se HSM: er" nedan för att kontrol lera resurser. Om du tror att du behöver överskrida den här kvoten, som främst finns där, så kan du e-posta HSMrequest@microsoft.com med information.

### <a name="deployment-failure-based-on-capacity"></a>Distributions problem baserat på kapacitet
När en viss stämpel eller region blir full, är det nästan alla kostnads fria HSM: er, vilket kan leda till distributions problem. Varje stämpel har 11 HSM: er tillgängligt för kunder, vilket innebär 22 per region. Det finns även 3 reserv delar och 1 testen het i varje stämpel. Om du tror att du har nått gränsen och sedan skickar e-post HSMrequest@microsoft.com för information om fyllnads nivån för vissa stämplar.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hur gör jag för att se HSM: er vid etablering?
Eftersom dedikerad HSM är en vit listas-tjänst betraktas den som en "dold typ" i Azure Portal. Om du vill se HSM-resurserna måste du markera kryss rutan Visa dolda typer som visas nedan. NIC-resursen följer alltid HSM och är en bra plats för att ta reda på IP-adressen för HSM innan du använder SSH för att ansluta.

![Under näts delegering](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Nätverks resurser

Distribution av dedikerad HSM har ett beroende av nätverks resurser och vissa följd begränsningar som ska vara medvetna om.

### <a name="provisioning-expressroute"></a>Etablering av ExpressRoute

Dedikerad HSM använder ExpressRoute-gateway som en "tunnel" för kommunikation mellan kundens privata IP-adressutrymme och den fysiska HSM: en i ett Azure-datacenter.  Eftersom det finns en begränsning för en gateway per VNet, måste kunder som kräver anslutning till sina lokala resurser via ExpressRoute använda ett annat VNet för anslutningen.  

### <a name="hsm-private-ip-address"></a>HSM, privat IP-adress

Exempel mallarna som anges för dedikerad HSM antar att HSM-IP: en hämtas automatiskt från ett angivet under näts intervall. Du kan ange en explicit IP-adress för HSM via ett "NetworkInterfaces"-attribut i ARM-mallen. 

![Under näts delegering](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM-initiering

Initieringen förbereder en ny HSM för användning eller en befintlig HSM för åter användning. Initieringen av HSM måste vara klar innan du kan generera eller lagra objekt, tillåta klienter att ansluta eller utföra kryptografiska åtgärder.

### <a name="lost-credentials"></a>Förlorade autentiseringsuppgifter

Om lösen ordet för Shell-administratören förloras går det att förlora HSM-nyckel. En supportbegäran bör göras för att återställa HSM.
När HSM initieras lagras autentiseringsuppgifter på ett säkert sätt. Autentiseringsuppgifter för Shell och HSM bör behållas i enlighet med företagets principer.

### <a name="failed-logins"></a>Misslyckade inloggningar

Att ange Felaktiga autentiseringsuppgifter för HSM: er kan ha skadliga konsekvenser. Följande är standard beteenden för HSM-roller.

| Roll | Tröskelvärde (antal försök) | Resultatet av för många felaktiga inloggnings försök | Återställning |
|--|--|--|--|
| HSM SÅ | 3 |  HSM är nollställt (alla HSM-objekt identiteter och alla partitioner är borta)  |  HSM måste startas om. Innehållet kan återställas från säkerhets kopior. | 
| Partition så | 10 |  Partitionen är noll. |  Partitionen måste initieras om. Innehållet kan återställas från en säkerhets kopia. |  
| Granska | 10 | Inkräktar | Olåsta automatiskt efter 10 minuter. |  
| Kryptografi utanordnare | 10 (kan minskas) | Om HSM-princip 15: Aktivera återställning av PIN-kod för partition är inställt på 1 (aktive rad) är rollen CO och CU utelåst.<br>Om HSM-princip 15: Aktivera så återställning av PIN-kod för partition är inställt på 0 (inaktive rad) är CO-och CU-rollerna permanent utelåsta och partitionens innehåll är inte längre tillgängligt. Det här är standardinställningen. | CO-rollen måste vara olåst och autentiseringsuppgifterna återställs av partitionen så att använder `role resetpw -name co` .<br>Partitionen måste initieras om och nyckel materialet återställs från en säkerhets kopierings enhet. |  

## <a name="hsm-configuration"></a>HSM-konfiguration 

Följande är en situation där konfigurations fel antingen är gemensamma eller har en effekt som är betrodd för att anropa:

### <a name="hsm-documentation-and-software"></a>HSM-dokumentation och program vara
Program vara och dokumentation för Thales SafeNet Luna 7 HSM-enheter är inte tillgängliga från Microsoft och måste laddas ned från Thales direkt. Registrering krävs med det Thales-kund-ID som togs emot under registrerings processen. Enheterna, som tillhandahålls av Microsoft, har program version 7,2 och version 7.0.3 av inbyggd program vara. Tidigt i 2020 Thalese-dokumentationen offentlig och hittar den [här](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>Konfiguration av HSM-nätverk

Var försiktig när du konfigurerar nätverk i HSM.  HSM har en anslutning via ExpressRoute-gatewayen från ett privat kund-IP-adressutrymme direkt till HSM.  Den här kommunikations kanalen är endast för kund kommunikation och Microsoft har ingen åtkomst. Om HSM har kon figurer ATS på ett sådant sätt att nätverks Sök vägen påverkas, innebär det att all kommunikation med HSM tas bort.  I det här fallet är det enda alternativet att öka en support förfrågan via Azure Portal att enheten ska återställas. Den här återställnings proceduren anger HSM tillbaka till det ursprungliga läget och all konfiguration och viktiga material går förlorade.  Konfigurationen måste återskapas och när enheten ansluter till den HA-gruppen får nyckel materialet repliker ATS.  

### <a name="hsm-device-reboot"></a>Omstart av HSM-enhet

Vissa konfigurations ändringar kräver att HSM är i ström spar läge eller startas om. Microsoft-testning av HSM i Azure fastställde att det vid vissa tillfällen då omstart kan sluta svara. Indirekt är att en supportbegäran måste skapas i Azure Portal begär hård omstart och det kan ta upp till 48 timmar innan det är en manuell process i ett Azure-datacenter.  Undvik den här situationen genom att se till att du har distribuerat start korrigerings filen som är tillgänglig från Thales direkt. Se [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) i Thales Luna Network HSM 7,2 downloads för en rekommenderad korrigerings fil för ett problem där systemet slutar svara under omstarten (Obs! Du måste ha registrerat dig i Thales support Portal för att kunna ladda ned).

### <a name="ntls-certificates-out-of-sync"></a>NTLS certifikat är inte synkroniserade
En klient kan förlora anslutningen till en HSM när ett certifikat upphör att gälla eller har skrivits över genom konfigurations uppdateringar. Klient konfigurationen för certifikat utbyte bör tillämpas igen med varje HSM.
Exempel på NTLS-loggning med ogiltigt certifikat:

> NTLS [8508]: info: 0: inkommande anslutningsbegäran...: 192.168.50.2/59415 NTLS [8508]: fel meddelande från SSLAccept är: fel: 14094418: SSL-rutiner: ssl3_read_bytes: TLSv1 Alert okänd ca NTLS [8508]: fel vid SSL-godkännande (RC_SSL_ERROR) NTLS [8508]: info: 0xc0000711: det gick inte att upprätta en säker kanal med klienten: 192.168.50.2/59415: RC_SSL_FAILED_HANDSHAKE NTLS [8508]: info: 0: NTLS-klienten "okänt värdnamn": 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Misslyckad TCP-kommunikation

Kommunikation från Luna-klient installationen till HSM kräver minst TCP-port 1792. Tänk på detta när alla nätverkskonfigurationer ändras i miljön.

### <a name="failed-ha-group-member-doesnt-recover"></a>Det gick inte att återställa gruppen med HA-medlemmar

Om en misslyckad HA grupp medlem inte kan återställas, måste den återställas manuellt från Luna-klienten med hjälp av kommandot hagroup Recover.
Du måste konfigurera ett antal nya försök för en HA-grupp för att aktivera automatisk återställning. Som standard försöker en HA-grupp inte återställa en HA-medlem till gruppen när den återställs.

### <a name="ha-group-doesnt-sync"></a>HA-gruppen synkroniseras inte

I de fall där medlemsvariabler inte har samma kloning-domän, visas följande för synchronization-kommandot: Varning: synkroniseringen kan Miss lyckas.  Medlemmarna i fack 0 och fack 1 har motstridiga inställningar för kloning av privata nycklar.
En ny partition med rätt kloning-domän ska läggas till i gruppen HA, följt av att den felaktigt konfigurerade partitionen tas bort.

## <a name="hsm-deprovisioning"></a>HSM-avetablering 

Endast när fullständigt slutfört med en HSM kan avetableras och sedan återställas den och återställa den till en kostnads fri pool. 

### <a name="how-to-delete-an-hsm-resource"></a>Så här tar du bort en HSM-resurs

Det går inte att ta bort Azure-resursen för en HSM om inte HSM är i ett "nollställt" läge.  Därför måste allt huvud material ha tagits bort innan du försöker ta bort det som en resurs. Det snabbaste sättet att zeroize är att hämta HSM-administratörens lösen ord fel tre gånger (OBS! detta gäller HSM-administratören och inte på administratörs nivå av utrustning). Luna-gränssnittet har ett `hsm -factoryreset` kommando som zeroizes, men det kan bara köras via konsolen på den seriella porten och kunder har inte åtkomst till detta.

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller insikter om områden över distributions livs cykeln för HSM som kan ha problem eller kräva fel sökning eller noggranna överväganden. Förhoppnings vis hjälper den här artikeln dig att undvika onödiga fördröjningar och onödigt mycket, och om du har relevanta tillägg eller ändringar kan du sedan ta en support förfrågan med Microsoft och berätta för oss. 
