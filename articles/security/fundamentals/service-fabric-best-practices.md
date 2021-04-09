---
title: Metod tips för Azure Service Fabric Security
description: Den här artikeln innehåller en uppsättning metod tips för Azure Service Fabric Security.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7396c9a29c7d9f69dbe6a9cc5cd085c72ebafde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94700954"
---
# <a name="azure-service-fabric-security-best-practices"></a>Metodtips för Azure Service Fabric-säkerhet
Att distribuera ett program på Azure är snabbt, enkelt och kostnads effektivt. Innan du distribuerar moln programmet till produktion bör du gå igenom vår lista över viktiga och rekommenderade metod tips för att implementera säkra kluster i ditt program.

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

För varje bästa praxis förklarar vi:

-   Det bästa tillvägagångs sättet är.
-   Varför du bör implementera bästa praxis.
-   Vad kan inträffa om du inte implementerar bästa praxis.
-   Hur du kan lära dig att implementera bästa praxis.

Vi rekommenderar följande rekommenderade metoder för Azure Service Fabric-säkerhet:

-   Använd Azure Resource Manager mallar och Service Fabric PowerShell-modulen för att skapa säkra kluster.
-   Använd X. 509-certifikat.
-   Konfigurera säkerhets principer.
-   Implementera Reliable Actors säkerhets konfiguration.
-   Konfigurera TLS för Azure Service Fabric.
-   Använd Nätverks isolering och säkerhet med Azure Service Fabric.
-   Konfigurera Azure Key Vault för säkerhet.
-   Tilldela användare till roller.


## <a name="best-practices-for-securing-your-clusters"></a>Metod tips för att skydda dina kluster

Använd alltid ett säkert kluster:
-   Implementera kluster säkerhet med hjälp av certifikat.
-   Tillhandahåll klient åtkomst (admin och skrivskyddad) med hjälp av Azure Active Directory (Azure AD).

Använd automatiserade distributioner:
-   Använd skript för att skapa, distribuera och rulla över hemligheterna.
-   Lagra hemligheterna i Azure Key Vault och Använd Azure AD för all annan klient åtkomst.
-   Kräv autentisering för mänsklig åtkomst till hemligheterna.

Överväg även följande konfigurations alternativ:
-   Skapa perimeternätverk (kallas även demilitariserad-zoner, DMZs och skärmade undernät) med hjälp av Azure nätverks säkerhets grupper (NSG: er).
-   Få åtkomst till virtuella datorer i klustret (VM) eller hantera klustret med hjälp av hopp servrar med Anslutning till fjärrskrivbord.

Dina kluster måste skyddas för att förhindra att obehöriga användare ansluter, särskilt när ett kluster körs i produktionen. Även om det är möjligt att skapa ett oskyddat kluster kan anonyma användare ansluta till klustret om klustret exponerar hanterings slut punkter för det offentliga Internet.

Det finns tre [scenarier](../../service-fabric/service-fabric-cluster-security.md) för att implementera kluster säkerhet med hjälp av olika tekniker:

-   Säkerhet från nod till nod: det här scenariot skyddar kommunikationen mellan de virtuella datorerna och datorerna i klustret. Den här typen av säkerhet garanterar att endast de datorer som har behörighet att ansluta till klustret kan vara värdar för program och tjänster i klustret.
I det här scenariot kan kluster som körs på Azure eller fristående kluster som körs i Windows använda antingen [certifikat säkerhet](../../service-fabric/service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet](../../service-fabric/service-fabric-windows-cluster-windows-security.md) för Windows Server-datorer.
-   Säkerhet från klient till nod: det här scenariot skyddar kommunikationen mellan en Service Fabric-klient och de enskilda noderna i klustret.
-   Service Fabric rollbaserad åtkomst kontroll (Service Fabric RBAC): det här scenariot använder separata identiteter (certifikat, Azure AD och så vidare) för varje administratör och användar klient roll som har åtkomst till klustret. Du anger roll identiteter när du skapar klustret.

>[!NOTE]
>**Säkerhets rekommendationer för Azure-kluster:** Använd Azure AD-säkerhet för att autentisera klienter och certifikat för nod-till-nod-säkerhet.

Information om hur du konfigurerar ett fristående Windows-kluster finns i [Konfigurera inställningar för ett fristående Windows-kluster](../../service-fabric/service-fabric-cluster-manifest.md).

Använd Azure Resource Manager mallar och Service Fabric PowerShell-modulen för att skapa ett säkert kluster.
Stegvisa instruktioner för att skapa ett säkert Service Fabric kluster med hjälp av Azure Resource Manager-mallar finns i [skapa ett Service Fabric kluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Använd Azure Resource Manager-mallen:
-   Anpassa klustret med hjälp av mallen för att konfigurera hanterad lagring för virtuella datorer virtuella hård diskar (VHD: er).
-   Enhets ändringar i resurs gruppen med hjälp av mallen för enkel konfigurations hantering och granskning.

Behandla kluster konfigurationen som kod:
-   Var noggrann när du kontrollerar distributions konfigurationerna.
-   Undvik att använda implicita kommandon för att ändra dina resurser direkt.

Många aspekter av [Service Fabric programmets livs cykel](../../service-fabric/service-fabric-application-lifecycle.md) kan automatiseras. [Service Fabric PowerShell-modulen](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiserar vanliga uppgifter för att distribuera, uppgradera, ta bort och testa Azure Service Fabric-program. Hanterade API: er och HTTP-API: er för program hantering är också tillgängliga.

## <a name="use-x509-certificates"></a>Använd X. 509-certifikat
Skydda alltid dina kluster genom att använda X. 509-certifikat eller Windows-säkerhet. Säkerhet konfigureras bara när klustret skapas. Det går inte att aktivera säkerhet när klustret har skapats.

Ange ett [kluster certifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md)genom att ange värdet för egenskapen **ClusterCredentialType** till X509. Ange ett Server certifikat för externa anslutningar genom att ange egenskapen **ServerCredentialType** till X509.

Följ också dessa metoder:
-   Skapa certifikat för produktions kluster genom att använda en korrekt konfigurerad Windows Server Certificate service. Du kan också hämta certifikaten från en godkänd certifikat utfärdare (CA).
-   Använd aldrig ett temporärt certifikat eller test certifikat för produktions kluster om certifikatet har skapats med hjälp av MakeCert.exe eller ett liknande verktyg.
-   Använd ett självsignerat certifikat för test kluster, men inte för produktions kluster.

Om klustret är oskyddat kan vem som helst ansluta till klustret anonymt och utföra hanterings åtgärder. Därför bör du alltid säkra produktions kluster genom att använda X. 509-certifikat eller Windows-säkerhet.

Mer information om hur du använder X. 509-certifikat finns i [lägga till eller ta bort certifikat för ett Service Fabric-kluster](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurera säkerhets principer
Service Fabric skyddar också resurserna som används av program. Resurser som filer, kataloger och certifikat lagras under användar kontona när programmet distribueras. Den här funktionen gör att program som körs säkrare från varandra, även i en delad värd miljö.

-   Använd en Active Directory domän grupp eller användare: kör tjänsten under autentiseringsuppgifterna för ett Active Directory användar-eller grupp konto. Se till att du använder Active Directory lokalt i din domän och inte Azure Active Directory. Få åtkomst till andra resurser i domänen som har beviljats behörigheter genom att använda en domän användare eller grupp. Till exempel resurser som fil resurser.

-   Tilldela en säkerhets åtkomst princip för HTTP-och HTTPS-slutpunkter: ange egenskapen **SecurityAccessPolicy** för att tillämpa en **runas** -princip på en tjänst när tjänst manifestet deklarerar slut punkts resurser med http. Portar som tilldelas HTTP-slutpunkterna är korrekt åtkomst kontrollerade listor för det RunAs-användarkonto som tjänsten körs under. När principen inte har angetts har http.sys inte åtkomst till tjänsten och du kan få problem med anrop från klienten.

Information om hur du använder säkerhets principer i ett Service Fabric kluster finns i [Konfigurera säkerhets principer för ditt program](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementera Reliable Actors säkerhets konfiguration
Service Fabric Reliable Actors är en implementering av aktörens design mönster. Precis som med ett mönster för program varu design baseras beslutet om att använda ett speciellt mönster på om ett program varu problem passar mönstret.

I allmänhet använder du aktörens design mönster för att hjälpa till att modellera lösningar för följande program varu problem eller säkerhets scenarier:
-   Ditt problem utrymme innebär ett stort antal (tusentals eller mer) av små, oberoende och isolerade enheter av tillstånd och logik.
-   Du arbetar med entrådade objekt som inte kräver betydande interaktion från externa komponenter, inklusive frågor om tillstånd i en uppsättning aktörer.
-   Dina aktörs instanser blockerar inte anropare med oförutsägbara fördröjningar genom att skicka I/O-åtgärder.

I Service Fabric implementeras aktörer i Reliable Actors Application Framework. Det här ramverket baseras på aktörens mönster och byggt ovanpå [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md). Varje tillförlitlig aktörs tjänst som du skriver är en partitionerad tillstånds känslig tillförlitlig tjänst.

Varje aktör definieras som en instans av en aktörs typ, som är identisk med hur ett .NET-objekt är en instans av en .NET-typ. En **aktörs typ** som implementerar funktionerna i en kalkylator kan till exempel ha många aktörer av den typen som distribueras på olika noder i ett kluster. Var och en av de distribuerade aktörerna kännetecknas unikt av ett aktörs-ID.

[Replicators säkerhetskonfigurationer](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) används för att skydda kommunikations kanalen som används vid replikering. Den här konfigurationen förhindrar att tjänster ser var and ras replikeringstrafik och säkerställer att hög tillgängliga data är säkra. Som standard förhindrar ett tomt säkerhets konfigurations avsnitt replikeringen av säkerheten.
Konfigurationer för duplicering konfigurerar den ansvariga för att göra providerns tillstånd för aktörs status mycket tillförlitligt.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurera TLS för Azure Service Fabric
Processen för serverautentisering [autentiserar](../../service-fabric/service-fabric-cluster-creation-via-arm.md) kluster hanterings slut punkter till en hanterings klient. Hanterings klienten känner sedan igen att den pratar med det riktiga klustret. Det här certifikatet tillhandahåller också en [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) för https Management API och för Service Fabric Explorer över https.
Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikat utfärdare måste certifikatets ämnes namn matcha det anpassade domän namn som du använder för klustret.

Om du vill konfigurera TLS för ett program måste du först skaffa ett SSL/TLS-certifikat som har signerats av en certifikat utfärdare. CA: n är en betrodd tredje part som utfärdar certifikat för TLS-säkerhets syfte. Om du inte redan har ett SSL/TLS-certifikat måste du skaffa ett från ett företag som säljer SSL/TLS-certifikat.

Certifikatet måste uppfylla följande krav för SSL/TLS-certifikat i Azure:
-   Certifikatet måste innehålla en privat nyckel.

-   Certifikatet måste skapas för nyckel utbyte och kunna exporteras till en personal information Exchange-fil (. pfx).

-   Certifikatets ämnes namn måste matcha domän namnet som används för att komma åt din moln tjänst.

    - Hämta ett anpassat domän namn som ska användas för åtkomst till din moln tjänst.
    - Begär ett certifikat från en certifikat utfärdare med ett ämnes namn som matchar tjänstens anpassade domän namn. Om ditt anpassade domän namn till exempel är __contoso__**. com**, ska certifikatet från din certifikat utfärdare ha ämnes namnet **. contoso.com** eller __www__**. contoso.com**.

    >[!NOTE]
    >Du kan inte hämta ett SSL/TLS-certifikat från en certifikat utfärdare för __cloudapp__**.net** -domänen.

-   Certifikatet måste ha minst 2 048-bitars kryptering.

HTTP-protokollet är inte säkert och kan omfattas av avlyssnings attacker. Data som överförs via HTTP skickas som oformaterad text från webbläsaren till webb servern eller mellan andra slut punkter. Angripare kan fånga upp och Visa känsliga data som skickas via HTTP, till exempel kreditkorts information och konto inloggningar. När data skickas eller publiceras via en webbläsare via HTTPS ser SSL till att känslig information krypteras och skyddas från avlyssning.

Mer information om hur du använder SSL/TLS-certifikat finns i [Konfigurera TLS för ett program i Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Använda nätverks isolering och säkerhet med Azure Service Fabric
Konfigurera ett 3-NodeType-säkert kluster med hjälp av [Azure Resource Manager-mallen](../../azure-resource-manager/templates/template-syntax.md) som ett exempel. Styr inkommande och utgående nätverks trafik med hjälp av mall-och nätverks säkerhets grupper.

Mallen har en NSG för var och en av de virtuella datorernas skalnings uppsättningar och används för att styra trafiken in och ut ur uppsättningen. Reglerna konfigureras som standard för att tillåta all trafik som krävs för system tjänsterna och program portarna som anges i mallen. Granska de här reglerna och gör eventuella ändringar som passar dina behov, inklusive att lägga till nya regler för dina program.

Mer information finns i [vanliga nätverks scenarier för Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurera Azure Key Vault för säkerhet
Service Fabric använder certifikat för att tillhandahålla autentisering och kryptering för att skydda ett kluster och dess program.

Service Fabric använder X. 509-certifikat för att skydda ett kluster och tillhandahålla funktioner för program säkerhet. Du använder Azure Key Vault för att [Hantera certifikat](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) för Service Fabric kluster i Azure. Azure-resurs leverantören som skapar klustren hämtar certifikaten från ett nyckel valv. Providern installerar sedan certifikaten på de virtuella datorerna när klustret distribueras på Azure.

Det finns en certifikat relation mellan [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), Service Fabric klustret och resurs leverantören som använder certifikaten. När klustret skapas lagras information om certifikat relationen i ett nyckel valv.

Det finns två grundläggande steg för att konfigurera ett nyckel valv:
1. Skapa en resurs grupp specifikt för ditt nyckel valv.

    Vi rekommenderar att du sätter nyckel valvet i sin egen resurs grupp. Den här åtgärden hjälper till att förhindra förlust av nycklar och hemligheter om andra resurs grupper tas bort, till exempel lagring, beräkning eller gruppen som innehåller klustret. Resurs gruppen som innehåller nyckel valvet måste finnas i samma region som det kluster som använder det.

2. Skapa ett nyckel valv i den nya resurs gruppen.

    Nyckel valvet måste vara aktiverat för distribution. Compute Resource providern kan sedan hämta certifikaten från valvet och installera dem på VM-instanserna.

Läs mer om hur du konfigurerar ett nyckel valv i [Vad är Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat programmen som ska representera ditt kluster tilldelar du användarna de roller som stöds av Service Fabric: skrivskyddad och administratör. Du kan tilldela dessa roller med hjälp av Azure Portal.

>[!NOTE]
> Mer information om hur du använder roller i Service Fabric finns [Service Fabric rollbaserad åtkomst kontroll för Service Fabric klienter](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric stöder två åtkomst kontroll typer för klienter som är anslutna till ett [Service Fabric-kluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md): administratör och användare. Kluster administratören kan använda åtkomst kontroll för att begränsa åtkomsten till vissa kluster åtgärder för olika användar grupper. Åtkomst kontroll gör klustret säkrare.

## <a name="next-steps"></a>Nästa steg

- [Service Fabric säkerhets check lista](../../service-fabric/service-fabric-best-practices-security.md)
- Konfigurera din Service Fabric [utvecklings miljö](../../service-fabric/service-fabric-get-started.md).
- Läs mer om [Service Fabric support alternativ](../../service-fabric/service-fabric-support.md).