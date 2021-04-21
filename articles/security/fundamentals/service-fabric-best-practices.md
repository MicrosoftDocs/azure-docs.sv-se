---
title: Metodtips för Azure Service Fabric säkerhet
description: Den här artikeln innehåller en uppsättning metodtips för Azure Service Fabric säkerhet.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7d87e2496158fec8ff33ab8586c845a6207f810
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816070"
---
# <a name="azure-service-fabric-security-best-practices"></a>Metodtips för Azure Service Fabric-säkerhet
Det går snabbt, enkelt och kostnadseffektivt att distribuera ett program i Azure. Innan du distribuerar molnprogrammet till produktion bör du granska vår lista över viktiga och rekommenderade metodtips för att implementera säkra kluster i ditt program.

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

För varje bästa praxis förklarar vi:

-   Vad bästa praxis är.
-   Varför du bör implementera bästa praxis.
-   Vad som kan hända om du inte implementerar bästa praxis.
-   Hur du kan lära dig att implementera bästa praxis.

Vi rekommenderar följande metodtips för Service Fabric Azure-säkerhet:

-   Använd Azure Resource Manager-mallar och Service Fabric PowerShell-modulen för att skapa säkra kluster.
-   Använd X.509-certifikat.
-   Konfigurera säkerhetsprinciper.
-   Implementera Reliable Actors säkerhetskonfiguration.
-   Konfigurera TLS för Azure Service Fabric.
-   Använd nätverksisolering och säkerhet med Azure Service Fabric.
-   Konfigurera Azure Key Vault för säkerhet.
-   Tilldela användare till roller.


## <a name="best-practices-for-securing-your-clusters"></a>Metodtips för att skydda dina kluster

Använd alltid ett säkert kluster:
-   Implementera klustersäkerhet med hjälp av certifikat.
-   Ge klientåtkomst (administratör och skrivskyddad) med hjälp av Azure Active Directory (Azure AD).

Använd automatiserade distributioner:
-   Använd skript för att generera, distribuera och återställa hemligheterna.
-   Lagra hemligheterna i Azure Key Vault använda Azure AD för all annan klientåtkomst.
-   Kräv autentisering för mänsklig åtkomst till hemligheterna.

Överväg även följande konfigurationsalternativ:
-   Skapa perimeternätverk (även kallade demilitariserade zoner, DMZ:er och avskärmade undernät) med hjälp av Azure-nätverkssäkerhetsgrupper (NSG:er).
-   Få åtkomst till virtuella datorer (VM) för kluster eller hantera klustret med hjälp av hoppservrar med Anslutning till fjärrskrivbord.

Dina kluster måste skyddas för att förhindra att obehöriga användare ansluter, särskilt när ett kluster körs i produktion. Även om det är möjligt att skapa ett oskyddat kluster kan anonyma användare ansluta till klustret om klustret exponerar hanteringsslutpunkter för det offentliga Internet.

Det finns tre [scenarier för](../../service-fabric/service-fabric-cluster-security.md) att implementera klustersäkerhet med hjälp av olika tekniker:

-   Nod-till-nod-säkerhet: Det här scenariot skyddar kommunikationen mellan de virtuella datorerna och datorerna i klustret. Den här säkerhetsformen säkerställer att endast de datorer som har behörighet att ansluta till klustret kan vara värdar för program och tjänster i klustret.
I det här scenariot kan kluster som körs i Azure, eller fristående kluster som körs i Windows, använda antingen certifikatsäkerhet eller [Windows-säkerhet](../../service-fabric/service-fabric-windows-cluster-windows-security.md) för Windows Server-datorer. [](../../service-fabric/service-fabric-windows-cluster-x509-security.md)
-   Säkerhet från klient till nod: Det här scenariot skyddar kommunikationen mellan Service Fabric klient och de enskilda noderna i klustret.
-   Service Fabric rollbaserad åtkomstkontroll (Service Fabric RBAC): Det här scenariot använder separata identiteter (certifikat, Azure AD och så vidare) för varje administratörs- och användarklientroll som har åtkomst till klustret. Du anger rollidentiteterna när du skapar klustret.

>[!NOTE]
>**Säkerhetsrekommendation för Azure-kluster:** Använd Azure AD-säkerhet för att autentisera klienter och certifikat för nod-till-nod-säkerhet.

Information om hur du konfigurerar ett fristående Windows-kluster [finns i Konfigurera inställningar för ett fristående Windows-kluster.](../../service-fabric/service-fabric-cluster-manifest.md)

Använd Azure Resource Manager-mallar och Service Fabric PowerShell-modulen för att skapa ett säkert kluster.
Stegvisa instruktioner för att skapa ett säkert Service Fabric med hjälp av Azure Resource Manager-mallar finns i [Skapa ett Service Fabric kluster.](../../service-fabric/service-fabric-cluster-creation-via-arm.md)

Använd Azure Resource Manager mall:
-   Anpassa klustret med hjälp av mallen för att konfigurera hanterad lagring för virtuella hårddiskar (VHD).
-   Gör ändringar i resursgruppen med hjälp av mallen för enkel konfigurationshantering och granskning.

Hantera klusterkonfigurationen som kod:
-   Var noggrann när du kontrollerar dina distributionskonfigurationer.
-   Undvik att använda implicita kommandon för att direkt ändra dina resurser.

Många aspekter av [Service Fabric kan](../../service-fabric/service-fabric-application-lifecycle.md) automatiseras. [PowerShell Service Fabric modulen](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiserar vanliga uppgifter för att distribuera, uppgradera, ta bort och testa Azure Service Fabric program. Hanterade API:er och HTTP-API:er för programhantering är också tillgängliga.

## <a name="use-x509-certificates"></a>Använda X.509-certifikat
Skydda alltid dina kluster med hjälp av X.509-certifikat eller Windows-säkerhet. Säkerheten konfigureras bara när klustret skapas. Det går inte att aktivera säkerhet när klustret har skapats.

Om du vill [ange ett klustercertifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md)anger du värdet för **egenskapen ClusterCredentialType** till X509. Om du vill ange ett servercertifikat för externa anslutningar anger du **egenskapen ServerCredentialType** till X509.

Följ även dessa metoder:
-   Skapa certifikaten för produktionskluster med hjälp av en korrekt konfigurerad Windows Server-certifikattjänst. Du kan också hämta certifikaten från en godkänd certifikatutfärdare (CA).
-   Använd aldrig ett tillfälligt certifikat eller testcertifikat för produktionskluster om certifikatet har skapats med hjälp MakeCert.exe eller ett liknande verktyg.
-   Använd ett själv signerat certifikat för testkluster, men inte för produktionskluster.

Om klustret är oskyddat kan vem som helst ansluta till klustret anonymt och utföra hanteringsåtgärder. Därför bör du alltid skydda produktionskluster med hjälp av X.509-certifikat eller Windows-säkerhet.

Mer information om hur du använder X.509-certifikat finns i [Lägga till eller ta bort certifikat för Service Fabric kluster](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurera säkerhetsprinciper
Service Fabric också de resurser som används av program. Resurser som filer, kataloger och certifikat lagras under användarkontona när programmet distribueras. Den här funktionen gör program som körs säkrare från varandra, även i en delad värdmiljö.

-   Använd en Active Directory-domängrupp eller -användare: Kör tjänsten under autentiseringsuppgifterna för en Active Directory-användare eller ett gruppkonto. Se till att använda Active Directory lokalt i din domän och inte Azure Active Directory. Få åtkomst till andra resurser i domänen som har beviljats behörighet med hjälp av en domänanvändare eller grupp. Till exempel resurser som filresurser.

-   Tilldela en säkerhetsåtkomstprincip för HTTP- och HTTPS-slutpunkter: Ange egenskapen **SecurityAccessPolicy** för att tillämpa en **RunAs-princip** på en tjänst när tjänstmanifestet deklarerar slutpunktsresurser med HTTP. Portar som allokeras till HTTP-slutpunkterna är korrekt åtkomstkontrollerade listor för RunAs-användarkontot som tjänsten körs under. När principen inte har angetts har http.sys åtkomst till tjänsten och du kan få fel med anrop från klienten.

Information om hur du använder säkerhetsprinciper i ett Service Fabric kluster finns [i Konfigurera säkerhetsprinciper för ditt program](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementera Reliable Actors säkerhetskonfiguration
Service Fabric Reliable Actors är en implementering av aktörsdesignmönstret. Precis som med alla programdesignmönster baseras beslutet att använda ett visst mönster på om ett programvaruproblem passar mönstret.

Använd i allmänhet aktörsdesignmönstret för att modellera lösningar för följande programvaruproblem eller säkerhetsscenarier:
-   Ditt problemområde omfattar ett stort antal (tusentals eller fler) små, oberoende och isolerade tillstånds- och logikenheter.
-   Du arbetar med entrådiga objekt som inte kräver betydande interaktion från externa komponenter, inklusive frågetillstånd över en uppsättning aktörer.
-   Aktörsinstanserna blockerar inte anropare med oförutsägbara fördröjningar genom att utfärda I/O-åtgärder.

I Service Fabric implementeras aktörer i det Reliable Actors programramverket. Det här ramverket baseras på aktörsmönstret och bygger på [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Varje tillförlitlig aktörstjänst som du skriver är en partitionerad tillståndsful och tillförlitlig tjänst.

Varje aktör definieras som en instans av en aktörstyp, identisk med hur ett .NET-objekt är en instans av en .NET-typ. En aktörstyp **som** implementerar funktionerna i en kalkylator kan till exempel ha många aktörer av den typen som distribueras på olika noder i ett kluster. Var och en av de distribuerade aktörer kännetecknas unikt av en aktörsidentifierare.

[Replikeringssäkerhetskonfigurationer](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) används för att skydda den kommunikationskanal som används under replikeringen. Den här konfigurationen förhindrar tjänster från att se varandras replikeringstrafik och säkerställer att data med hög tillgång är säkra. Som standard förhindrar ett tomt säkerhetskonfigurationsavsnitt replikeringssäkerhet.
Replikeringskonfigurationer konfigurerar den replikerare som ansvarar för att göra aktörstillståndsproviderns tillstånd mycket tillförlitligt.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurera TLS för Azure Service Fabric
Serverautentiseringsprocessen autentiserar klusterhanteringsslutpunkterna till en [hanteringsklient.](../../service-fabric/service-fabric-cluster-creation-via-arm.md) Hanteringsklienten känner sedan igen att den pratar med det verkliga klustret. Det här certifikatet tillhandahåller också en [TLS för](../../service-fabric/service-fabric-cluster-creation-via-arm.md) HTTPS-hanterings-API:et och för Service Fabric Explorer över HTTPS.
Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Om du vill konfigurera TLS för ett program måste du först skaffa ett SSL/TLS-certifikat som har signerats av en certifikatutfärdare. Certifikatutfärdaren är en betrodd tredje part som utfärdar certifikat för TLS-säkerhet. Om du inte redan har ett SSL/TLS-certifikat måste du skaffa ett från ett företag som säljer SSL/TLS-certifikat.

Certifikatet måste uppfylla följande krav för SSL/TLS-certifikat i Azure:
-   Certifikatet måste innehålla en privat nyckel.

-   Certifikatet måste skapas för nyckelutbyte och kan exporteras till en personlig informationsutbytesfil (.pfx).

-   Certifikatets ämnesnamn måste matcha domännamnet som används för att få åtkomst till molntjänsten.

    - Skaffa ett anpassat domännamn som ska användas för åtkomst till molntjänsten.
    - Begär ett certifikat från en certifikatutfärdare med ett ämnesnamn som matchar tjänstens anpassade domännamn. Om ditt anpassade domännamn till exempel är __contoso__**.com** bör certifikatet från certifikatutfärdaren ha ämnesnamnet **.contoso.com** __eller www__**.contoso.com**.

    >[!NOTE]
    >Du kan inte hämta ett SSL/TLS-certifikat från en certifikatutfärdare för __cloudapp__**.net-domänen.**

-   Certifikatet måste använda minst 2 048-bitarskryptering.

HTTP-protokollet är oskyddat och kan avlyssningsangrepp. Data som överförs via HTTP skickas som oformaterad text från webbläsaren till webbservern eller mellan andra slutpunkter. Angripare kan fånga upp och visa känsliga data som skickas via HTTP, till exempel kreditkortsinformation och kontoinloggningar. När data skickas eller publiceras via en webbläsare via HTTPS ser SSL till att känslig information krypteras och säkras från avlyssning.

Mer information om hur du använder SSL/TLS-certifikat finns [i Konfigurera TLS för ett program i Azure.](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Använda nätverksisolering och säkerhet med Azure Service Fabric
Konfigurera ett säkert kluster med 3 nodtyper med hjälp [Azure Resource Manager mallen](../../azure-resource-manager/templates/template-syntax.md) som ett exempel. Kontrollera inkommande och utgående nätverkstrafik med hjälp av mallen och nätverkssäkerhetsgrupper.

Mallen har en NSG för var och en av VM-skalningsuppsättningarna och används för att styra trafiken in och ut ur uppsättningen. Reglerna konfigureras som standard för att tillåta all trafik som behövs för systemtjänsterna och programportarna som anges i mallen. Granska dessa regler och gör eventuella ändringar som passar dina behov, inklusive att lägga till nya regler för dina program.

Mer information finns i Vanliga [nätverksscenarier för Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurera Azure Key Vault för säkerhet
Service Fabric använder certifikat för att tillhandahålla autentisering och kryptering för att skydda ett kluster och dess program.

Service Fabric använder X.509-certifikat för att skydda ett kluster och för att tillhandahålla programsäkerhetsfunktioner. Du kan Azure Key Vault hantera [certifikat för](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) Service Fabric kluster i Azure. Azure-resursprovidern som skapar klustren hämtar certifikaten från ett nyckelvalv. Providern installerar sedan certifikaten på de virtuella datorerna när klustret distribueras i Azure.

Det finns en certifikatrelation [mellan Azure Key Vault,](../../key-vault/general/security-features.md)Service Fabric klustret och den resursprovider som använder certifikaten. När klustret skapas lagras information om certifikatrelationen i ett nyckelvalv.

Det finns två grundläggande steg för att konfigurera ett nyckelvalv:
1. Skapa en resursgrupp specifikt för ditt nyckelvalv.

    Vi rekommenderar att du lägger nyckelvalvet i en egen resursgrupp. Den här åtgärden hjälper till att förhindra förlust av dina nycklar och hemligheter om andra resursgrupper tas bort, till exempel lagring, beräkning eller gruppen som innehåller klustret. Resursgruppen som innehåller ditt nyckelvalv måste finnas i samma region som klustret som använder det.

2. Skapa ett nyckelvalv i den nya resursgruppen.

    Nyckelvalvet måste vara aktiverat för distribution. Beräkningsresursprovidern kan sedan hämta certifikaten från valvet och installera dem på VM-instanserna.

Mer information om hur du ställer in ett nyckelvalv finns [i Vad är Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat programmen som representerar klustret tilldelar du användarna de roller som stöds av Service Fabric: skrivskyddad och administratör. Du kan tilldela dessa roller med hjälp av Azure Portal.

>[!NOTE]
> Mer information om hur du använder Service Fabric i Service Fabric finns [i Service Fabric rollbaserad åtkomstkontroll för Service Fabric klienter](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric två typer av åtkomstkontroll för klienter som är anslutna [till ett Service Fabric kluster:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)administratör och användare. Klusteradministratören kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare. Åtkomstkontroll gör klustret säkrare.

## <a name="next-steps"></a>Nästa steg

- [Service Fabric checklista för säkerhet](../../service-fabric/service-fabric-best-practices-security.md)
- Konfigurera din Service Fabric [utvecklingsmiljö](../../service-fabric/service-fabric-get-started.md).
- Läs mer [Service Fabric om supportalternativen](../../service-fabric/service-fabric-support.md).