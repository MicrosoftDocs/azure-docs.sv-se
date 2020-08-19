---
title: 'Azure AD Connect: topologier som stöds | Microsoft Docs'
description: I det här avsnittet beskrivs och topologier som inte stöds för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47973a8444de64dc5c2bb75b5f0d65d1e6d35f6e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509099"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologier för Azure AD Connect
I den här artikeln beskrivs olika lokala och Azure Active Directory (Azure AD)-topologier som använder Azure AD Connect Sync som lösning för nyckel integrering. Den här artikeln innehåller konfigurationer som stöds och som inte stöds.


Här är förklaringarna för bilder i artikeln:

| Beskrivning | Symbol |
| --- | --- |
| Lokal Active Directory skog |![Lokal Active Directory skog](./media/plan-connect-topologies/LegendAD1.png) |
| Lokal Active Directory med filtrerad import |![Active Directory med filtrerad import](./media/plan-connect-topologies/LegendAD2.png) |
| Azure AD Connect Sync-Server |![Azure AD Connect Sync-Server](./media/plan-connect-topologies/LegendSync1.png) |
| Azure AD Connect Sync-Server "mellanlagrings läge" |![Azure AD Connect Sync-Server "mellanlagrings läge"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync med Forefront Identity Manager (FIM) 2010 eller Microsoft Identity Manager (MIM) 2016 |![GALSync med FIM 2010 eller MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect Sync-Server, detaljerad |![Azure AD Connect Sync-Server, detaljerad](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Scenario som inte stöds |![Scenario som inte stöds](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft stöder inte ändring eller operativ Azure AD Connect synkronisering utanför de konfigurationer eller åtgärder som dokumenteras formellt. Någon av dessa konfigurationer eller åtgärder kan resultera i ett inkonsekvent eller tillstånd som inte stöds för Azure AD Connect Sync. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.


## <a name="single-forest-single-azure-ad-tenant"></a>En enda skog, en enda Azure AD-klient
![Topologi för en enskild skog och en enskild klient](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

Den vanligaste topologin är en enda lokal skog med en eller flera domäner, och en enda Azure AD-klient. För Azure AD-autentisering används hash-synkronisering av lösen ord. Snabb installationen av Azure AD Connect stöder bara den här topologin.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>En skog, flera Sync-servrar till en Azure AD-klient
![Stöds inte, filtrerad topologi för en enskild skog](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Det finns inte stöd för att ha flera Azure AD Connect Sync-servrar anslutna till samma Azure AD-klient, förutom en [Server för mellanlagring](#staging-server). Det stöds inte även om servrarna är konfigurerade för synkronisering med en ömsesidigt exklusiv uppsättning objekt. Du kanske har övervägt den här topologin om du inte kan komma åt alla domäner i skogen från en enskild server, eller om du vill distribuera belastningen mellan flera servrar.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Flera skogar, en enda Azure AD-klient
![Topologi för flera skogar och en enda klient](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Många organisationer har miljöer med flera lokala Active Directory skogar. Det finns olika anledningar till att ha mer än en lokal Active Directory skog. Vanliga exempel är modeller med konto resurs skogar och resultatet av en sammanslagning eller ett förvärv.

Om du har flera skogar måste alla skogar kunna kontaktas av en enda Azure AD Connect Sync-Server. Servern måste vara ansluten till en domän. Om det behövs för att få åtkomst till alla skogar kan du placera servern i ett perimeternätverk (även kallat DMZ, demilitariserad Zone och skärmat undernät).

I installations guiden för Azure AD Connect finns flera alternativ för att konsolidera användare som representeras i flera skogar. Målet är att en användare bara representeras en gång i Azure AD. Det finns vissa vanliga topologier som du kan konfigurera i den anpassade installations Sök vägen i installations guiden. På sidan **unik identifiering av användare** väljer du motsvarande alternativ som representerar din topologi. Konsolideringen är endast konfigurerad för användare. Duplicerade grupper sammanställs inte med standard konfigurationen.

Vanliga topologier beskrivs i avsnitten om olika topologier, [kompletta nät](#multiple-forests-full-mesh-with-optional-galsync)och [en topologi för konto resurser](#multiple-forests-account-resource-forest).

Standard konfigurationen i Azure AD Connect Sync antar:

* Varje användare har bara ett aktiverat konto och skogen där det här kontot finns används för att autentisera användaren. Detta antagande är för hash-synkronisering av lösen ord, direkt autentisering och Federation. UserPrincipalName och sourceAnchor/immutableID kommer från den här skogen.
* Varje användare har bara en post låda.
* Den skog som är värd för post lådan för en användare har den bästa data kvaliteten för attribut som visas i den globala adress listan i Exchange (GAL). Om det inte finns någon post låda för användaren kan alla skogar användas för att bidra till dessa attributvärden.
* Om du har en länkad post låda finns det också ett konto i en annan skog som används för inloggning.

Om din miljö inte matchar dessa antaganden inträffar följande saker:

* Om du har mer än ett aktivt konto eller fler än en post låda, väljer Synkroniseringsmotorn en och ignorerar den andra.
* En länkad post låda utan annat aktivt konto exporteras inte till Azure AD. Användar kontot visas inte som medlem i någon grupp. En länkad post låda i DirSync representeras alltid som en normal post låda. Den här ändringen är avsiktligt ett annat beteende för att bättre stödja scenarier med flera skogar.

Du hittar mer information i [förstå standard konfigurationen](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Flera skogar, flera synkroniserade servrar till en Azure AD-klient
![Topologi som inte stöds för flera skogar och flera Sync-servrar](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Det finns inte stöd för att ha mer än en Azure AD Connect Sync-server som är ansluten till en enda Azure AD-klient. Undantaget är användningen av en [fristående server](#staging-server).

Den här topologin skiljer sig från den som finns nedan i att **flera Sync-servrar** som är anslutna till en enda Azure AD-klient stöds inte.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Flera skogar, en enda Sync-Server, användare representeras i endast en katalog
![Alternativ för att endast representera användare en gång i alla kataloger](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Beskriver flera skogar och separata topologier](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

I den här miljön behandlas alla lokala skogar som separata entiteter. Ingen användare finns i någon annan skog. Varje skog har sin egen Exchange-organisation och det finns ingen GALSync mellan skogarna. Den här topologin kan vara en situation efter en sammanslagning/förvärv eller i en organisation där varje affär senhet fungerar oberoende av varandra. Dessa skogar finns i samma organisation i Azure AD och visas med en enhetlig GAL. I föregående bild representeras varje objekt i varje skog en gång i metaversum och sammanställs i mål Azure AD-klienten.

### <a name="multiple-forests-match-users"></a>Flera skogar: matcha användare
Vanliga för alla dessa scenarier är att distributions-och säkerhets grupper kan innehålla en blandning av användare, kontakter och främmande säkerhets objekt (FSPs). FSPs används i Active Directory Domain Services (AD DS) för att representera medlemmar från andra skogar i en säkerhets grupp. Alla FSPs matchas mot det faktiska objektet i Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Flera skogar: komplett nät med valfri GALSync
![Alternativ för att använda e-postattributet för matchning när användar identiteter finns i flera kataloger](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Fullständig nättopologi för flera skogar](./media/plan-connect-topologies/MultiForestFullMesh.png)

Med en fullständig nättopologi kan användare och resurser finnas i vilken skog som helst. Vanligt vis finns det dubbelriktade förtroenden mellan skogarna.

Om Exchange finns i fler än en skog kan det finnas (valfritt) en lokal GALSync-lösning. Varje användare visas sedan som en kontakt i alla andra skogar. GALSync implementeras vanligt vis via FIM 2010 eller MIM 2016. Azure AD Connect kan inte användas för lokala GALSync.

I det här scenariot kopplas identitets objekt till via e-postattributet. En användare som har en post låda i en skog är kopplad till kontakterna i de andra skogarna.

### <a name="multiple-forests-account-resource-forest"></a>Flera skogar: konto-resurs skog
![Alternativ för att använda attributen ObjectSID och msExchMasterAccountSID för matchning när identiteter finns i flera kataloger](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Konto – resurs skogens topologi för flera skogar](./media/plan-connect-topologies/MultiForestAccountResource.png)

Du har en eller flera *konto* skogar med aktiva användar konton i en skogs topologi för konto resurser. Du har också en eller flera *resurs* skogar med inaktiverade konton.

I det här scenariot har en (eller flera) resurs skog förtroende för alla konto skogar. Resurs skogen har vanligt vis ett utökat Active Directory schema med Exchange och Lync. Alla Exchange-och Lync-tjänster, tillsammans med andra delade tjänster, finns i den här skogen. Användare har ett inaktiverat användar konto i den här skogen och post lådan är länkad till konto skogen.

## <a name="office-365-and-topology-considerations"></a>Överväganden för Office 365 och topologier
Vissa Office 365-arbetsbelastningar har vissa begränsningar för topologier som stöds:

| Arbetsbelastning | Begränsningar |
| --------- | --------- |
| Exchange Online | Mer information om hybrid topologier som stöds av Exchange Online finns i [hybrid distributioner med flera Active Directory-skogar](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype för företag | När du använder flera lokala skogar stöds endast skogs topologin för konto resurser. Mer information finns i [miljö krav för Skype för företag Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Om du är en större organisation bör du överväga att använda [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) -funktionen. Det gör att du kan definiera i vilken data Center region som användarens resurser finns.

## <a name="staging-server"></a>Mellanlagrings Server
![Mellanlagrings server i en topologi](./media/plan-connect-topologies/MultiForestStaging.png)

Azure AD Connect stöder installation av en andra server i *mellanlagrings läge*. En server i det här läget läser data från alla anslutna kataloger men skriver inte något till anslutna kataloger. Den använder normal synkronisering och har därför en uppdaterad kopia av identitets data.

Vid en olycka där den primära servern slutar fungera kan du redundansväxla till mellanlagrings servern. Det gör du i guiden Azure AD Connect. Den andra servern kan finnas i ett annat data Center eftersom ingen infrastruktur delas med den primära servern. Du måste manuellt kopiera alla konfigurations ändringar som gjorts på den primära servern till den andra servern.

Du kan använda en uppsamlings Server för att testa en ny anpassad konfiguration och den inverkan den har på dina data. Du kan förhandsgranska ändringarna och justera konfigurationen. När du är nöjd med den nya konfigurationen kan du göra mellanlagrings servern till den aktiva servern och ställa in den gamla aktiva servern i mellanlagringsplatsen.

Du kan också använda den här metoden för att ersätta den aktiva Sync-servern. Förbered den nya servern och Ställ in den på mellanlagrings läge. Se till att det är i ett felfritt tillstånd, inaktivera mellanlagrings läge (gör det aktivt) och Stäng den aktuella aktiva servern.

Det är möjligt att ha fler än en uppsamlings server om du vill ha flera säkerhets kopior i olika data Center.

## <a name="multiple-azure-ad-tenants"></a>Flera Azure AD-klienter
Vi rekommenderar att du har en enda klient i Azure AD för en organisation.
Innan du planerar att använda flera Azure AD-klienter kan du läsa artikeln [administrativ enhets hantering i Azure AD](../users-groups-roles/directory-administrative-units.md). Den täcker vanliga scenarier där du kan använda en enda klient.

![Topologi för flera skogar och flera klienter](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Det finns en 1:1-relation mellan en Azure AD Connect Sync-Server och en Azure AD-klient. För varje Azure AD-klient behöver du en Azure AD Connect synkronisering av Server installationen. Azure AD-klient instanserna är isolerade efter design. Det innebär att användare i en klient organisation inte kan se användare i den andra klienten. Om du vill ha denna separation är detta en konfiguration som stöds. Annars bör du använda en enda Azure AD-klient modell.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Varje objekt bara en gång i en Azure AD-klient
![Filtrerad topologi för en enskild skog](./media/plan-connect-topologies/SingleForestFiltered.png)

I den här topologin är en Azure AD Connect Sync-Server ansluten till varje Azure AD-klient. Azure AD Connect Sync-servrar måste konfigureras för filtrering så att varje har en ömsesidigt exklusiv uppsättning objekt att arbeta med. Du kan till exempel begränsa varje server till en viss domän eller organisationsenhet.

En DNS-domän kan bara registreras i en enda Azure AD-klient. UPN för användare i den lokala Active Directory-instansen måste också använda separata namn områden. I föregående bild registreras till exempel tre separata UPN-suffix i den lokala Active Directory-instansen: contoso.com, fabrikam.com och wingtiptoys.com. Användare i varje lokal Active Directory domän använder en annan namnrymd.

>[!NOTE]
>Synkronisering av global adress lista (GalSync) görs inte automatiskt i den här topologin och kräver ytterligare en anpassad MIM-implementering för att säkerställa att varje klient har en fullständig global adress lista (GAL) i Exchange Online och Skype för företag – online.


Den här topologin har följande begränsningar för andra scenarier som stöds:

* Endast en av Azure AD-klienterna kan aktivera en Exchange hybrid med den lokala Active Directory-instansen.
* Windows 10-enheter kan bara associeras med en Azure AD-klient.
* Alternativet enkel inloggning (SSO) för lösen ords-hash-synkronisering och direktautentisering kan bara användas med en Azure AD-klient.

Kravet för en ömsesidigt exklusiv uppsättning objekt gäller också för tillbakaskrivning. Vissa tillbakaskrivning-funktioner stöds inte med den här topologin eftersom de förutsätter en enda lokal konfiguration. Dessa funktioner omfattar:

* Tillbakaskrivning av grupp med standard konfiguration.
* Tillbakaskrivning av enhet.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Varje objekt flera gånger i en Azure AD-klient
![Topologi som inte stöds för en enskild skog och flera klienter](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologi som inte stöds för en enskild skog och flera kopplingar](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Dessa uppgifter stöds inte:

* Synkronisera samma användare med flera Azure AD-klienter.
* Gör en konfigurations ändring så att användare i en Azure AD-klient visas som kontakter i en annan Azure AD-klient.
* Ändra Azure AD Connect Sync för att ansluta till flera Azure AD-klienter.

### <a name="galsync-by-using-writeback"></a>GALSync med hjälp av tillbakaskrivning
![Topologi som inte stöds för flera skogar och flera kataloger med GALSync fokusering på Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologi som inte stöds för flera skogar och flera kataloger, med GALSync fokusering på lokala Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-klienter isoleras genom design. Dessa uppgifter stöds inte:

* Ändra konfigurationen för Azure AD Connect Sync för att läsa data från en annan Azure AD-klient.
* Exportera användare som kontakter till en annan lokal Active Directory instans genom att använda Azure AD Connect synkronisering.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync med lokal Sync-Server
![GALSync i en topologi för flera skogar och flera kataloger](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Du kan använda FIM 2010 eller MIM 2016 lokalt för att synkronisera användare (via GALSync) mellan två Exchange-organisationer. Användare i en organisation visas som externa användare/kontakter i den andra organisationen. De olika lokala Active Directory instanserna kan sedan synkroniseras med sina egna Azure AD-klienter.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Använda obehöriga klienter för att komma åt Azure AD Connect Server delen
![Använda obehöriga klienter för att komma åt Azure AD Connect Server delen](./media/plan-connect-topologies/other-client-unsupported.png)

Azure Active Directory Connect-servern kommunicerar med Azure Active Directory via Azure Active Directory Connect Server delen. Den enda program vara som kan användas för att kommunicera med den här server delen är Azure Active Directory Connect. Det finns inte stöd för att kommunicera med Azure Active Directory Connect Server delen med någon annan program vara eller metod. 

## <a name="next-steps"></a>Nästa steg
Information om hur du installerar Azure AD Connect för dessa scenarier finns i [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur [du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
