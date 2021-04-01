---
title: Distribuera en Azure Firewall Manager-Provider för säkerhets partner
description: Lär dig hur du distribuerar en Azure Firewall Manager-Provider för säkerhets partner med hjälp av Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 906687e08c9f31890a9ecec9154079e704512832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96485730"
---
# <a name="deploy-a-security-partner-provider"></a>Distribuera en säkerhetspartnerprovider

*Leverantörer av säkerhets partner* i Azure Firewall Manager gör att du kan använda dina välkända, bästa SECaaS-erbjudanden (Security-as-a-Service) från tredje part för att skydda Internet åtkomsten för dina användare.

Mer information om vilka scenarier som stöds och rikt linjer för bästa praxis finns i [Vad är leverantörer av säkerhets partner?](trusted-security-partners.md)


Integrerade SECaaS-partner (Security as a Service) från tredje part är nu tillgängliga: 

- **Zscaler**
- **[Check Point](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Distribuera en säkerhets leverantör från tredje part i en ny hubb

Hoppa över det här avsnittet om du distribuerar en tredjeparts-Provider till en befintlig hubb.

1. Logga in på Azure Portal på https://portal.azure.com.
2. I **Sök** skriver du **Firewall Manager** och väljer den under **tjänster**.
3. Navigera till **komma igång**. Välj **Visa skyddade virtuella nav**.
4. Välj **Skapa ny säker virtuell hubb**.
5. Ange prenumeration och resurs grupp, Välj en region som stöds och Lägg till din hubb och virtuella WAN-information. 
6. Välj **Inkludera VPN-gateway för att aktivera providers för säkerhets partner**.
7. Välj de **enheter för gateway-skalning** som är lämpliga för dina behov.
8. Välj **Nästa: Azure-brandvägg**
   > [!NOTE]
   > Leverantörer av säkerhets partner ansluter till hubben med hjälp av VPN Gateway tunnlar. Om du tar bort VPN Gateway försvinner anslutningarna till dina leverantörer av säkerhets partner.
9. Om du vill distribuera Azure-brandväggen för att filtrera privat trafik tillsammans med leverantör från tredje part för att filtrera Internet trafik, väljer du en princip för Azure-brandväggen. Se de [scenarier som stöds](trusted-security-partners.md#key-scenarios).
10. Om du bara vill distribuera en säkerhetsprovider från tredje part i hubben väljer du **Azure Firewall: enabled/disabled** för att ange att den ska **inaktive ras**. 
11. Välj  **Nästa: providern för säkerhets partner**.
12. Ange **providern för säkerhets partner** till **aktive rad**. 
13. Välj en partner. 
14. Välj **Nästa: Granska + skapa**. 
15. Granska innehållet och välj sedan **skapa**.

Distributionen av VPN gateway kan ta mer än 30 minuter.

Kontrol lera att navet har skapats genom att gå till Azure Firewall Manager->skyddade hubbar. Välj översikts sidan för hubb->för att Visa partner namnet och status som **säkerhets anslutning väntar**.

När hubben har skapats och säkerhets partnern har kon figurer ATS fortsätter du med att ansluta säkerhets leverantören till hubben.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Distribuera en säkerhets leverantör från tredje part i en befintlig hubb

Du kan också välja en befintlig hubb i ett virtuellt WAN-nätverk och konvertera det till en *säker virtuell hubb*.

1. I **komma igång** väljer du **Visa skyddade virtuella nav**.
2. Välj **konvertera befintliga hubbar**.
3. Välj en prenumeration och en befintlig hubb. Följ resten av stegen för att distribuera en provider från tredje part i en ny hubb.

Kom ihåg att en VPN-gateway måste distribueras för att konvertera en befintlig hubb till en skyddad hubb med leverantörer från tredje part.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurera säkerhets leverantörer från tredje part för att ansluta till en skyddad hubb

Om du vill konfigurera tunnlar till den virtuella hubbens VPN Gateway behöver tredjepartsleverantörer åtkomst behörighet till din hubb. Det gör du genom att associera ett huvud namn för tjänsten med din prenumeration eller resurs grupp och bevilja åtkomst behörighet. Du måste sedan ge dessa autentiseringsuppgifter till tredje part med hjälp av portalen.

### <a name="create-and-authorize-a-service-principal"></a>Skapa och auktorisera ett huvud namn för tjänsten

1. Skapa Azure Active Directory (AD) tjänstens huvud namn: du kan hoppa över omdirigerings-URL: en. 

   [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Lägg till behörigheter och omfattning för tjänstens huvud namn.
   [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Du kan begränsa åtkomsten till endast din resurs grupp för mer detaljerad kontroll.

### <a name="visit-partner-portal"></a>Besök Partner Portal

1. Följ de anvisningar som medföljer din partner för att slutföra installationen. Detta inkluderar att skicka AAD-information för att identifiera och ansluta till hubben, uppdatera de utgående principerna och kontrol lera anslutnings status och loggar.

   - [Zscaler: konfigurera Microsoft Azure virtuell WAN-integration](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: konfigurera Microsoft Azure virtuell WAN-integration](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss: konfigurera Microsoft Azure virtuell WAN-integration](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Du kan titta på statusen för att skapa tunnel på Azures virtuella WAN-portal i Azure. När tunnlarna visar **anslutna** i både Azure och partner portalen fortsätter du med nästa steg för att ställa in vägar för att välja vilka grenar och virtuella nätverk som ska skicka Internet trafik till partnern.

## <a name="configure-route-settings"></a>Konfigurera flödes inställningar

1. Bläddra till Azure Firewall Manager-> skyddade hubbar. 
2. Välj en hubb. Nu bör Hub- **statusen visas i** stället för en **väntande säkerhets anslutning**.

   Se till att leverantören av tredje part kan ansluta till hubben. Tunnlarna på VPN-gatewayen ska vara i **anslutet** tillstånd. Det här tillståndet är en mer reflekterande av anslutnings hälsan mellan hubben och partnern från tredje part, jämfört med tidigare status.
3. Välj hubben och navigera till **flödes inställningar**.

   När du distribuerar en tredjeparts-Provider i hubben konverteras hubben till en *säker virtuell hubb*. Detta säkerställer att leverantören av tredje part annonserar en router med 0.0.0.0/0 (standard) till hubben. VNet-anslutningar och platser som är anslutna till navet får dock inte den här vägen om du inte väljer vilka anslutningar som ska hämta den här standard vägen.
4. Under **Internet trafik** väljer du **VNet-till-Internet** eller **Branch-till-Internet** eller båda så att vägar konfigureras via den tredje parten.

   Detta anger bara vilken typ av trafik som ska dirigeras till hubben, men den påverkar inte vägarna på virtuella nätverk eller grenar än. Dessa vägar sprids inte till alla virtuella nätverk/grenar som är kopplade till hubben som standard.
5. Du måste välja **säkra anslutningar** och välja de anslutningar som vägarna ska ställas in på. Detta anger vilka virtuella nätverk/grenar som kan börja skicka Internet trafik till tredje parts-providern.
6. Från **väg inställningar** väljer du **säkra anslutningar** under Internet trafik och väljer sedan det VNet eller de grenar (*platser* i virtuella WAN-nätverk) som ska skyddas. Välj **säker Internet trafik**.
   ![Säker Internet trafik](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Gå tillbaka till sidan hubbar. Hubbens **providerns säkerhets partner** status bör nu  **skyddas**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Internet trafik för gren eller VNet via tjänst från tredje part

Sedan kan du kontrol lera om virtuella VNet-datorer eller filial platsen kan komma åt Internet och kontrol lera att trafiken flödar till tjänsten från tredje part.

När du har slutfört stegen för flödes inställningen skickas virtuella nätverk för virtuella datorer och avdelnings platserna en 0/0 till en tjänst väg från tredje part. Det går inte att RDP eller SSH till dessa virtuella datorer. Om du vill logga in kan du distribuera tjänsten [Azure skydds](../bastion/bastion-overview.md) i ett peer-kopplat VNet.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skydda ditt moln nätverk med Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md)