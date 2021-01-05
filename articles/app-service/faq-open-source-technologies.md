---
title: Vanliga frågor och svar om öppen källkod
description: Få svar på vanliga frågor om tekniker med öppen källkod i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831847"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Vanliga frågor och svar om öppen källkod för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar om problem med tekniker med öppen källkod för [Web Apps funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hur gör jag för att aktiverar PHP-loggning för att felsöka PHP-problem?

Så här aktiverar du PHP-loggning:

1. Logga in på din **kudu-webbplats** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. På den översta menyn väljer du **Felsök konsol**  >  **cmd**.
3. Välj mappen **plats** .
4. Välj mappen **wwwroot** .
5. Välj **+** ikonen och välj sedan **ny fil**.
6. Ange fil namnet till **.user.ini**.
7. Välj Penn ikonen bredvid **.user.ini**.
8. Lägg till den här koden i filen: `log_errors=on`
9. Välj **Spara**.
10. Välj Penn ikonen bredvid **wp-config. php**.
11. Ändra texten till följande kod:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. I Azure Portal, i menyn webbapp, startar du om din webbapp.

Mer information finns i [Aktivera fel loggar för WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hur gör jag för att logga python-programfel i appar som finns i App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hur gör jag för att ändra versionen för det Node.js program som finns i App Service?

Om du vill ändra versionen för Node.js programmet kan du använda något av följande alternativ:

* Använd **appinställningar** i Azure Portal.
  1. I Azure Portal går du till din webbapp.
  2. Välj **program inställningar** på bladet **Inställningar** .
  3. I **appinställningar** kan du inkludera WEBSITE_NODE_DEFAULT_VERSION som nyckel och vilken VERSION av Node.js du vill ha som värde.
  4. Gå till **kudu-konsolen** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
  5. Ange följande kommando för att kontrol lera Node.js-versionen:  
     ```
     node -v
     ```
* Ändra filen iisnode. yml. Om du ändrar Node.js versionen i filen iisnode. yml anges bara den körnings miljö som iisnode använder. Kudu cmd och andra använder fortfarande den Node.js version som anges i **appinställningar** i Azure Portal.

  Om du vill ange iisnode. yml manuellt skapar du en iisnode. YML-fil i rotmappen för din app. I filen inkluderar du följande rad:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Ange filen iisnode. yml genom att använda package.jspå under distribution av käll kontroll.
  Distributions processen för Azure käll kontroll omfattar följande steg:
  1. Flyttar innehåll till Azure-webbappen.
  2. Skapar ett standard skript för distribution, om det inte finns en (Deploy. cmd,. distributions-filer) i rotmappen för webbappar.
  3. Kör ett distributions skript där det skapar en iisnode. YML-fil om du nämner Node.js-versionen i package.jspå fil > motor `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Filen iisnode. yml har följande kodrad:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Jag ser meddelandet "Det gick inte att upprätta en databas anslutning" i min WordPress-app som finns App Service. Hur gör jag för att felsöka detta?

Om du ser det här felet i din Azure WordPress-app för att aktivera php_errors. log och debug. log, slutför du stegen som beskrivs i [Aktivera fel loggar för WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

När loggarna är aktiverade kan du återskapa felet och sedan kontrol lera loggarna för att se om du har slut på anslutningar:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Om du ser det här felet i fel söknings. log-eller php_errors. log-filerna överskrider din app antalet anslutningar. Om du är värd för ClearDB kontrollerar du antalet anslutningar som är tillgängliga i din [service plan](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hur gör jag för att felsöka en Node.js app som finns i App Service?

1.  Gå till **kudu-konsolen** ( `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ).
2.  Gå till mappen program loggar (D:\home\LogFiles\Application).
3.  I logging_errors.txt-filen söker du efter innehåll.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hur gör jag för att installera inbyggda python-moduler i en App Service webbapp eller API-app?

Vissa paket kan inte installeras med hjälp av pip i Azure. Paketet är kanske inte tillgängligt i python-paketets index, eller så kan en kompilator krävas (en kompilator är inte tillgänglig på den dator som kör-webbappen i App Service). Information om hur du installerar inbyggda moduler i App Service Web Apps och API Apps finns [i installera Python-moduler i App Service](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hur gör jag för att du distribuera en django-app till App Service med hjälp av Git och den nya versionen av python?

Information om hur du installerar django finns i [distribuera en django-app till App Service](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python).

## <a name="where-are-the-tomcat-log-files-located"></a>Var finns Tomcat-loggfilerna?

För Azure Marketplace och anpassade distributioner:

* Plats för mapp: D:\home\site\wwwroot\bin\apache-Tomcat-8.0.33\logs
* Intressanta filer:
    * catalina. *åååå-mm-dd*. log
    * Host Manager. *åååå-mm-dd*. log
    * värd. *åååå-mm-dd*. log
    * Cachehanteraren. *åååå-mm-dd*. log
    * site_access_log. *åååå-mm-dd*. log


För distributioner av Portal **app-inställningar** :

* Plats för mapp: D:\home\LogFiles
* Intressanta filer:
    * catalina. *åååå-mm-dd*. log
    * Host Manager. *åååå-mm-dd*. log
    * värd. *åååå-mm-dd*. log
    * Cachehanteraren. *åååå-mm-dd*. log
    * site_access_log. *åååå-mm-dd*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hur gör jag för att felsöka anslutnings fel för JDBC-drivrutin?

Du kan se följande meddelande i dina Tomcat-loggar:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Så här löser du felet:

1. Ta bort filen sqljdbc*. jar från mappen app/lib.
2. Om du använder den anpassade Tomcat eller Tomcat webb server för Azure Marketplace kopierar du den här jar-filen till mappen Tomcat lib.
3. Om du aktiverar Java från Azure Portal (Välj **Java 1,8**  >  **Tomcat-Server**) kopierar du jar-filen sqljdbc. * i mappen som är parallell med din app. Lägg sedan till följande classpath-inställning i web.config-filen:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Varför visas fel när jag försöker kopiera Live-loggfiler?

Om du försöker kopiera Live-loggfiler för en Java-app (till exempel Tomcat) kan du se följande FTP-fel:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Fel meddelandet kan variera beroende på FTP-klienten.

Detta låsnings problem har inaktuella Java-appar. Endast kudu stöder nedladdning av den här filen medan appen körs.

Om du stoppar appen tillåts FTP-åtkomst till dessa filer.

En annan lösning är att skriva ett webb jobb som körs enligt ett schema och kopierar filerna till en annan katalog. Ett exempel projekt finns i [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) -projektet.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Var hittar jag loggfilerna för Jetty?

För Marketplace och anpassade distributioner finns logg filen i mappen D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Observera att mappens plats beror på vilken version av Jetty du använder. Till exempel är sökvägen som visas här för Jetty 9.1.2. Leta efter jetty_ *YYYY_MM_DD*. stderrout. log.

För Portal App Settings-distributioner är logg filen i D:\home\LogFiles. Sök efter jetty_ *YYYY_MM_DD*. stderrout. log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kan jag skicka e-post från min Azure-webbapp?

App Service har ingen inbyggd e-postfunktion. En del användbara alternativ för att skicka e-post från din app finns i den här [Stack Overflow diskussionen](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Varför omdirigeras min WordPress-webbplats till en annan URL?

Om du nyligen har migrerat till Azure kan WordPress omdirigera till den gamla domän-URL: en. Detta orsakas av en inställning i MySQL-databasen.

WordPress kompis + är ett Azure-webbplats tillägg som du kan använda för att uppdatera URL: en för omdirigering direkt i databasen. Mer information om hur du använder WordPress kompis + finns i [WordPress-verktyg och MySQL-migrering med WordPress kompis +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

Alternativt, om du vill uppdatera omdirigerings-URL: en manuellt med hjälp av SQL-frågor eller PHPMyAdmin, se [WordPress: omdirigera till fel URL](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hur gör jag för att ändra inloggnings lösen ordet för ditt WordPress?

Om du har glömt ditt lösen ord för WordPress-inloggning kan du använda WordPress kompis + för att uppdatera det. Om du vill återställa lösen ordet installerar du WordPress kompis + Azure Site extension och slutför sedan stegen som beskrivs i [WordPress-verktyg och MySQL-migrering med WordPress kompis +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Jag kan inte logga in på WordPress. Hur gör jag för att lösa det?

Om du upptäcker att du har låst av WordPress efter att du nyligen har installerat ett plugin-program kan du ha en felaktig plugin. WordPress kompis + är ett Azure-webbplats tillägg som kan hjälpa dig att inaktivera plugin-program i WordPress. Mer information finns i [WordPress-verktyg och MySQL-migrering med WordPress kompis +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hur gör jag för att migrera min WordPress-databas?

Du har flera alternativ för att migrera MySQL-databasen som är ansluten till din WordPress-webbplats:

* Utvecklare: använda [kommando tolken eller phpMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service)
* Icke-utvecklare: Använd [WordPress kompis +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hur gör jag för att hjälp till att göra WordPress säkrare?

Information om rekommenderade säkerhets metoder för WordPress finns i [metod tips för säkerhet för WordPress i Azure](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Jag försöker använda PHPMyAdmin och jag ser meddelandet "åtkomst nekad". Hur gör jag för att lösa det?

Det här problemet kan uppstå om funktionen MySQL in-app inte körs ännu i App Service-instansen. Försök att komma åt din webbplats för att lösa problemet. Detta startar de nödvändiga processerna, inklusive MySQL in-app processen. För att verifiera att MySQL in-app körs i Process Explorer kontrollerar du att mysqld.exe visas i processerna.

När du har säkerställt att MySQL in-app körs försöker du använda PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Jag får ett HTTP 403-fel när jag försöker importera eller exportera MySQL in-app-databasen med hjälp av PHPMyadmin. Hur gör jag för att lösa det?

Om du använder en äldre version av Chrome kan du ha en känd bugg. Lös problemet genom att uppgradera till en nyare version av Chrome. Prova också att använda en annan webbläsare, till exempel Internet Explorer eller Microsoft Edge, där problemet inte uppstår.