---
title: Perguntas frequentes sobre o Azure Active Directory | Microsoft Docs
description: Este artigo responde perguntas frequentes sobre o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2caca430de5ad666f4f4341e0723bc3173d6d91a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137797"
---
# <a name="azure-active-directory-connect-faq"></a>Perguntas frequentes do Azure Active Directory Connect

## <a name="general-installation"></a>Instalação geral

**P: Como posso proteger meu servidor do Azure AD Connect para diminuir a superfície de ataque de segurança?**

A Microsoft recomenda proteger seu servidor do Azure AD Connect para diminuir a superfície de ataque de segurança para este componente crítico de seu ambiente de TI.  Seguindo as recomendações abaixo irá diminuir os riscos de segurança para sua organização.

* Implantar o Azure AD Connect em um servidor de ingressado no domínio e restringir o acesso administrativo aos administradores de domínio ou outros grupos de segurança rigidamente controlada

Para obter mais informações, consulte: 

* [Protegendo grupos de administradores](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Protegendo contas de administrador interno](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Melhoria de segurança e sustainment, reduzindo as superfícies de ataque](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Redução da superfície de ataque do Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**P: A instalação funcionará se o Administrador Global do Azure Active Directory (Azure AD) tiver a autenticação de dois fatores (2FA) habilitada?**  
A partir das compilações de fevereiro de 2016, este cenário é suportado.

**P: Existe uma forma de instalar o Azure AD Connect autônomo?**  
Instalação do Azure AD Connect é suportada apenas quando você usa o Assistente de instalação. Não há suporte para uma instalação silenciosa e autônoma.

**P: Tenho uma floresta em que um domínio não pode ser contatado. Como instalo o Azure AD Connect?**  
A partir das compilações de fevereiro de 2016, este cenário é suportado.

**P: O agente de integridade do Azure Active Directory Domain Services (Azure AD DS) funciona no núcleo do servidor?**  
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o seguinte cmdlet do PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: O Azure AD Connect oferece suporte à sincronização de dois domínios no Azure AD?**  
Sim, há suporte para esse cenário. Consulte [Vários domínios](how-to-connect-install-multiple-domains.md).
 
**P: É possível ter vários conectores para o mesmo domínio do Active Directory no Azure AD Connect?**  
Não há suporte para nenhum, vários conectores para o mesmo domínio do AD. 

**P: Posso mover o banco de dados do Azure AD Connect do banco de dados local para uma instância remota do SQL Server?**   
Sim, as etapas a seguir fornecem diretrizes gerais sobre como fazer isso. Estamos trabalhando atualmente em um documento mais detalhado.
1. Fazer backup de banco de dados ADSync do LocalDB.
A maneira mais simples de fazer isso é usar o SQL Server Management Studio instalado no mesmo computador que o Azure AD Connect. Conectar-se ao *(LocalDb).\ADSync*, e, em seguida, fazer backup de banco de dados ADSync.

2. Restaurar o banco de dados "ADSync" para a instância remota do SQL.

3. Instalar o Azure AD Connect no existente [banco de dados SQL remoto](how-to-connect-install-existing-database.md).
   O artigo demonstra como migrar para o uso de um banco de dados SQL local. Se você estiver migrando para um banco de dados SQL remoto, na etapa 5 do processo, também será necessário inserir uma conta de serviço existente na qual o serviço Windows Sync será executado. Essa conta de serviço do mecanismo de sincronização é descrita aqui:
   
      **Usar uma conta de serviço existente**: Por padrão, o Azure AD Connect usa uma conta de serviço virtual para os serviços de sincronização a serem usados. Se você usa uma instância remota do SQL Server ou usa um proxy que exija autenticação, use uma conta de serviço gerenciado ou uma conta de serviço no domínio e saber a senha. Nesses casos, insira a conta a usar. Certifique-se de que os usuários que estão executando a instalação sejam administradores de sistema no SQL, para que as credenciais de login da conta de serviço possam ser criadas. Para saber mais, confira [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Com a compilação mais recente, o provisionamento do banco de dados agora pode ser executado fora da banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário de banco de dados. Para obter mais informações, confira [Instale o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md).

Para manter as coisas simples, é recomendável que os usuários que instalam o Azure AD Connect ser administradores do sistema no SQL. No entanto, com compilações recentes, agora é possível usar administradores SQL delegados, conforme descrito em [ Instalar o Azure AD Connect usando permissões de administrador delegadas do SQL ](how-to-connect-install-sql-delegation.md).

**P: Quais são algumas das práticas recomendadas do campo?**  

Veja a seguir um documento informativo que apresenta algumas práticas recomendadas que dão suporte à engenharia, e nossos consultores desenvolvidos ao longo dos anos.  Isso é apresentado em uma lista com marcadores que pode ser referenciada rapidamente.  Embora essa lista tenta ser abrangente, pode haver outras práticas recomendadas que podem não ter tenham feito na lista.

- Se usando SQL completo e em seguida, ele deve permanecer local versus remoto
    - Menos saltos
    - Mais fácil solucionar problemas
    - Menos complexidade
    - Precisa designar recursos para SQL e permitir que a sobrecarga para o Azure AD Connect e do sistema operacional
- Ignorar Proxy se possível, se não for possível ignorar o proxy, você precisará garantir que o valor de tempo limite é maior que 5 minutos.
- Se o proxy for necessário, em seguida, você deve adicionar o proxy para o arquivo Machine. config
- Lembre-se de trabalhos do SQL locais e de manutenção e como elas afetarão o Azure AD Connect – particularmente reindexação
- Certifique-se de que o DNS pode resolver externamente
- Certifique-se de que [especificações de servidor](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) são por recomendação se você estiver usando servidores físicos ou virtuais
- Certifique-se de que se você estiver usando um servidor virtual dedicados de recursos necessários
- Certifique-se de que você tenha o disco e a configuração do disco atende as práticas recomendadas para o SQL Server
- Instalar e configurar o Azure AD Connect Health para monitoramento
- Use o limite excluir é incorporado ao Azure AD Connect.
- Revisão de atualizações de versão cuidadosamente para estar preparado para todas as alterações e novos atributos que podem ser adicionados
- Backup de tudo
    - Chaves de backup
    - Regras de sincronização de backup
    - Configuração do servidor de backup
    - Banco de dados SQL de backup
- Certifique-se de que não há nenhum 3ª agentes de backup de terceiros que estiver fazendo backup do SQL sem o gravador VSS do SQL (comum em servidores virtuais com instantâneos de terceiros 3ª)
- Limitar a quantidade de regras de sincronização personalizadas que são usadas como eles adicionam complexidade
- O deleite do Azure AD conectar servidores conforme a camada 0 servidores
- Ser desconfiada modificando as regras de sincronização de nuvem sem excelente Noções básicas sobre o impacto e os drivers de negócios
- Certifique-se de que a URL correta e as portas de Firewall estão abertas para o suporte do Azure AD Connect e Azure AD Connect Health
- Aproveite o atributo filtrado de nuvem para solucionar problemas e impedir que objetos fantasma
- Verifique se que você está usando o Documentador de configuração de conectar-se de AD do Azure para manter a consistência entre os servidores com o servidor de preparo
- Servidores de preparo devem estar em data centers separados (locais físicos
- Servidores de preparo não pretendem ser uma solução de alta disponibilidade, mas você pode ter vários servidores de preparo
- Apresentando um servidores de preparo "Latência" possa atenuar um potencial tempo de inatividade em caso de erro
- Testar e validar todas as atualizações no servidor de preparo primeiro
- Sempre valide exportações antes de alternar para o preparo serverLeverage o servidor de preparo para importações completas e sincronizações completas reduzir o impacto nos negócios
- Manter a consistência de versão entre os servidores do AD do Azure se conectar tanto quanto possível 

**P: Pode permitir que o Azure AD Connect criar a conta do Azure AD Connector no computador de grupo de trabalho?**
Não.  Para permitir que o Azure AD Connect para criar automaticamente a conta do conector do AD do Azure, a máquina deve estar ingressado no domínio.  

## <a name="network"></a>Rede
**P: Tenho um firewall, um dispositivo de rede ou outro item que limita o tempo que as conexões podem permanecer abertas na minha rede. O que meu limite de tempo limite do lado do cliente deve ser quando usa o Azure AD Connect?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 300 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Esta recomendação também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: Há suporte para SLDs (domínios de rótulo único)?**  
Embora seja altamente não usar essa configuração de rede ([consulte o artigo](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), o uso da sincronização do Azure AD Connect com um único domínio de rótulo é compatível, desde que a configuração de rede para o domínio de nível único esteja funcionando corretamente.

**P: Há suporte para florestas com domínios do AD não contíguos?**  
Não, o Azure AD Connect não oferece suporte a florestas locais que contêm namespaces contíguos.

**P: Há suporte para nomes NetBios com pontos?**  
 Não, o Azure AD Connect não oferece suporte a florestas/domínios locais em que o nome NetBios contém um ponto (.) no nome.

**P: Há suporte para ambiente puro do IPv6?**  
Não, o Azure AD Connect não oferece suporte ao ambiente puro do IPv6.

**P: Eu tenho um ambiente de várias florestas e a rede entre as duas florestas está usando NAT (Network Address Translation). Está usando o Azure AD Connect entre essas duas florestas com suporte?**</br>
 Não, o uso do Azure AD Connect via NAT não é suportado. 

## <a name="federation"></a>Federação
**P: O que devo fazer se receber um email me pedindo para renovar o certificado do Office 365?**  
Para obter orientação sobre como renovar o certificado, consulte [renovar certificados](how-to-connect-fed-o365-certs.md).

**P: A opção "Atualizar automaticamente terceira parte confiável" está definida para a terceira parte confiável do Office 365. Preciso realizar alguma ação quando meu certificado de autenticação de tokens é substituído automaticamente?**  
Use as diretrizes descritas no artigo [Renovar certificados](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: É possível renomear o servidor após a instalação do Azure AD Connect?**  
Não. Alterar o nome do servidor torna o mecanismo de sincronização incapaz de se conectar à instância do banco de dados SQL e o serviço não pode ser iniciado.

**P: Regras de sincronização da próxima geração criptográfico (NGC) têm suporte em um computador habilitado para FIPS?**  
Não.  Não há suporte para eles.

## <a name="identity-data"></a>Dados de identidade
**P: Por que o atributo userPrincipalName (UPN) do Azure AD não corresponde ao UPN local?**  
Para obter informações, consulte estes artigos:

* [Os nomes de usuário no Office 365, Azure ou Intune não coincidem com o UPN local ou a ID de logon alternativa](https://support.microsoft.com/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização do Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://support.microsoft.com/kb/2669550)

Você também pode configurar o Azure AD para permitir que o mecanismo de sincronização atualize da forma descrita em [Azure AD Connect sync service features (Recursos do serviço de sincronização do Azure AD Connect)](how-to-connect-syncservice-features.md).

**P: É possível fazer a correspondência flexível de um grupo ou objeto de contato local do Azure AD com um grupo ou objeto de contato existente do Azure AD?**  
Sim, essa correspondência flexível se baseará o proxyAddress. A correspondência suave não tem suporte para grupos que não são habilitados para email.

**P: É possível definir manualmente o atributo ImmutableId em um grupo ou objeto de contato existente do Azure AD para fazer a correspondência fixa com um grupo ou objeto de contato local do Azure AD?**  
Não, definindo manualmente o atributo de ImmutableId em um objeto de contato/grupo do AD do Azure existente para correspondência de disco rígida no momento não há suporte.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde estão documentados os cmdlets do PowerShell para o Azure AD Connect?**  
 Com exceção dos cmdlets documentados neste site, não há suporte para outros cmdlets do PowerShell encontrados no Azure AD Connect para uso do cliente.

**P: Posso usar a opção de "exportação/importação do servidor" encontrada no Synchronization Service Manager para mover a configuração entre servidores?**  
Não. Essa opção não irá recuperar todos os parâmetros de configuração e não deve ser usada. Em vez disso, use o assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Para mais informações, consulte [Migração Swing](how-to-upgrade-previous-version.md#swing-migration).

**P: As senhas podem ser armazenadas em cache para a página de entrada do Azure? Esse cache pode ser bloqueado por conter um elemento de entrada de senha com o atributo *autocomplete = "false"*?**  
Atualmente, modificar os atributos HTML do campo de entrada de **senha**, incluindo a marca de preenchimento automático, não há suporte. Estamos trabalhando em um recurso que permitirá Javascript personalizado, e permitirá que você adicione qualquer atributo ao campo de **Senha**.

**P: A página de entrada do Azure exibe os nomes dos usuários que se conectaram anteriormente com êxito. Esse comportamento pode ser desativado?**  
Atualmente, modificar os atributos HTML do campo de entrada de **senha**, incluindo a marca de preenchimento automático, não há suporte. Estamos trabalhando em um recurso que permitirá Javascript personalizado, e permitirá que você adicione qualquer atributo ao campo de **Senha**.

**P: Há uma forma de evitar sessões simultâneas?**  
Não.

## <a name="auto-upgrade"></a>Atualização automática

**P: Quais são as vantagens e consequências do uso da atualização automática?**  
É recomendável que todos os clientes habilitem a atualização automática para a instalação do Azure AD Connect. Os benefícios são que eles sempre receba os patches mais recentes, incluindo atualizações de segurança de vulnerabilidades que foram encontradas no Azure AD Connect. O processo de atualização é simples e ocorrerá automaticamente assim que uma nova versão estiver disponível. Muitos milhares de clientes do Azure AD Connect usam a atualização automática com cada nova versão.

O processo de atualização automática sempre primeiro estabelece se uma instalação está qualificada para atualização automática. Se for qualificado, a atualização é executada e testada. O processo também inclui procurando alterações personalizadas para regras e fatores ambientais específicos. Se os testes mostram que uma atualização é bem-sucedida, a versão anterior é restaurada automaticamente.

Dependendo do tamanho do ambiente, o processo pode levar algumas horas. Enquanto a atualização está em andamento, acontece sem sincronização entre o Windows Server Active Directory e o Azure AD.

**P: Recebi um email informando que a atualização automática não funciona mais e que preciso instalar uma nova versão. Por que preciso fazer isso?**  
No ano passado, lançamos um versão do Azure AD Connect que, em algumas situações, pode ter desabilitado o recurso de atualização automática no seu servidor. Corrigimos o problema no Azure AD Connect versão 1.1.750.0. Se você tiver sido afetado pelo problema, poderá atenuá-lo executando um script do PowerShell para repará-lo ou atualizando manualmente para a versão mais recente do Azure AD Connect. 

Para executar o script do PowerShell, [ baixe o script ](https://aka.ms/repairaadconnect) e execute-o no servidor do Azure AD Connect em uma janela administrativa do PowerShell. Para saber como executar o script [exibir este vídeo rápido](https://aka.ms/repairaadcau).

Para atualizar manualmente, é necessário baixar e executar a versão mais recente do arquivo AADConnect.msi.
 
-  Se sua versão atual for anterior à 1.1.750.0, [Baixe e atualize para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).
- Se sua versão do Azure AD Connect for 1.1.750.0 ou posterior, nenhuma ação adicional é necessária. Você já está usando a versão que contém a correção de atualização automática. 

**P: Recebi um email solicitando a atualização para a versão mais recente para habilitar novamente a atualização automática. Estou usando a versão 1.1.654.0. Preciso atualizar?**  
Sim, você precisa atualizar para a versão 1.1.750.0 ou posterior para habilitar novamente a atualização automática. [Faça o download e atualize para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).

**P: Recebi um email solicitando a atualização para a versão mais recente para habilitar novamente a atualização automática. Usei o PowerShell para habilitar a atualização automática, ainda preciso instalar a versão mais recente?**  
Sim, você ainda precisa atualizar para a versão 1.1.750.0 ou mais recente. Habilitar o serviço de atualização automática com o PowerShell não atenuará o problema encontrado na versão 1.1.750.0 e anteriores.

**P: Quero atualizar para uma versão mais recente, mas não sei quem instalou o Azure AD Connect e não tenho o nome de usuário e a senha. Preciso disso?**
Você não precisa saber o nome de usuário e a senha que foi usada inicialmente para atualizar o Azure AD Connect. Use qualquer conta do Azure AD que tenha a função de Administrador Global.

**P: Como descobrir a versão do Azure AD Connect que estou usando?**  
Para verificar qual versão do Azure AD Connect está instalada no servidor, vá ao Painel de Controle e procure a versão instalada do Microsoft Azure AD Connect selecionando **Programas** > **Programas e Recursos**, como mostrado aqui:

![Versão do Azure AD Connect no Painel de Controle](./media/reference-connect-faq/faq1.png)

**P: Como atualizar para a versão mais recente do Azure AD Connect?**  
Para saber como atualizar para a versão mais recente, consulte [Azure AD Connect: atualização de uma versão anterior para a mais recente](how-to-upgrade-previous-version.md). 

**P: Já atualizamos para a versão mais recente do Azure AD Connect no ano passado. Precisamos fazer a atualização novamente?**  
A equipe do Azure AD Connect faz atualizações frequentes para o serviço. Para se beneficiar de correções de bugs e atualizações de segurança, bem como novos recursos, é importante manter o servidor atualizado com a versão mais recente. Se você habilitar a atualização automática, sua versão do software é atualizado automaticamente. Para localizar o histórico de lançamento de versões do Azure AD Connect, consulte [Azure AD Connect: histórico de lançamento de versões](reference-connect-version-history.md).

**P: Quanto tempo demora para realizar a atualização, e qual é o impacto para os meus usuários?**  
O tempo necessário para atualizar depende do tamanho do seu locatário. Para organizações maiores, pode ser melhor realizar a atualização à noite ou no fim de semana. Durante a atualização nenhuma atividade de sincronização ocorre.

**P: Acredito que já atualizei para o Azure AD Connect, mas o portal do Office ainda menciona o DirSync. Por que é isso?**  
A equipe do Office está trabalhando para receber atualizações de portal do Office para refletir o nome do produto atual. Ele não reflete a ferramenta de sincronização que você está usando.

**P: Meu status de atualização automática informa: “Suspenso”. Por que está suspenso? Devo habilitá-lo?**  
Um bug foi introduzido em uma versão anterior que, em determinadas circunstâncias, deixa o status de atualização automática definido como "Suspenso". Manualmente, permitindo que ele seja tecnicamente possível, mas exige várias etapas complexas. A melhor coisa que você pode fazer é instalar a versão mais recente do Azure AD Connect.

**P: Minha empresa tem requisitos rigorosos de gerenciamento de alterações, e quero controlar quando eles são lançados. É possível controlar quando a atualização automática é iniciada?**  
Não há nenhum desses recursos hoje mesmo. O recurso está sendo avaliado para um lançamento futuro.

**P: Receberei um email se a atualização automática falhar? Como saberei se ela foi bem-sucedida?**  
Você não será notificado do resultado da atualização. O recurso está sendo avaliado para um lançamento futuro.

**P: Vocês publicam uma linha do tempo de quando planejam lançar atualizações automáticas?**  
Atualização automática é a primeira etapa no processo de lançamento de uma versão mais recente. Sempre que houver uma nova versão, as atualizações são enviadas automaticamente. As versões mais recentes do Azure AD Connect são pré-anunciadas no [Roteiro do Azure AD](../fundamentals/whats-new.md).

**P: A atualização automática também atualiza o Azure AD Connect Health?**  
Sim, atualização automática também atualiza o Azure AAD Connect Health.

**P: Vocês também fazem a atualização automática dos servidores do Azure AD Connect no modo de preparo?**  
Sim, você pode atualização automática um servidor de conexão do AD do Azure que está no modo de preparo.

**P: O que devo fazer se a atualização automática falhar e meu servidor do Azure AD Connect não iniciar?**  
Em casos raros, o serviço do Azure AD Connect não inicia após executar a atualização. Nesses casos, reinicialize o servidor, que geralmente corrige o problema. Se o serviço do Azure AD Connect não iniciar, abra um tíquete de suporte. Para obter mais informações, consulte [criar uma solicitação de serviço, entre em contato com o suporte do Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**P: Não sei quais são os riscos de atualizar para uma versão mais recente do Azure AD Connect. Vocês podem me ligar para ajudar com a atualização?**  
Se você precisar de ajuda a atualizar para uma versão mais recente do Azure AD Connect, abra um tíquete de suporte em [criar uma solicitação de serviço, entre em contato com o suporte do Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>solução de problemas
**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Pesquise a KB para soluções técnicas de conserto de problemas comuns sobre o suporte do Azure AD Connect.

[Perguntas frequentes sobre o Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Pesquisar perguntas e respostas técnicas ou fazer suas próprias perguntas, acessando [a comunidade do Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Obter suporte para o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
