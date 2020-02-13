---
title: Ciągła integracja/ciągłe wdrażanie aplikacji MicroProfile przy użyciu usługi Azure Pipelines
description: Dowiedz się, jak skonfigurować cykl wydawania z zastosowaniem ciągłej integracji/ciągłego wdrażania, aby wdrożyć aplikację usługi MicroProfile w wystąpieniu usługi Azure Web App for Containers przy użyciu usługi Azure Pipelines.
services: devops
documentationcenter: MicroProfile
author: ruyakubu
manager: brunoborges
ms.author: ruyakubu
ms.date: 07/31/2019
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: cdd704626b51105f93c19378511f4a267cb56649
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999914"
---
# <a name="cicd-for-microprofile-apps-using-azure-pipelines"></a>Ciągła integracja/ciągłe wdrażanie aplikacji MicroProfile przy użyciu usługi Azure Pipelines

W tym samouczku pokazano, jak łatwo skonfigurować cykl wydawania z zastosowaniem ciągłej integracji i ciągłego wdrażania (CI/CD) w usłudze Azure Pipelines w celu wdrożenia aplikacji Java EE usługi [MicroProfile](http://microprofile.io) w usłudze Azure Web App for Containers. Aplikacja usługi MicroProfile w tym samouczku używa obrazu podstawowego platformy [Payara Micro](https://www.payara.fish/payara_micro) do utworzenia pliku WAR. 

```Dockerfile
FROM payara/micro:5.182
COPY target/*.war $DEPLOY_DIR/ROOT.war
EXPOSE 8080
```
Proces konteneryzacji usługi Azure Pipelines należy rozpocząć od utworzenia obrazu platformy Docker i wypchnięcia obrazu kontenera do usługi Azure Container Registry (ACR). Aby ukończyć proces, należy utworzyć potok wydania usługi Azure Pipelines i wdrożyć obraz kontenera w aplikacji internetowej.

## <a name="prerequisites"></a>Wymagania wstępne

1. W witrynie [Azure Portal](https://portal.azure.com) utwórz rejestr usługi [Azure Container Registry](https://azure.microsoft.com/services/container-registry).
   
1. W witrynie Azure Portal utwórz wystąpienie usługi [Azure Web App for Containers](https://azure.microsoft.com/services/app-service/containers/). Wybierz pozycję **Linux** w polu **System operacyjny**, a w polu **Konfiguruj kontener** wybierz pozycję **Szybki start** jako **Źródło obrazu**.  
   
1. Skopiuj i zapisz adres URL klonowania z repozytorium usługi GitHub: [https://github.com/Azure-Samples/microprofile-hello-azure](https://github.com/Azure-Samples/microprofile-hello-azure)
   
1. Zarejestruj się lub zaloguj się do organizacji [Azure DevOps](https://dev.azure.com) i utwórz nowy [projekt](/vsts/organizations/projects/create-project). 
   
1. Zaimportuj przykładowe repozytorium usługi GitHub do repozytoriów platformy Azure:
   
   1. Na stronie projektu usługi Azure DevOps wybierz pozycję **Repozytoria** na lewym pasku nawigacyjnym.
   1. W obszarze **or import a repository** (lub zaimportuj repozytorium) wybierz pozycję **Importuj**. 
   1. W obszarze **Adres URL klonowania** wprowadź zapisany adres URL klonowania usługi Git i wybierz pozycję **Importuj**.
  
## <a name="create-a-build-pipeline"></a>Tworzenie potoku kompilacji

Potok kompilacji ciągłej integracji w usłudze Azure Pipelines automatycznie wykonuje wszystkie zadania kompilacji za każdym razem, gdy w aplikacji źródłowej Java EE zostanie wykonane zatwierdzenie. W tym przykładzie usługa Azure Pipelines używa narzędzia Maven do kompilowania projektu usługi MicroProfile w języku Java.

1. Na stronie projektu usługi Azure DevOps wybierz kolejno opcje **Potoki** > **Kompilacje** w lewym okienku nawigacji. 
   
1. Wybierz pozycję **Nowy potok**.
   
1. Wybierz pozycję **Use the classic editor to create a pipeline without YAML** (Użyj edytora klasycznego, aby utworzyć potok bez pliku YAML). 
   
1. Upewnij się, że nazwa projektu i zaimportowane repozytorium GitHub są wyświetlane w polach, a następnie wybierz przycisk **Kontynuuj**.
   
1. Wybierz pozycję **Maven** z listy szablonów, a następnie wybierz przycisk **Zastosuj**.
   
1. W okienku po prawej stronie upewnij się, że pozycja **Hosted Ubuntu 1604** (Hostowany system Ubuntu 16.04) jest wyświetlana na liście rozwijanej **Pula agentów**.
   
   > [!NOTE]
   > To ustawienie informuje usługę Azure Pipelines, który serwer kompilacji ma być używany.  Można również użyć prywatnego, dostosowanego serwera kompilacji.
   
1. Aby skonfigurować potok pod kątem ciągłej integracji, wybierz kartę **Wyzwalacze** w okienku po lewej stronie, a następnie zaznacz pole wyboru obok pozycji **Enable continuous integration** (Włącz ciągłą integrację).  
   
1. W górnej części strony wybierz listę rozwijaną obok pozycji **Save & queue** (Zapisz i dodaj do kolejki) i wybierz przycisk **Zapisz**. 

   ![Włączanie ciągłej integracji](media/cicd-microprofile/continuous-integration.png)

## <a name="create-a-docker-build-image"></a>Tworzenie obrazu kompilacji platformy Docker

Usługa Azure Pipelines używa pliku dockerfile z obrazem podstawowym z platformy Payara Micro do utworzenia obrazu platformy Docker.  

1. Wybierz kartę **Zadania**, a następnie wybierz znak plus **+** obok pozycji **Zadanie agenta 1**, aby dodać zadanie.
   
   ![Dodawanie nowego zadania](media/cicd-microprofile/add-task.png)
   
1. W okienku po prawej stronie wybierz pozycję **Docker** z listy szablonów, a następnie wybierz przycisk **Dodaj**. 
   
1. Wybierz pozycję **buildAndPush** (Skompiluj i wypchnij) i wprowadź opis w polu **Nazwa wyświetlana** w okienku po lewej stronie.
   
1. W obszarze **Repozytorium kontenerów** wybierz pozycję **Nowy** obok pola **Rejestr kontenerów**. 
   
1. Wypełnij okno dialogowe **Add a Docker Registry service connection** (Dodawanie połączenia usługi rejestru platformy Docker) w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Typ rejestru**|Wybierz pozycję **Azure Container Registry**.|
   |**Nazwa połączenia**|Wprowadź nazwę połączenia.|
   |**Subskrypcja platformy Azure**|Wybierz subskrypcję platformy Azure z listy rozwijanej i w razie potrzeby wybierz pozycję **Autoryzuj**.|
   |**Rejestr kontenerów platformy Azure**|Wybierz nazwę rejestru usługi Azure Container Registry z listy rozwijanej.| 
   
1. Kliknij przycisk **OK**.
   
   ![Dodawanie połączenia usługi rejestru platformy Docker](media/cicd-microprofile/dockerconnection.png)
   
   > [!NOTE]
   > Jeśli używasz usługi Docker Hub lub innego rejestru, wybierz pozycję **Docker Hub** lub **Inne** zamiast **Azure Container Registry** obok pozycji **Typ rejestru**. Następnie podaj poświadczenia i informacje o połączeniu dla rejestru kontenerów.
   
1. W obszarze **Polecenia** wybierz pozycję **build** (kompiluj) z listy rozwijanej **Polecenie**.
   
1. Wybierz symbol wielokropka **...** obok pola **Dockerfile**, przejdź do pliku **Dockerfile** i wybierz go z repozytorium GitHub, a następnie wybierz przycisk **OK**. 
   
   ![Wybieranie pliku Dockerfile](media/cicd-microprofile/selectdockerfile.png)
   
1. W obszarze **Tagi** wprowadź *latest* (najnowsze) w nowym wierszu. 
   
1. W górnej części strony wybierz listę rozwijaną obok pozycji **Save & queue** (Zapisz i dodaj do kolejki) i wybierz przycisk **Zapisz**. 

## <a name="push-the-docker-image-to-acr"></a>Wypychanie obrazu platformy Docker do usługi ACR

Usługa Azure Pipelines wypycha obraz platformy Docker do usługi Azure Container Registry i używa go do uruchomienia aplikacji interfejsu API usługi MicroProfile jako skonteneryzowanej aplikacji internetowej Java.

1. Używasz platformy Docker w usłudze Azure Pipelines, dlatego utwórz kolejny szablon platformy Docker, powtarzając kroki w obszarze [Create a Docker build image](#create-a-docker-build-image) (Tworzenie obrazu kompilacji platformy Docker). Tym razem wybierz pozycję **push** (wypchnij) z listy rozwijanej **Command** (Polecenie).
   
1. Wybierz listę rozwijaną obok pozycji **Save & queue** (Zapisz i dodaj do kolejki) i wybierz przycisk **Save & queue** (Zapisz i dodaj do kolejki). 
   
1. W oknie podręcznym **Run pipeline** (Przebieg potoku) upewnij się, że pozycja **Hosted Ubuntu 1604** (Hostowany system Ubuntu 16.04) jest wybrana w polu **Pula agentów** i wybierz pozycję **Save and run** (Zapisz i uruchom). 
   
1. Po zakończeniu kompilacji możesz wybrać hiperlink na stronie **Kompilacja**, aby zweryfikować powodzenie kompilacji i wyświetlić inne szczegóły.
   
   ![Wybieranie hiperlinku kompilacji](media/cicd-microprofile/checkbuild.png)

## <a name="create-a-release-pipeline"></a>Tworzenie potoku wydania

Potok ciągłego wydania usługi Azure Pipelines automatycznie wyzwala wdrożenie do środowiska docelowego, takiego jak platforma Azure bezpośrednio po pomyślnym zakończeniu kompilacji. Możesz tworzyć potoki wydania dla środowisk, takich jak środowisko programowania, testowania, środowisko przejściowe lub produkcyjne.

1. Na stronie projektu usługi Azure DevOps wybierz kolejno opcje **Potoki** > **Wydania** w lewym okienku nawigacji. 
   
1. Wybierz pozycję **Nowy potok**.
   
1. Wybierz pozycję **Deploy a Java app to Azure App Service** (Wdrażanie aplikacji w usłudze Azure App Service) z listy szablonów, a następnie wybierz przycisk **Zastosuj**. 
   
   ![Wybieranie szablonu Deploy a Java app to Azure App Service (Wdrażanie aplikacji w usłudze Azure App Service)](media/cicd-microprofile/selectreleasetemplate.png)
   
1. W oknie podręcznym zmień wartość **Stage 1** (Etap 1) na nazwę etapu, taką jak *Dev* (Programowanie), *Test* (Testowanie), *Staging* (Przygotowanie) lub *Production* (Produkcja), a następnie zamknij okno. 
   
1. W obszarze **Artefakty** w okienku po lewej stronie wybierz pozycję **Dodaj**, aby połączyć artefakty z potoku kompilacji z potokiem wydania. 
   
1. W okienku po prawej stronie wybierz potok kompilacji z listy rozwijanej w obszarze **Source (build pipeline)** (Źródło (potok kompilacji)), a następnie wybierz pozycję **Dodaj**.
   
   ![Dodawanie artefaktu kompilacji](media/cicd-microprofile/addbuildartifact.png)
   
1. Wybierz hiperlink na etapie **Production** (Produkcja) i wybierz pozycję **View stage tasks** (Wyświetl zadania etapu).
   
   ![Wybieranie nazwy etapu](media/cicd-microprofile/viewstagetasks.png)
   
1. W okienku po prawej stronie wypełnij formularz w następujący sposób:
   
   |Pole|Wartość|
   |---|---|
   |**Subskrypcja platformy Azure**|Z listy rozwijanej wybierz subskrypcję platformy Azure.|
   |**Typ aplikacji**|Wybierz pozycję **Web App for Containers (Linux)** z listy rozwijanej.|
   |**Nazwa usługi aplikacji**|Wybierz wystąpienie usługi ACR z listy rozwijanej.|
   |**Registry or Namespaces** (Rejestr lub przestrzenie nazw)|Wprowadź nazwę usługi ACR w polu. Na przykład wprowadź *mymicroprofileregistry.azure.io*.
   |**Repozytorium**|Wprowadź repozytorium zawierające obraz platformy Docker.| 
   
   ![Konfigurowanie zadań etapu](media/cicd-microprofile/configurestage.png)
   
1. W okienku po lewej stronie wybierz pozycję **Deploy War to Azure App Service** (Wdróż plik war w usłudze Azure App Service) i w okienku po prawej stronie wprowadź tag *latest* (najnowsze) w polu **Tag**. 
   
1. W okienku po lewej stronie wybierz pozycję **Run on agent** (Uruchom na agencie), a następnie w okienku po prawej stronie wybierz pozycję **Hosted Ubuntu 1604** (Hostowany system Ubuntu 16.04) z listy rozwijanej **Pula agentów**. 

## <a name="set-up-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Dodaj lub zdefiniuj zmienne środowiskowe, aby nawiązać połączenie z rejestrem kontenerów podczas wdrażania.

1. Wybierz kartę **Zmienne**, a następnie wybierz pozycję **Dodaj**, aby dodać następujące zmienne dla adresu URL rejestru kontenerów, nazwy użytkownika i hasła. 
   
   |Nazwa|Wartość|
   |---|---|
   |*registry.url*|Wprowadź adres URL rejestru kontenerów. Na przykład: *https:\//mymicroprofileregistry.azure.io*|
   |*registry.username*|Wprowadź nazwę użytkownika dla rejestru.|
   |*registry.password*|Wprowadź hasło dla rejestru. Ze względów bezpieczeństwa wybierz ikonę kłódki, aby ukryć wartość hasła.|
   
   ![Dodawanie zmiennych](media/cicd-microprofile/addvariables.png)
   
1. Na karcie **Zadania** wybierz pozycję **Deploy War to Azure App Service** (Wdróż plik war w usłudze Azure App Service) w okienku po lewej stronie. 
   
1. W okienku po prawej stronie rozwiń obszar **Application and Configuration Settings** (Ustawienia aplikacji i konfiguracji), a następnie wybierz symbol wielokropka **...** obok pola **Ustawienia aplikacji**.
   
1. W oknie podręcznym **Ustawienia aplikacji** wybierz pozycję **Dodaj**, aby zdefiniować i przypisać zmienne ustawień aplikacji:
   
   |Nazwa|Wartość|
   |---|---|
   |*DOCKER_REGISTRY_SERVER_URL*|*$(registry.url)*|
   |*DOCKER_REGISTRY_SERVER_USERNAME*|*$(registry.username)*|
   |*DOCKER_REGISTRY_SERVER_PASSWORD*|*$(registry.password)*|
   
1. Kliknij przycisk **OK**.
   
   ![Dodawanie i ustawianie zmiennych](media/cicd-microprofile/appsettings.png)
   
## <a name="set-up-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania 

Aby włączyć ciągłe wdrażanie: 

1. Na karcie **Potok** w obszarze **Artefakty** wybierz ikonę błyskawicy w artefakcie kompilacji. 
   
1. W okienku po prawej stronie dla opcji **Continuous deployment trigger** (Wyzwalacz ciągłego wdrażania) wybierz ustawienie **Włączony**.
   
1. Wybierz pozycję **Zapisz** w prawym górnym rogu, a następnie ponownie wybierz pozycję **Zapisz**. 
   
   ![Włączanie wyzwalacza ciągłego wdrażania](media/cicd-microprofile/setcontinuousdeployment.png)
   
## <a name="deploy-the-java-app"></a>Wdrażanie aplikacji Java

Teraz, gdy włączono ciągłą integrację/ciągłe wdrażanie, zmodyfikowanie kodu źródłowego powoduje automatyczne utworzenie i uruchomienie kompilacji i wydania. Można również tworzyć i uruchamiać wydania ręcznie w następujący sposób:

1. W prawym górnym rogu strony potoku wydania wybierz pozycję **Utwórz wydanie**.
   
1. Na stronie **Tworzenie nowego wydania** wybierz nazwę etapu w obszarze **Stages for a trigger change from automated to manual** (Etapy zmiany wyzwalania z automatycznego na ręczne). 
   
1. Wybierz pozycję **Utwórz**. 
   
1. Wybierz nazwę wydania, umieść wskaźnik myszy na etapie lub wybierz go, a następnie wybierz pozycję **Wdróż**. 
   
## <a name="test-the-java-web-app"></a>Testowanie aplikacji internetowej Java

Po pomyślnym zakończeniu wdrażania przetestuj swoją aplikację internetową. 

1. Skopiuj adres URL aplikacji internetowej z witryny Azure Portal.
   
   ![Aplikacja usługi App Service w witrynie Azure Portal](media/cicd-microprofile/portalurl.png)
   
1. Wprowadź adres URL w przeglądarce internetowej, aby uruchomić aplikację. Na stronie internetowej powinien zostać wyświetlony komunikat **Hello Azure!**
   
   ![Strona aplikacji internetowej Java](media/cicd-microprofile/webapp.png)

