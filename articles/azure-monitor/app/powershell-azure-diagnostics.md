---
title: Usando o PowerShell para instalar o Application Insights no Azure | Microsoft Docs
description: Automatize a configuração do Diagnóstico do Azure para redirecionar para o Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 4ac803a8-f424-4c0c-b18f-4b9c189a64a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/17/2015
ms.author: mbullwin
ms.openlocfilehash: 3c0decaa89b4ecc503157a32fcb1e5b4d249ccfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254622"
---
# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Usando o PowerShell para configurar o Application Insights para um aplicativo Web do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O [Microsoft Azure](https://azure.com) pode ser [configurado para enviar o Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) para o [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md). O diagnóstico está relacionado aos Serviços de Nuvem do Azure e às VMs do Azure. Eles complementam a telemetria que você envia de um aplicativo usando o SDK do Application Insights. Como parte do processo de automatização da criação de novos recursos no Azure, você poderá configurar o diagnóstico usando o PowerShell.

## <a name="azure-template"></a>Modelo do Azure
Se o aplicativo Web estiver no Azure e se você criar os recursos usando um modelo do Azure Resource Manager, poderá configurar o Application Insights adicionando isto ao nó de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` - um nome para o recurso do Application Insights
* `myWebAppName` - a ID do aplicativo Web

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Habilitar a extensão de diagnóstico como parte da implantação de um Serviço de Nuvem
O cmdlet `New-AzureDeployment` tem um parâmetro `ExtensionConfiguration`, que usa uma matriz de configurações de diagnóstico. Elas podem ser criadas com o cmdlet `New-AzureServiceDiagnosticsExtensionConfig` . Por exemplo: 

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Habilitar extensão de diagnóstico em um Serviço de Nuvem existente
Em um serviço existente, use `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração de extensão de diagnóstico atual
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Remover extensão de diagnóstico
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se você tiver habilitado a extensão de diagnóstico usando `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sem o parâmetro Função, poderá remover a extensão usando `Remove-AzureServiceDiagnosticsExtension` sem o parâmetro Função. Se o parâmetro Função tiver sido usado ao habilitar a extensão, ele também deverá ser usado ao removê-la.

Para remover a extensão de diagnóstico de cada função individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Consulte também
* [Monitorar aplicativos dos Serviços de Nuvem do Azure com o Application Insights](../../azure-monitor/app/cloudservices.md)
* [Enviar o Diagnóstico do Azure para o Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatizar a configuração de alertas](powershell-alerts.md)

