---
title: Definir as configurações da regra de detecção inteligente do Application Insights do Azure com os modelos do Azure Resource Manager | Microsoft Docs
description: Automatizar o gerenciamento e a configuração das regras de detecção inteligente do Application Insights do Azure com Modelos do Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461554"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gerenciar regras de detecção inteligente do Application Insights usando modelos do Azure Resource Manager

As regras de detecção inteligente no Application Insights podem ser gerenciadas e configuradas usando [modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Esse método pode ser usado na implantação de novos recursos do Application Insights com a automação do Azure Resource Manager ou para modificar as configurações dos recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuração da regra de detecção inteligente

É possível definir as seguintes configurações para uma regra de detecção inteligente:
- Se a regra está habilitada (o padrão é **true**.)
- Se emails devem ser enviados para os proprietários de assinatura, colaboradores e leitores quando uma detecção for encontrada (o padrão é **true**.)
- Quaisquer destinatários de email adicionais que devem receber uma notificação quando uma detecção for encontrada.
- * Configuração de email não está disponível para regras de detecção inteligente marcadas como _visualização_.

Para permitir a definição das configurações da regra por meio do Azure Resource Manager, a configuração da regra de detecção inteligente agora está disponível como um recurso interno dentro do recurso do Application Insights nomeado **ProactiveDetectionConfigs**.
Para máxima flexibilidade, cada regra de detecção inteligente pode ser definida com configurações de notificação exclusivas.

## <a name="examples"></a>Exemplos

Abaixo estão alguns exemplos que mostram como definir as configurações das regras de detecção inteligente usando modelos do Azure Resource Manager.
Todos os exemplos se referem a um recurso do Application Insights nomeado _"myApplication"_ e à "regra de detecção inteligente de duração da dependência longa" que é nomeada internamente _"longdependencyduration"_.
Certifique-se de substituir o nome de recurso do Application Insights e especificar o nome interno da regra de detecção inteligente relevante. Verifique a tabela abaixo para obter uma lista dos nomes internos correspondentes do Resource Manager do Azure para cada regra de detecção inteligente.

### <a name="disable-a-smart-detection-rule"></a>Desabilitar uma regra de detecção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Desabilitar o envio de notificações de email para uma regra de detecção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adicionar destinatários de email adicionais para uma regra de detecção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

## <a name="smart-detection-rule-names"></a>Nomes das regras de detecção inteligente

Abaixo está uma tabela de nomes de regra de detecção inteligente assim como aparecem no portal, juntamente com seus nomes internos, os quais devem ser usados no modelo do Azure Resource Manager.

> [!NOTE]
> As regras de detecção inteligente marcadas como versão prévia não dão suporte a notificações por email. Portanto, somente será possível definir a propriedade habilitada para essas regras. 

| Nome da regra do portal do Azure | Nome interno
|:---|:---|
| Tempo de carregamento de página lento | slowpageloadtime |
| Tempo de resposta do servidor lento | slowserverresponsetime |
| Duração da dependência longa | longdependencyduration |
| Degradação no tempo de resposta do servidor | degradationinserverresponsetime |
| Degradação na duração da dependência | Degradação na duração da dependência |
| Degradação na taxa de gravidade de rastreamento (visualização) | extension_traceseveritydetector |
| Aumento anormal no volume de exceção (visualização) | extension_exceptionchangeextension |
| Potencial perda de memória detectada (visualização) | extension_memoryleakextension |
| Potencial problema de segurança detectado (visualização) | extension_securityextensionspackage |
| Problema de utilização de recursos detectado (visualização) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta seção só se aplica aos alertas clássicos de detecção inteligente e ajudará você a otimizar suas notificações de alerta para fazer com que somente os destinatários desejados recebam notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e a nova experiência de alertas, confira o [artigo sobre visão geral de alertas](../platform/alerts-overview.md). Atualmente, os alertas de detecção inteligente dão suporte somente à experiência de alertas clássicos. A única exceção a isso é [alertas de detecção inteligente sobre os Serviços de Nuvem do Azure](./proactive-cloud-services.md). Para controlar a notificação de alertas de detecção inteligente nos Serviços de Nuvem do Azure, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alertas clássicos/de detecção inteligente.

* Para alertas de detecção inteligente, a opção de caixa de seleção **em massa/em grupo**, se habilitada, envia para os usuários com funções de leitor, colaborador ou proprietário na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura do recurso do Application Insights fazem parte do escopo e receberão notificações. 

> [!NOTE]
> Se você estiver usando a opção de caixa de seleção **em massa/grupo** e desabilitá-la, não poderá reverter a alteração.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como detectar automaticamente:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Perdas de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)