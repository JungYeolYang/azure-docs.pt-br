---
title: Criar um aplicativo Web Node.js – Serviço de Aplicativo do Azure | Microsoft Docs
description: Implante seu primeiro Olá, Mundo em Node.js nos Aplicativos Web do Serviço de Aplicativo do Azure em minutos.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c103e6cb3626750414ee5083dad3e34b6be4986c
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408939"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar um aplicativo Web do Node.js no Azure

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar o Serviço de Aplicativo em _Linux_, consulte [Criar um aplicativo Web Node.js no Serviço de Aplicativo do Azure em Linux](./containers/quickstart-nodejs.md).
>

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este início rápido mostra como implantar um aplicativo Node.js no Serviço de Aplicativo do Azure. Crie o aplicativo Web usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) e use o ZipDeploy para implantar o código Node.js de exemplo para o aplicativo Web.

![Aplicativo de exemplo em execução no Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Você pode seguir as etapas aqui usando um computador Mac, Windows ou Linux. A conclusão das etapas demora cerca de cinco minutos assim que os pré-requisitos são instalados.   

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* <a href="https://nodejs.org/" target="_blank">Instalar o Node.js e o NPM</a>

## <a name="download-the-sample"></a>Baixar o exemplo

Baixe o projeto de exemplo de Node.js do [https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) e extraia o arquivo ZIP.

Abra _index.js_ e localize a seguinte linha:

```javascript
const port = process.env.PORT || 1337;
```

O Serviço de Aplicativo injeta process.env.PORT em seu aplicativo, de modo que o código usa a variável para saber em qual porta escutar. 

Em uma janela do terminal, navegue para o **diretório raiz** do projeto de exemplo do Node.js (o diretório que contém _index.js_).

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo no local para ver como ele deve ficar quando o implantar no Azure. Abra uma janela do terminal e use o script `npm start` para iniciar o servidor HTTP integrado do Node.js.

```bash
npm start
```

Abra um navegador da Web e navegue até o aplicativo de exemplo em `http://localhost:1337`.

Você vê a mensagem **Olá, Mundo** no aplicativo de exemplo exibido na página.

![Aplicativo de exemplo em execução local](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Na janela do terminal, pressione **Ctrl+C** para sair do servidor Web.

> [!NOTE]
> No Serviço de Aplicativo do Azure, o aplicativo é executado no IIS usando o [iisnode](https://github.com/Azure/iisnode). Para habilitar a execução do aplicativo com o iisnode, o diretório raiz do aplicativo contém um arquivo web.config. O arquivo pode ser lido pelos IIS, e as configurações relacionadas ao iisnode são documentadas no [repositório GitHub iisnode](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

## <a name="create-a-project-zip-file"></a>Criar um projeto de arquivo zip

Verifique se você ainda está no **diretório raiz** do projeto de exemplo (o diretório que contém _index.js_). Criar um arquivo zip de tudo em seu projeto. O comando a seguir usa a ferramenta padrão em seu terminal:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
```

Posteriormente, você carrega o arquivo ZIP para o Azure e o implanta no Serviço de Aplicativo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)] 

## <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um aplicativo Web no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). 

No exemplo a seguir, substitua `<app_name>` por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Definir tempo de execução do Node.js

Defina o tempo de execução do Node como 10.14.1. Para ver todos os tempos de execução com suporte, execute [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes).

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Navegue até o aplicativo Web recém-criado. Substitua `<app_name>` por um nome de aplicativo exclusivo.

```bash
http://<app_name>.azurewebsites.net
```

Seu novo aplicativo Web deve ficar assim:

![Página de aplicativo Web vazia](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```
http://<app_name>.azurewebsites.net
```

O código de exemplo do Node.js está em execução em um aplicativo Web do Serviço de Aplicativo do Azure.

![Aplicativo de exemplo em execução no Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**Parabéns!** Você implantou seu primeiro aplicativo do Node.js no Serviço de Aplicativo.

## <a name="update-and-redeploy-the-code"></a>Atualizar e reimplantar o código

Usando um editor de texto, abra o arquivo `index.js` no aplicativo do Node.js e faça uma pequena alteração no texto dentro da chamada para `response.end`:

```javascript
response.end("Hello Azure!");
```

Na janela do terminal local, navegue para o **diretório raiz** do aplicativo (o diretório que contém _index.js_) e crie um arquivo zip para o projeto atualizado.

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
```

Implante esse novo arquivo ZIP no Serviço de Aplicativo usando as mesmas etapas em [Implantar o arquivo zip](#deploy-zip-file).

Mude para a janela do navegador aberta na etapa **Navegar até o aplicativo** e atualize a página.

![Aplicativo de exemplo atualizado em execução no Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. 

![Página Serviço de Aplicativo no portal do Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Node.js com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
