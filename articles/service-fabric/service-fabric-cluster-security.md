---
title: Skydda ett Azure Service Fabric-kluster
description: Lär dig mer om säkerhets scenarier för ett Azure Service Fabric-kluster och de olika tekniker som du kan använda för att implementera dem.
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573076"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric kluster säkerhets scenarier

Ett Azure Service Fabric-kluster är en resurs som du äger. Det är ditt ansvar att skydda dina kluster så att obehöriga användare kan ansluta till dem. Ett säkert kluster är särskilt viktigt när du kör produktions arbets belastningar i klustret. Det är möjligt att skapa ett oskyddat kluster, men om klustret exponerar hanterings slut punkter för det offentliga Internet kan anonyma användare ansluta till den. Oskyddade kluster stöds inte för produktions arbets belastningar. 

Den här artikeln är en översikt över säkerhets scenarier för Azure-kluster och fristående kluster och de olika tekniker som du kan använda för att implementera dem:

* Säkerhet från nod till nod
* Säkerhet från klient till nod
* Service Fabric rollbaserad åtkomst kontroll

## <a name="node-to-node-security"></a>Säkerhet från nod till nod

Säkerhet från nod till nod hjälper till att säkra kommunikationen mellan de virtuella datorerna eller datorerna i ett kluster. Det här säkerhets scenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värdbaserade program och tjänster i klustret.

![Diagram över nod-till-nod-kommunikation][Node-to-Node]

Kluster som körs på Azure och fristående kluster som körs på Windows kan båda använda antingen [certifikat säkerhet](/previous-versions/msp-n-p/ff649801(v=pandp.10)) eller [Windows-säkerhet](/previous-versions/msp-n-p/ff649396(v=pandp.10)) för Windows Server-datorer.

### <a name="node-to-node-certificate-security"></a>Säkerhet för nod-till-nod-certifikat

Service Fabric använder de X. 509-Server certifikat som du anger som en del av konfigurationen för Node-typ när du skapar ett kluster. I slutet av den här artikeln kan du se en kort översikt över vad dessa certifikat är och hur du kan hämta eller skapa dem.

Konfigurera certifikat säkerhet när du skapar klustret, antingen i Azure Portal, genom att använda en Azure Resource Manager mall eller med en fristående JSON-mall. Service Fabric SDK: s standard beteende är att distribuera och installera certifikatet med den sista i det framtida förfallo datumet. det klassiska beteendet tillåter definition av primära och sekundära certifikat, för att tillåta manuella initierade förnyelser och rekommenderas inte för användning över de nya funktionerna. De primära certifikat som ska användas har det sista datumet som upphör att gälla, bör skilja sig från administratörs klienten och de skrivskyddade klient certifikaten som du anger för [säkerhet från klient till nod](#client-to-node-security).

Information om hur du konfigurerar certifikat säkerhet i ett kluster för Azure finns i [Konfigurera ett kluster med hjälp av en Azure Resource Manager mall](service-fabric-cluster-creation-via-arm.md).

Information om hur du konfigurerar certifikat säkerhet i ett kluster för ett fristående Windows Server-kluster finns i [skydda ett fristående kluster i Windows med hjälp av X. 509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Windows-säkerhet från nod till nod

> [!NOTE]
> Windows-autentisering baseras på Kerberos. NTLM stöds inte som autentiseringstyp.
>
> När det är möjligt ska du använda 509 certifikatautentisering för Service Fabric kluster.

Information om hur du konfigurerar Windows-säkerhet för ett fristående Windows Server-kluster finns i [skydda ett fristående kluster i Windows med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Säkerhet från klient till nod

Säkerhet mellan klienter autentiserar klienter och skyddar kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet hjälper till att säkerställa att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienterna identifieras unikt genom sina Windows-säkerhetsautentiseringsuppgifter eller deras autentiseringsuppgifter för certifikat säkerhet.

![Diagram över klient-till-nod-kommunikation][Client-to-Node]

Kluster som körs på Azure och fristående kluster som körs på Windows kan båda använda antingen [certifikat säkerhet](/previous-versions/msp-n-p/ff649801(v=pandp.10)) eller [Windows-säkerhet](/previous-versions/msp-n-p/ff649396(v=pandp.10)), även om rekommendationen är att använda 509 certifikatautentisering när det är möjligt.

### <a name="client-to-node-certificate-security"></a>Säkerhet för klient-till-nod-certifikat

Konfigurera säkerhet för klient-till-nod-certifikat när du skapar klustret, antingen i Azure Portal, med hjälp av en Resource Manager-mall eller med en fristående JSON-mall. Om du vill skapa certifikatet anger du ett administratörs klient certifikat eller ett användar klient certifikat. Som bästa praxis bör administratörs klienten och användar klient certifikaten som du anger skilja sig från de primära och sekundära certifikat som du anger för [nod-till-nod-säkerhet](#node-to-node-security). Kluster certifikat har samma rättigheter som klient administratörs certifikat. De bör dock endast användas av kluster och inte av administrativa användare av säkerhets skäl.

Klienter som ansluter till klustret med hjälp av administratörs certifikatet har fullständig åtkomst till hanterings funktionerna. Klienter som ansluter till klustret med hjälp av det skrivskyddade användar klient certifikatet har bara Läs behörighet till hanterings funktionerna. Dessa certifikat används för Service Fabric RBAC som beskrivs längre fram i den här artikeln.

Information om hur du konfigurerar certifikat säkerhet i ett kluster för Azure finns i [Konfigurera ett kluster med hjälp av en Azure Resource Manager mall](service-fabric-cluster-creation-via-arm.md).

Information om hur du konfigurerar certifikat säkerhet i ett kluster för ett fristående Windows Server-kluster finns i [skydda ett fristående kluster i Windows med hjälp av X. 509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure Active Directory säkerhet från klient till nod på Azure

Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. Programmen är indelade i de med ett webbaserat inloggnings gränssnitt och de har en inbyggd klient upplevelse. Om du inte redan har skapat en klient börjar du med [att läsa hur du får en Azure Active Directory klient][active-directory-howto-tenant].

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer][service-fabric-visualizing-your-cluster] och [Visual Studio][service-fabric-manage-application-in-visual-studio]. Därför kan du skapa två Azure AD-program för att kontrol lera åtkomsten till klustret, ett webb program och ett internt program.

För kluster som körs på Azure kan du också skydda åtkomsten till hanterings slut punkter med hjälp av Azure Active Directory (Azure AD). Information om hur du skapar nödvändiga Azure AD-artefakter och hur du fyller dem när du skapar klustret finns i [Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Säkerhetsrekommendationer

För Service Fabric-kluster som distribueras i ett offentligt nätverk som hanteras i Azure är rekommendationen för ömsesidig klient-till-nod-autentisering:

* Använd Azure Active Directory för klientidentitet
* Ett certifikat för Server identitet och TLS-kryptering av http-kommunikation

För Service Fabric kluster som distribueras i ett offentligt nätverk som finns på Azure, är rekommendationen för nod-till-nod-säkerhet att använda ett kluster certifikat för att autentisera noder.

Om du har Windows Server 2012 R2 och Windows Active Directory, rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänst konton för fristående Windows Server-kluster. Annars använder du Windows-säkerhet med Windows-konton.

## <a name="service-fabric-role-based-access-control"></a>Service Fabric rollbaserad åtkomst kontroll

Du kan använda åtkomst kontroll för att begränsa åtkomsten till vissa kluster åtgärder för olika användar grupper. Detta gör klustret säkrare. Två åtkomst kontroll typer stöds för klienter som ansluter till ett kluster: administratörs roll och användar roll.

Användare som har tilldelats rollen administratör har fullständig åtkomst till hanterings funktioner, inklusive Läs-och skriv funktioner. Användare som har tilldelats användar rollen har som standard endast Läs behörighet till hanterings funktioner (till exempel fråge funktioner). De kan också lösa program och tjänster.

Ange administratörs-och användar klient roller när du skapar klustret. Tilldela roller genom att ange separata identiteter (till exempel med hjälp av certifikat eller Azure AD) för varje roll typ. Mer information om standard inställningar för åtkomst kontroll och hur du ändrar standardinställningarna finns i [Service Fabric rollbaserad åtkomst kontroll för Service Fabric klienter](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X. 509-certifikat och Service Fabric

X. 509 digitala certifikat används ofta för att autentisera klienter och servrar. De används också för att kryptera och signera meddelanden digitalt. Service Fabric använder X. 509-certifikat för att skydda ett kluster och tillhandahålla funktioner för program säkerhet. Mer information om digitala X. 509-certifikat finns i [arbeta med certifikat](/dotnet/framework/wcf/feature-details/working-with-certificates). Du använder [Key Vault](../key-vault/general/overview.md) för att hantera certifikat för Service Fabric kluster i Azure.

Några viktiga saker att tänka på:

* Om du vill skapa certifikat för kluster som kör produktions arbets belastningar använder du en korrekt konfigurerad Windows Server-certifikatutfärdare eller en från en godkänd [certifikat utfärdare (ca)](https://en.wikipedia.org/wiki/Certificate_authority).
* Använd aldrig temporära eller test certifikat som du skapar med hjälp av verktyg som MakeCert.exe i en produktions miljö.
* Du kan använda ett självsignerat certifikat, men endast i ett test kluster. Använd inte ett självsignerat certifikat i produktionen.
* När du genererar certifikatets tumavtryck ska du se till att generera ett SHA1-tumavtryck. SHA1 är det som används när du konfigurerar klient-och kluster certifikat tumavtrycken.

### <a name="cluster-and-server-certificate-required"></a>Kluster-och Server certifikat (obligatoriskt)

Dessa certifikat (en primär och eventuellt en sekundär) krävs för att skydda ett kluster och förhindra obehörig åtkomst till det. Dessa certifikat tillhandahåller autentisering av kluster och server.

Cluster Authentication autentiserar kommunikation mellan noder för kluster Federation. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret. Serverautentisering autentiserar kluster hanterings slut punkter till en hanterings klient, så att hanterings klienten vet att den pratar med det riktiga klustret och inte en "man i mitten". Det här certifikatet tillhandahåller också en TLS för HTTPS Management API och för Service Fabric Explorer över HTTPS. När en klient eller nod autentiserar en nod, är en av de inledande kontrollerna värdet för det egna namnet i fältet **ämne** . Antingen detta egna namn eller något av certifikatets alternativa namn (San) måste finnas i listan över tillåtna gemensamma namn.

Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel. Dessa certifikat har vanligt vis Extensions. pfx eller. pem  
* Certifikatet måste skapas för nyckel utbyte, vilket kan exporteras till en personal information Exchange-fil (. pfx).
* **Certifikatets ämnes namn måste matcha den domän som du använder för att få åtkomst till Service Fabric klustret**. Den här matchningen krävs för att tillhandahålla ett TLS för klustrets slut punkt för HTTPS-hantering och Service Fabric Explorer. Du kan inte hämta ett TLS/SSL-certifikat från en certifikat utfärdare (CA) för domänen *. cloudapp.azure.com. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Några andra saker att tänka på:

* **Ämnes** fältet kan ha flera värden. Varje värde föregås av en initiering som anger värde typen. Normalt är initieringen **CN** (för *eget namn*). till exempel **CN = www \. contoso.com**.
* **Ämnes** fältet kan vara tomt.
* Om fältet **Alternativt namn på certifikat mottagare** är ifyllt, måste det ha både det egna namnet på certifikatet och en post per San. Dessa anges som **DNS-namn** värden. Information om hur du skapar certifikat som har San finns i [så här lägger du till ett alternativt namn för certifikat mottagare i ett säkert LDAP-certifikat](https://support.microsoft.com/kb/931351).
* Värdet för fältet **avsett syfte** för certifikatet bör innehålla ett lämpligt värde, till exempel **serverautentisering** eller **klientautentisering**.

### <a name="application-certificates-optional"></a>Program certifikat (valfritt)

Valfritt antal ytterligare certifikat kan installeras i ett kluster för program säkerhets syfte. Innan du skapar klustret bör du tänka på vilka program säkerhets scenarier som kräver att ett certifikat installeras på noderna, till exempel:

* Kryptering och dekryptering av program konfigurations värden.
* Kryptering av data mellan noder under replikering.

Konceptet att skapa säkra kluster är detsamma, oavsett om de är Linux-eller Windows-kluster.

### <a name="client-authentication-certificates-optional"></a>Certifikat för klientautentisering (valfritt)

Valfritt antal ytterligare certifikat kan anges för administratörs-eller användar klient åtgärder. Klienten kan använda dessa certifikat när ömsesidig autentisering krävs. Klient certifikat utfärdas vanligt vis inte av en certifikat utfärdare från tredje part. I stället innehåller det personliga arkivet för den aktuella användar platsen vanligt vis klient certifikat som placeras där av en rot utfärdare. Certifikatet bör ha ett **avsett syfte** värde för **klientautentisering**.  

Som standard har kluster certifikatet administratörs behörighet för klienten. Dessa ytterligare klient certifikat bör inte installeras i klustret, men de anges som tillåtna i kluster konfigurationen.  Klient certifikaten måste dock installeras på klient datorerna för att ansluta till klustret och utföra alla åtgärder.

> [!NOTE]
> Alla hanterings åtgärder på ett Service Fabric-kluster kräver Server certifikat. Det går inte att använda klient certifikat för hantering.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett kluster i Azure med hjälp av en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md)
* [Skapa ett kluster med Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
