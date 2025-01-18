---
lab:
  title: 'Laboratório: implantar e gerenciar um aplicativo de contêiner usando os Aplicativos de Contêiner do Azure'
  type: Answer Key
  module: 'Module 6: Deploy and manage a container app using Azure Container Apps'
---

# Laboratório: implantar e gerenciar um aplicativo de contêiner usando os Aplicativos de Contêiner do Azure
# Chave de respostas do laboratório do aluno

## Instruções

Nesse laboratório, você irá implantar e gerenciar um aplicativo usando Aplicativos de Contêiner do Azure. Para implementar a solução, você começará configurando um ambiente de desenvolvimento que usa uma combinação entre ferramentas locais e recursos do Azure. Após o ambiente ter sido preparado, você usará o Registro de Contêiner do Azure, os Aplicativos de Contêiner do Azure e o Azure Pipelines para implantar e gerenciar seu aplicativo.  

No final desse laboratório, você será capaz de:

1. Configurar uma conexão segura entre um Registro de Contêiner do Azure e um Aplicativo de Contêiner do Azure.
1. Crie e configure um aplicativo de contêiner nos Aplicativos de Contêiner do Azure.
1. Configurar a integração contínua usando o Azure Pipelines.
1. Dimensionar um aplicativo implantado nos Aplicativos de Contêiner do Azure.
1. Gerenciar revisões nos Aplicativos de Contêiner do Azure.

### Exercício 1: Configurar recursos do Azure

Nesse exercício, você vai configurar os recursos do Azure que darão suporte à sua solução de Aplicativos de Contêiner do Azure.

Você levará cerca de 10 a 15 minutos para executar as seguintes tarefas:

- Examinar as configurações do grupo de recursos.
- Configurar uma Rede Virtual do Azure com sub-redes.
- Configurar um recurso do Barramento de Serviço do Azure.
- Configurar um recurso do Registro de Contêiner do Azure.

#### Tarefa 1: Examinar as configurações do grupo de recursos

Execute as etapas a seguir para examinar a configuração de local do grupo de recursos usado nesse laboratório.

1. No ambiente de laboratório, abra uma janela do navegador e, a seguir, navegue até o portal do Azure: `https://portal.azure.com/`

    Trabalhe com seu instrutor de sala de aula se precisar de ajuda para entrar no portal do Azure com uma assinatura/conta apropriada para esse laboratório.

1. Na página inicial do portal do Azure, em **Navegar**, selecione **Grupos de recursos**.

1. Na página Grupos de recursos, selecione **RG1**.

    Se o grupo de recursos RG1 não tiver sido criado, você deve criá-lo agora.

1. Anote a configuração do **Local** atribuído ao grupo de recursos **RG1**.

    Você usará o mesmo local/região ao criar outros recursos do Azure durante esse laboratório.

1. Feche a página **RG1** e, a seguir, feche a página **Grupos de recursos**.

#### Tarefa 2: Configurar uma Rede Virtual e suas sub-redes

Execute as etapas a seguir para configurar uma rede virtual e suas sub-redes.

1. Na barra de pesquisa superior do portal do Azure, na caixa de texto Pesquisar, insira **rede virtual**

1. Nos resultados da pesquisa, selecione **Redes virtuais**.

1. Selecione **Criar rede virtual**.

1. Na guia Básico, configure sua rede virtual da seguinte maneira:

    - Assinatura: Verifique se a assinatura do Azure que você está usando para este projeto guiado está selecionada.
    - Nome do grupo de recursos: Selecione **RG1**
    - Nome da rede virtual: Insira **VNET1**
    - Região: Verifique se a Região especificada corresponde à configuração de localização do seu grupo de recursos.

1. Selecione a guia **Endereços IP**.

1. Na guia Endereços IP, em **Sub-redes**, selecione o **padrão**.

1. Na página Editar sub-rede, configure a sub-rede da seguinte maneira:

    - Nome: Inserir **`PESubnet`**
    - Endereço inicial: Certifique-se de que **10.0.0.0** esteja especificado.
    - Tamanho da sub-rede: Certifique-se de que **/24 (256 endereços)** esteja especificado.

    Essa primeira sub-rede será usada para um ponto de extremidade privado (Registro de Contêiner do Azure).

1. Selecione **Salvar**.

1. Na guia de endereços IP, selecione **+ Adicionar uma sub-rede**.

1. Na página Adicionar uma sub-rede, configure a sub-rede da seguinte maneira:

    - Nome: Inserir **`ACASubnet`**
    - Endereço inicial: Certifique-se de que **10.0.4.0** esteja especificado.
    - Tamanho da sub-rede: Certifique-se de que **/23 (512 endereços)** esteja especificado.

    A sub-rede dos Aplicativos de Contêiner do Azure requer um espaço de endereço maior que 256.

1. Selecione **Adicionar**.

1. Selecione **Examinar + criar**.

1. Depois que a validação for aprovada, selecione **Criar**.

1. Aguarde a conclusão da implantação e, a seguir, feche a página da VNET1.

#### Tarefa 3: Configurar o Barramento de Serviço

Conclua as etapas a seguir para configurar uma instância do Barramento de Serviço.

1. Na barra de pesquisa superior do portal do Azure, na caixa de texto Pesquisar, insira **barramento de serviço**

1. Nos resultados da pesquisa, selecione **Barramento de Serviço**.

1. Selecione **Criar namespace do barramento de serviço**.

1. Na guia Básico, configure o namespace do barramento de serviço da seguinte maneira:

    - Assinatura: Verifique se a assinatura do Azure que você está usando para este projeto guiado está selecionada.
    - Nome do grupo de recursos: Selecione **RG1**
    - Nome do namespace: Insira **sb-az2003-** seguido de suas iniciais e da data. Por exemplo: **sb-az2003-cah12oct**.
    - Local: Certifique-se de que o Local especificado corresponde à configuração de localização do seu grupo de recursos.
    - Tipo de preço: Selecione **Básico**.

1. Selecione **Examinar + criar**.

1. Depois que a mensagem de validação bem-sucedida for exibida, selecione **Criar**.

1. Aguarde a conclusão da implantação e feche a página Namespace do Barramento de Serviço.

#### Tarefa 4: Configurar o Registro de Contêiner do Azure

Conclua as etapas a seguir para configurar uma instância do Registro de Contêiner.

1. Na barra de pesquisa superior do portal do Azure, na caixa de texto Pesquisar, insira **registro de contêiner**

1. Nos resultados da pesquisa, selecione **Registros de contêiner**.

1. Na página Registros de contêiner, selecione **Criar registro de contêiner** ou **+ Criar**.

1. Na guia Básico da página Criar registro de contêiner, especifique as seguintes informações:

    > [!NOTE]
    > O nome do registro deve ser exclusivo. Além disso, a camada Premium é necessária para o link privado com pontos de extremidade privados.

    - Assinatura: Verifique se a assinatura do Azure que você está usando para este projeto guiado está selecionada.
    - Grupo de recursos: Selecione **RG1**.
    - Nome do registro: Insira **acraz2003** seguido de suas iniciais e da data. Por exemplo: **acraz2003cah12oct**
    - Local: Certifique-se de que o Local especificado corresponde à configuração de localização do seu grupo de recursos.
    - SKU: Selecione **Premium**.

1. Selecione **Examinar + criar**.

1. Depois que a mensagem de Validação aprovada for exibida, selecione **Criar**.

1. Após a implantação ter sido concluída, abra o recurso do seu Registro de Contêiner.

1. No menu à esquerda da página do Registro de Contêiner, em **Configurações**, selecione **Rede**.

1. Na página Rede, na guia Acesso público, certifique-se de que **Todas as redes** esteja selecionado.

1. No menu à esquerda, em **Configurações**, selecione **Propriedades**.

1. Na página de propriedades, selecione **Usuário administrador** e, em seguida, selecione **Salvar**.

1. Feche a página do Registro de Contêiner.

1. Feche a janela do seu portal do Azure (no navegador).

### Exercício 2: Configurar ferramentas de desenvolvedor no ambiente do host

Nesse exercício, você vai se certificar de que as ferramentas de script e de desenvolvedor estejam configuradas corretamente na máquina virtual.

Você levará cerca de 20 a 25 minutos para executar as seguintes tarefas:

- Configurar extensões da CLI do Azure.
- Instale o Docker Desktop.
- Instalar o SDK do .NET 8.
- Atualizar o Visual Studio Code e configurar extensões.

#### Tarefa 1: Desinstalar o Visual Studio Code

Execute as etapas a seguir para desinstalar o Visual Studio Code.

1. Abra o menu Iniciar do Windows.

1. No menu Iniciar, selecione **Configurações**.

1. No menu à esquerda, selecione **Aplicativos**e, a seguir, selecione **Aplicativos Instalados**.

1. Localize o **Microsoft Visual Studio Code**.

1. À direita do Microsoft Visual Studio Code, selecione as reticências (...) e, em seguida, selecione **Desinstalar**.

1. Na janela pop-up, selecione **Desinstalar**.

1. Quando solicitado, selecione **Sim** e, em seguida, selecione **Ok**.

1. Feche as Configurações.

#### Tarefa 2: Configurar extensões da CLI do Azure

Execute as etapas a seguir para configurar a CLI do Azure.

1. Abra uma linha de comando ou um aplicativo terminal, como o Prompt de Comando do Windows.

1. Entre no Azure usando o comando `az login`.

    Uma janela do navegador será aberta para permitir que você selecione a conta do Azure.

1. Siga os prompts para concluir o processo de autenticação.

1. Feche a janela do navegador.

1. No aplicativo de linha de comando, insira o seguinte comando para instalar a extensão dos Aplicativos de Contêiner do Azure: `az extension add --name containerapp --upgrade`

1. Feche o aplicativo de linha de comando.

#### Tarefa 3: Instalar o Docker Desktop

Conclua as etapas a seguir para instalar o Docker Desktop.

1. Abra uma janela do navegador e navegue até a página de instalação do Docker Desktop: `https://docs.docker.com/desktop/install/windows-install/`

1. Selecione **Docker Desktop para Windows** e aguarde até que o arquivo do instalador seja baixado.

1. Abra o arquivo do instalador e siga as instruções online para instalar o Docker Desktop.

    O processo de instalação leva cerca de 5 minutos.

1. Após a instalação ter sido concluída, selecione **Fechar e reiniciar**.

1. Quando a máquina virtual atualizada for reiniciada, aguarde até a janela do **Contrato de Serviço de Assinatura do Docker** aparecer.

1. Na página do Contrato de Serviço de Assinatura do Docker, selecione **Aceitar**.

1. Na página "Concluir configuração do Docker Desktop", selecione **Concluir**.

1. Na página do Controle de Conta de Usuário, selecione **Sim**.

1. Na página "Bem-vindo ao Docker Desktop", selecione **Continuar sem entrar**.

1. Na página "Diga-nos qual é seu trabalho", selecione **Ignorar**.

1. Aguarde a conclusão do processo de inicialização do Mecanismo do Docker e, a seguir, minimize o aplicativo Docker Desktop.

    Não feche o Docker Desktop, apenas minimize o aplicativo em execução.

#### Tarefa 4: Instalar o SDK do .NET 8

Execute as etapas a seguir para instalar o SDK do .NET 8.

1. Abra uma janela do navegador da web e, a seguir, navegue até a página de download do SDK do .NET 8: `https://dotnet.microsoft.com/download`

1. Selecione **SDK x64 do .NET**

1. Após o download ser concluído, abra o arquivo de instalação e siga as instruções online para instalar o SDK do .NET 8.

1. Feche a janela do navegador.

#### Tarefa 5: Configurar o Visual Studio Code com extensões do C#, do Docker e do Serviço de Aplicativo do Azure

Execute as etapas a seguir para configurar o Visual Studio Code com as extensões.

1. Abra uma janela do navegador da web e, a seguir, navegue até a página de download do Visual Studio Code: `https://code.visualstudio.com/download`

1. Selecione **Windows** e aguarde até que o arquivo do instalador seja baixado.

1. Abra o arquivo do instalador do Visual Studio Code.

1. Aceite o contrato de licença e continue aceitando as configurações padrão até concluir a instalação.

1. Abra o Visual Studio Code.

1. Na **barra de atividades**, selecione **Extensões**.

    A barra Atividade é o menu vertical no lado esquerdo da interface do usuário do Visual Studio Code.

1. Na caixa de texto **Procurar extensões no Marketplace**, insira **C#**

    Inserir "C#" filtra a lista de extensões para mostrar apenas as extensões com algo relacionado à codificação em C#.

1. Na lista filtrada de extensões disponíveis, selecione a extensão rotulada "**Kit de desenvolvimento em C#** – extensão oficial de C# da Microsoft" publicada pela Microsoft.

1. Para instalar a extensão, selecione **Instalar**.

1. Aguarde a conclusão da instalação.

    O Kit de Desenvolvimento em C# leva cerca de 1 minuto para ser instalado.

1. No modo de exibição **EXTENSÕES**, substitua **C#** por **docker**.

1. Na lista filtrada de extensões disponíveis, selecione a extensão rotulada como **Docker** publicada pela Microsoft.

1. Para instalar a extensão, selecione **Instalar**.

1. No modo de exibição **EXTENSÕES**, substitua **docker**por **serviço de aplicativo do Azure**.

1. Na lista filtrada de extensões disponíveis, selecione a extensão rotulada como **Serviço de Aplicativo do Azure** publicada pela Microsoft.

1. Para instalar a extensão, selecione **Instalar**.

1. Feche o Visual Studio Code.

### Exercício 3: Criar e configurar recursos de implantação de aplicativos

Nesse exercício, você vai configurar um projeto do Azure DevOps e o Azure Pipeline, criar e efetuar push de uma imagem do Docker para o seu Registro de Contêiner e implantar um agente do Windows auto-hospedado.

Você levará cerca de 40 minutos para executar as seguintes tarefas:

- Configurar um projeto do Azure DevOps e inicializar seu repositório de códigos.
- Criar um aplicativo .NET e o sincronize com seu repositório do Azure DevOps.
- Criar uma imagem do Docker e efetuar push da imagem para o seu Registro de Contêiner do Azure.
- Criar um Pipeline do Azure chamado Pipeline1.
- Implante um agente do Windows auto-hospedado.

#### Tarefa 1: Configurar o projeto do Azure DevOps e inicializar o repositório de códigos

Execute as etapas a seguir para configurar o projeto do Azure DevOps.

1. Abra uma janela do navegador e navegue até o portal do Azure: `https://portal.azure.com/`

1. Na barra de pesquisa superior, na caixa de texto Pesquisar, insira **devops**

1. Nos resultados da pesquisa, selecione **organizações do Azure DevOps**.

1. Selecione **Minhas organizações do Azure DevOps**.

1. Na página "Precisamos de mais alguns detalhes", selecione **Continuar**.

1. Na página "Comece a usar o Azure DevOps", selecione **Criar uma nova organização** e, a seguir, selecione **Continuar**.

1. Na página "Quase terminando", insira os caracteres exibidos e, a seguir, selecione **Continuar**.

1. Na página da sua organização do Azure DevOps, selecione **Configurações da organização**.

1. No menu à esquerda, em **Segurança**, selecione **Políticas**.

1. Configure **Permitir projetos públicos** como **Ativado** e, a seguir, selecione **Salvar**.

1. Navegue de volta para a página da sua organização de DevOps.

1. Em "Criar um projeto para começar", insira as seguintes informações:

    - Nome do projeto: **AZ2003Project**
    - Descrição: **Projeto de código AZ2003**
    - Visibilidade: **Público**

1. Selecione **Criar projeto**.

1. No menu à esquerda da página do seu AZ2003Project, selecione **Repos**.

1. Em "Inicializar o branch principal com um README ou gitignore", selecione **Inicializar**.

1. Selecione **Clonar** e, a seguir, selecione **Clonar no VS Code**.

1. Em "Esse site está tentando abrir a caixa de diálogo do Visual Studio Code", selecione **Abrir**.

1. Na caixa de diálogo "Permitir que uma extensão abra esse URI", selecione **Abrir**.

1. Na janela "Escolher uma pasta para clonar", selecione **Área de Trabalho**, selecione **Nova Pasta**, digite **AZ2003** e, a seguir, pressione Enter.

1. Selecione **Selecionar como Destino de Repositório**.

1. Na caixa de diálogo "Gostaria de abrir o repositório clonado", selecione **Abrir** e, a seguir, selecione **Sim, confio nos autores**.

#### Tarefa 2: Criar um aplicativo .NET e sincronizá-lo com seu repositório do Azure DevOps

Execute as etapas a seguir para criar um aplicativo .NET e sincronizá-lo com seu repositório do Azure DevOps.

1. No menu Terminal do Visual Studio Code, selecione **Novo Terminal**.

1. No prompt de comando do terminal, para verificar se o SDK do .NET foi instalado corretamente, insira o seguinte comando:

    ```dotnetcli
    dotnet --version
    ```

    Se você receber um erro informando que o termo "dotnet" não é reconhecido, faça o seguinte:

    - No menu Iniciar do Windows, abra as **Configurações** do Windows.
    - Em Configurações, abra a guia **Aplicativos** e, a seguir, selecione **Aplicativos Instalados**.
    - Localize **SDK do .NET 8.0.100 (x64) da Microsoft** na lista de aplicativos instalados.
    - À direita do SDK do .NET 8.0.100 (x64) da Microsoft, selecione as reticências (...) e, a seguir, selecione **Modificar**.
    - Para permitir que o aplicativo faça alterações, selecione **Sim**.
    - Na janela do SDK do .NET 8.0.100 da Microsoft, selecione **Reparar**.
    - Aguarde até que a operação de reparo seja concluída com sucesso e, a seguir, selecione **Fechar**.
    - Feche a janela Configurações.
    - Volte para a janela do Visual Studio Code e feche o Visual Studio Code.
    - Reabra o Visual Studio Code.
    - No Terminal do Visual Studio Code, no prompt de comando, insira `dotnet --version`
    - Você deverá ver um número de versão exibido. Por exemplo: **8.0.100**.

1. No prompt de comando do terminal, para definir a configuração de email do Git, use o seguinte comando:

    Insira **git config --global user.email** seguido das informações de email da conta fornecidas no seu ambiente de laboratório

    Por exemplo: git config --global user.email LabUser-12345678@labhoster.onmicrosoft.com

1. No prompt de comando do terminal, para configurar o nome de usuário do Git, use o seguinte comando:

    Insira **git config --global user.name** seguido das informações de nome de usuário da conta fornecidas no seu ambiente de laboratório

    Por exemplo: git config --global user.name LabUser-12345678

1. No menu Exibir, selecione **Paleta de Comandos**.

1. No prompt de comando, selecione **.NET: Novo Projeto** e, a seguir, selecione **ASP.NET Core Vazio**.

1. Aguarde o carregamento dos recursos e, a seguir, insira as seguintes informações:

    - Na caixa de texto "Nome do novo projeto", insira **AZ2003App**
    - Aceite o diretório Padrão.

1. Abra o prompt de comando do terminal e, a seguir, execute o seguinte comando da CLI dotnet:

    ```dotnetcli
    dotnet build
    ```

1. Na pasta raiz do projeto, crie um arquivo .gitignore que contenha as seguintes informações:

    ```gitignore
    [Bb]in/
    [Oo]bj/
    ```

1. No menu Arquivo, selecione **Salvar tudo**.

1. Abra o modo de exibição de controle do código-fonte.

1. Na caixa de texto da mensagem de commit, insira **commit inicial**.

1. Selecione **Commit** e, a seguir, selecione **Sim** para preparar e fazer commit das alterações.

1. Selecione **Sincronizar Alterações** e, a seguir, selecione **Ok** para sincronizar seus arquivos com o repositório do DevOps.

1. Na caixa de diálogo do Gerenciador de Credenciais do Git, insira suas credenciais do ambiente de laboratório (nome de usuário e senha).

#### Tarefa 3: Criar uma imagem do Docker efetuar push da imagem para o seu Registro de Contêiner do Azure

Conclua as etapas a seguir para criar uma imagem do Docker e efetuar push da imagem para o Registro de Contêiner do Azure.

1. Certifique-se de que seu projeto de código AZ2003 esteja aberto no Visual Studio Code.

1. Para criar um Dockerfile, execute o seguinte comando na Paleta de Comandos: **Docker: Add Docker Files to Workspace**.

1. Quando solicitado, especifique as seguintes informações:

    - Plataforma de aplicativos: **.NET ASP.NET Core**.
    - Sistema Operacional: **Linux**.
    - Portas: **5000**.
    - Incluir arquivos do Docker Compose: **Não**

1. Para criar uma imagem do Docker, execute o seguinte comando na Paleta de Comandos: **Docker Images: Build Image**.

1. Aguarde a conclusão do processo de criação da imagem e, a seguir, feche o Terminal.

1. No menu à esquerda, para abrir o modo de exibição do Docker, selecione Docker.

1. No modo de exibição DOCKER, em Registros, selecione **Conectar Registro** e, a seguir, selecione **Registro de Contêiner do Azure**.

1. No modo de exibição DOCKER, expanda **Azure** e, a seguir, selecione **Permitir**.

1. Na janela do navegador, selecione a conta do Azure que você está usando para esse laboratório.

1. Volte para o Visual Studio Code.

1. No modo de exibição DOCKER, expanda a assinatura do Azure e verifique se o Registro de Contêiner do Azure que você criou está listado.

1. Para efetuar push da imagem do Docker para o Registro de Contêiner do Azure, execute o seguinte comando na Paleta de Comandos: **Docker Images: Efetuar Push**.

1. Quando o comando for executado, execute as seguintes etapas:

    - Selecionar grupo de imagens: selecione **az2003project**
    - Selecionar imagem (tag): selecione **mais recente**
    - Selecionar provedor de registro: selecione ** Azure**
    - Selecione sua assinatura.
    - Selecione um Registro de Contêiner do Azure para o qual efetuar push: selecione o registro de contêiner que você criou. Por exemplo: acraz2003cah12oct.
    - Para implantar a imagem, pressione Enter.

1. Aguarde a conclusão do processo de push da imagem e, a seguir, feche o Terminal.

1. Abra a exibição Controle do Código-Fonte, insira uma mensagem de commit e, a seguir, **Commit** e **Sincronizar Alterações**.

#### Tarefa 4: Criar um Pipeline do Azure chamado Pipeline1

Execute as etapas a seguir para criar um Pipeline do Azure chamado Pipeline1.

1. Abra o projeto do Azure DevOps.

1. No menu esquerdo, selecione **Pipelines**.

1. Selecione **Criar pipeline**.

1. Selecionar **Git do Azure Repos**.

1. Na página "Selecionar um repositório", selecione **AZ2003Project**.

1. Selecione **Pipeline de início**.

1. Em Salvar e executar, selecione **Salvar** e, em seguida, selecione **Salvar**.

1. Para alterar o nome do pipeline para "Pipeline1", execute as seguintes etapas:

    1. No menu esquerdo, selecione **Pipelines**.

    1. À direita do pipeline do AZ2003Project, selecione **Mais opções** e, a seguir, selecione **Renomear/migrar**.

    1. Na caixa de diálogo Renomear/mover pipeline, em Nome, insira **Pipeline1** e selecione **Salvar**.

#### Tarefa 5: Implantar um agente auto-hospedado do Windows

Para que um Azure Pipeline crie e implante o Windows, o Azure e outras soluções do Visual Studio, você precisa de pelo menos um agente do Windows no ambiente do host.

Execute as etapas a seguir para implantar um agente auto-hospedado do Windows.

1. Navegue até a página inicial da sua organização de DevOps.

1. No canto superior direito, selecione **Configurações de usuário**.

1. Na caixa de diálogo "Configurações de usuário", selecione **Tokens de acesso pessoal**.

1. Para criar um token de acesso pessoal, selecione **+ Novo Token**.

1. Em Nome, insira **AZ2003**.

1. Na parte inferior da janela "Criar um novo token de acesso pessoal", para ver a lista completa de escopos, selecione **Mostrar todos os escopos**.

1. Para o escopo, selecione **Pools de agentes (ler, gerenciar)** e **Grupo de implantação (ler, gerenciar)**.

    Certifique-se de que todas as outras caixas estejam desmarcadas.

1. Selecione **Criar**.

1. Na página Sucesso, para copiar o token selecione **Copiar para a área de transferência** e, a seguir, selecione **Fechar**.

1. Abra o Bloco de Notas e salve uma cópia do token.

    Você usará esse token quando configurar o agente.

1. Navegue até sua organização de DevOps e, a seguir, selecione **Configurações da organização**.

1. No menu do lado esquerdo, em Pipelines, selecione **Pools de agentes**.

1. Se a caixa de diálogo **Obter o agente** se abrir, pule para a etapa seguinte; caso contrário, execute as seguintes etapas:

    1. Para selecionar o pool padrão, selecione **Padrão**.

        Se o pool **padrão** não existir, selecione **Adicionar pool**e insira as seguintes informações:

        1. Em Tipo do pool, selecione **Auto-hospedado**.

        1. Em Nome, insira **Padrão**

        1. Selecione **Criar**.

        1. Para abrir o pool que você acabou de criar, selecione **Padrão**.

    1. Em Padrão, selecione a guia **Agentes** e, a seguir, selecione **Novo agente**.

1. Na caixa de diálogo Obter o agente, conclua as seguintes etapas:

    1. Selecione a guia **Windows**.

    1. No painel à esquerda, selecione **x64**.

    1. No painel à direita, selecione **Baixar**.

1. Aguarde o download ser concluído.

1. Feche a caixa de diálogo "Obter o agente".

    A próxima série de etapas de instrução irá orientar você ao longo do processo "Criar o agente".

1. Use o Explorador de Arquivos do Windows para criar o seguinte local de pasta para o agente:

    ```dos
    C:\agents
    ```

1. Use o Explorador de Arquivos do Windows para desempacotar o arquivo zip do agente baixado no diretório de agentes que você acabou de criar.

1. Aguarde a conclusão do processo de extração de arquivos e, a seguir, feche o Explorador de Arquivos.

1. Abra o PowerShell como Administrador, navegue até a pasta dos agentes e, a seguir, insira o seguinte comando do PowerShell:

    ```powershell
    .\config
    ```

1. Responda aos prompts de configuração da seguinte maneira:

    - Enter server URL >: insira a URL da sua organização de DevOps. Como: `https://dev.azure.com/<your organization>`
    - Enter authentication type (press enter for PAT) >: pressione Enter.
    - Enter personal access token >: Cole o token de acesso pessoal que você copiou para o Bloco de Notas.
    - Enter agent pool (press enter for default) >: pressione Enter.
    - Enter agent name (press enter for YOUR-PC-NAME) >: insira **az2003-agent**
    - Enter work folder (press enter for _work) >: pressione Enter.
    - Inserir agente de execução como serviço? (Y/N) (press enter for N) >: insira **Y** (Sim/Não)
    - Enter enable SERVICE_SID_TYPE_UNRESTRICTED for agent service (Y/N) (press enter for N) >: insira **Y**
    - Enter User account to use for the service (press enter for NT AUTHORITY\NETWORK SERVICE) >: pressione Enter.
    - Digite se deseja impedir que o serviço seja iniciado imediatamente após a conclusão da configuração? (Y/N) (press enter for N) >: pressione Enter.

    Uma mensagem informando que o agente iniciado com êxito é exibida.

    Para obter ajuda extra, confira a seguinte documentação: `https://learn.microsoft.com/azure/devops/pipelines/agents/windows-agent`

1. Feche o Windows PowerShell.

### Exercício 4: Configurar o Registro de Contêiner do Azure para uma conexão segura com os Aplicativos de Contêiner do Azure

Você foi solicitado a configurar seus recursos do Azure para cumprir os seguintes requisitos:

- Seu grupo de recursos deve incluir uma identidade gerenciada atribuída pelo usuário.
- Seu registro de contêiner deve conseguir usar a identidade gerenciada para efetuar pull de artefatos.
- O acesso para a identidade gerenciada deve ser limitado usando o princípio de privilégio mínimo.
- Seu registro de contêiner deve ser acessível de um ponto de extremidade privado em VNET1/PESubnet.

Nesse exercício, você vai configurar uma instância do registro de contêiner para uma conexão segura com um aplicativo de contêiner.

Você levará cerca de 10 minutos para executar as seguintes tarefas:

- Configure uma identidade gerenciada atribuída pelo usuário.
- Configure seu registro de contêiner com permissões AcrPull para a identidade gerenciada.
- Configure seu registro de contêiner com uma conexão de ponto de extremidade privado.

#### Tarefa 1: Configurar uma identidade gerenciada atribuída pelo usuário

Conclua as etapas a seguir para configurar uma identidade gerenciada atribuída pelo usuário.

1. Abra o portal do Azure.

1. Na barra de pesquisa superior do portal do Azure, insira **identidade gerenciada**

1. Na lista filtrada de recursos, selecione **Identidade Gerenciada Atribuída pelo Usuário**.

1. Na página Criar Identidade Gerenciada Atribuída pelo Usuário, especifique as seguintes informações:

    - Assinatura: Especifique a assinatura do Azure que você está usando para esse projeto guiado.
    - Grupo de recursos: **RG1**
    - Região: Insira a Região que corresponda à configuração de região do seu grupo de recursos.
    - Nome: **uai-az2003**

1. Selecione **Examinar + criar**.

1. Aguarde enquanto as configurações são validadas e, a seguir, selecione **Criar**.

1. Feche a página de identidade gerenciada.

#### Tarefa 2: Configurar seu registro de contêiner com permissões AcrPull para a identidade gerenciada

Conclua as etapas a seguir para configurar o Registro de Contêiner com permissões AcrPull para a identidade gerenciada.

1. No portal do Azure, abra o recurso Registro de Contêiner.

1. No menu à esquerda, selecione **Controle de Acesso (IAM)**.

1. Na página Controle de acesso (IAM), selecione **Adicionar Atribuição de Função**.

1. Procure a função AcrPull e selecione **AcrPull**.

1. Selecione **Avançar**.

1. Na guia Membros, à direita de Atribuir acesso a, selecione **Identidade gerenciada**.

1. Selecione **+ Selecionar membros**.

1. Na página Selecionar identidades gerenciadas, em Identidade gerenciada, selecione **Identidade gerenciada atribuída pelo usuário** e selecione a identidade gerenciada atribuída pelo usuário criada para este projeto.

    Por exemplo: uai-az2003.

1. Na página Selecionar identidades gerenciadas, clique em **Selecionar**.

1. Na guia Membros da página Adicionar atribuição de função, selecione **Revisar + atribuir**.

1. Na guia Examinar + atribuir, selecione **Examinar + atribuir**.

1. Aguarde até a atribuição de função ser adicionada.

    Uma notificação será exibida, mas se você deixar passar, poderá verificar a guia "Atribuições de função" para verificar se a função AcrPull foi atribuída a uai-az2003.

#### Tarefa 3: Configurar seu registro de contêiner com uma conexão de ponto de extremidade privado

Execute as etapas a seguir para configurar seu registro de contêiner com uma conexão de ponto de extremidade privado.

1. Verifique se o recurso Registro de Contêiner está aberto no portal.

1. Em Configurações, selecione **Rede**.

1. Na guia Acesso privado, selecione **+ Criar uma conexão de ponto de extremidade privado**.

1. Na guia Básico, em Detalhes do projeto, especifique as seguintes informações:

    - Assinatura: Especifique a assinatura do Azure que você está usando para esse projeto guiado.
    - Grupo de recursos: **RG1**
    - Nome: **pe-acr-az2003**
    - Região: Verifique se a Região especificada corresponde à configuração de região do seu grupo de recursos.

1. Selecione **Avançar: Recurso**.

1. Na guia Recurso, verifique se as seguintes informações são exibidas:

    - Assinatura: Verifique se a assinatura do Azure que você está usando para este projeto guiado está selecionada.
    - Tipo de recurso: Verifique se **Microsoft.ContainerRegistry/registries** está selecionado.
    - Recurso: Verifique se o nome do seu registro está selecionado.
    - Sub-recursos de destino: Verifique se **registro** está selecionado.

1. Selecione **Próximo: Rede Virtual**.

1. Na guia Rede Virtual, em Rede, verifique se as seguintes informações são exibidas:

    - Rede Virtual: Verifique se VNET1 está selecionada
    - Sub-rede: Verifique se PESubnet está selecionada.

1. Selecione **Avançar: DNS**.

1. Na guia DNS, em Integração de DNS Privado, verifique se as seguintes informações são exibidas:

    - Integrar à zona DNS privada: Verifique se **Sim** está selecionado.
    - Zona DNS privada: Observe que **(novo) privatelink.azurecr.io** está especificado.

1. Selecione **Avançar: Marcas**.

1. Selecione **Avançar: Revisar + criar**.

1. Na guia Revisar + criar, quando aparecer a mensagem Validação aprovada, selecione **Criar**.

1. Aguarde até que a implantação seja concluída.

1. Quando estiver vendo **Sua implantação foi concluída**, feche a página de implantação do ponto de extremidade privado.

### Exercício 5: Criar e configurar um aplicativo de contêiner nos Aplicativos de Contêiner do Azure

Você foi solicitado a configurar um Aplicativo de Contêiner que cumpra os seguintes requisitos:

- É implantado em VNET1/ACASubnet.
- Efetua pull de uma imagem de um registro de contêiner.
- Autentique usando uma identidade gerenciada atribuída pelo usuário (uai-az2003).
- Usa o Aplicativo de Contêiner para se conectar a uma instância do Barramento de Serviço usando o tipo de cliente .NET.
- O aplicativo pode executar até duas réplicas que são adicionadas sempre que existirem 1.000 solicitações HTTP simultâneas.

Nesse exercício, você vai implantar um aplicativo de contêiner a partir de uma imagem do Registro de Contêiner do Azure para a plataforma de Aplicativos de Contêiner do Azure.

Você levará cerca de 20 a 25 minutos para executar as seguintes tarefas:

- Criar um Aplicativo de Contêiner que use uma imagem do Registro de Contêiner do Azure.
- Configurar o aplicativo de contêiner para autenticar usando a identidade atribuída pelo usuário.
- Configurar uma conexão entre o aplicativo de contêiner e o Barramento de Serviço.
- Configurar regras de dimensionamento de HTTP.

#### Tarefa 1:  Criar um aplicativo de contêiner que use uma imagem do Registro de Contêiner do Azure

Execute as etapas a seguir para criar um Aplicativo de Contêiner que use uma imagem do Registro de Contêiner do Azure.

1. Na barra de pesquisa superior do portal do Azure, insira **aplicativo de contêiner**

1. Na lista filtrada de recursos, selecione **Aplicativos de Contêiner**.

1. Na página Aplicativos de Contêiner, selecione **Criar aplicativo de contêiner**.

1. Na guia Básico, especifique o seguinte:

    - Assinatura: Especifique a assinatura do Azure que você está usando para esse projeto guiado.
    - Grupo de recursos: **RG1**
    - Nome do aplicativo de contêiner: **aca-az2003**
    - Região: Verifique se a Região especificada corresponde à configuração de região da VNET1 (que deve corresponder ao local do seu grupo de recursos).

        O aplicativo de contêiner precisa estar na mesma região/local que a rede virtual para que você possa escolher VNET1 para o ambiente gerenciado. Para este projeto guiado, mantenha todos os seus recursos na região/local especificado para o seu grupo de recursos.

    - Ambiente de Aplicativos de Contêiner: Selecione **Criar**.

1. Na página Criar Ambiente de Aplicativos de Contêiner, selecione a guia **Rede** e depois especifique o seguinte:

    - Use sua própria rede virtual: Selecione **Sim**.
    - Rede virtual: Selecione **VNET1**.
    - Sub-rede da infraestrutura: **ACASubnet**.

    > [!NOTE]
    > Se a sub-rede ACASubnet não estiver listada, cancele esse processo de criação, abra seu recurso de rede virtual, ajuste o intervalo de endereços ACASubnet para **10.0.2.0/23** e, a seguir, reinicie as etapas para criar o recurso do Aplicativo de Contêiner.

1. Na página Criar Ambiente de Aplicativos de Contêiner, selecione **Criar**.

1. Na página Criar Aplicativo de Contêiner, selecione a guia Contêiner e especifique o seguinte:

    - Use a imagem de início rápido: **Desmarque** essa configuração.
    - Nome: Certifique-se de que **aca-az2003** esteja especificado.
    - Origem da imagem: Verifique se **Registro de Contêiner do Azure** está selecionado.
    - Registro: Selecione seu registro de contêiner. Por exemplo: **acraz2003cah12oct.azurecr.io**
    - Imagem: Selecione **az2003project**
    - Marca da imagem: Selecione **mais recente**

1. Selecione **Examinar + criar**.

1. Depois que a verificação for Aprovada, selecione **Criar**.

1. Aguarde até que a implantação seja concluída.

    > [!NOTE]
    > Essa implantação pode demorar cerca de 5-10 minutos para ser concluída.

#### Tarefa 2: Configurar o aplicativo de contêiner para autenticar usando a identidade atribuída pelo usuário

Conclua as etapas a seguir para configurar o aplicativo de contêiner para autenticação usando a identidade atribuída pelo usuário.

1. No portal do Azure, abra o Aplicativo de Contêiner que você criou.

1. Em Configurações, selecione **Identidade**.

1. Selecione a guia para **Atribuído pelo usuário**.

1. Selecione **Adicionar identidade gerenciada atribuída pelo usuário**.

1. Na página "Adicionar identidade gerenciada atribuída pelo usuário", selecione **uai-az2003** e, a seguir, selecione **Adicionar**.

#### Tarefa 3: Configurar uma conexão entre o aplicativo de contêiner e o Barramento de Serviço

Conclua as etapas a seguir para configurar uma conexão entre o aplicativo de contêiner e o Barramento de Serviço.

1. No portal do Azure, certifique-se de que seu Aplicativo de Contêiner esteja aberto.

1. Em Configurações, selecione **Conector de Serviço (Versão Prévia)**.

1. Selecione **Conectar aos seus Serviços**.

1. Na página Criar conexão, especifique o seguinte:

    - Tipo de serviço: Selecione **Barramento de Serviço**.
    - Tipo de cliente: Selecione **.NET**.

1. Selecione **Próximo: Autenticação**.

1. Na guia Autenticação, selecione **Identidade gerenciada atribuída pelo usuário**.

1. Para alterar as guias, selecione **Revisar + Criar**.

1. Depois que a mensagem de Validação aprovada for exibida, selecione **Criar**.

1. Aguarde até a conexão ser criada.

     A conexão do Barramento de Serviço estará listada na página do Conector de Serviço (versão prévia).

#### Tarefa 4: Configurar regras de dimensionamento de HTTP

Execute as etapas a seguir para configurar regras de dimensionamento de HTTP para seu Aplicativo de Contêiner.

1. Certifique-se de que seu Aplicativo de Contêiner esteja aberto no portal.

1. No menu do lado esquerdo, em Aplicativo, selecione **Revisões e réplicas**.

1. Observe o Nome atribuído à sua revisão ativa.

1. No menu do lado esquerdo, em Aplicativo, selecione **Dimensionar**.

1. Observe a **configuração atual da regra de escala**, defina as réplicas Mín/máx da seguinte maneira:

    - Defina o mínimo de réplicas: 0
    - Defina o máximo de réplicas: 2

1. Em Regra de escala, selecione **+ Adicionar**.

1. Na página Adicionar regra de escala, especifique o seguinte:

    - Nome da regra: Insira **scalerule-http**
    - Digite: Selecione **Escala de HTTP**.
    - Solicitações simultâneas: Defina o valor como **1.000**.

1. Na página Adicionar regra de escala, selecione **Adicionar**.

1. Na página Escala, selecione **Salvar como uma nova revisão**.

1. Verifique se sua nova regra de escala está sendo exibida.

    Se a regra de escala não for exibida após a atualização, verifique a revisão selecionada para conferir a revisão ativa atual e ajuste a Revisão selecionada na página "Escala e réplicas", se necessário.

### Exercício 6: Configurar a integração contínua usando o Azure Pipelines

Você foi solicitado a configurar um ambiente de integração contínua para Aplicativos de Contêiner que atendam aos seguintes requisitos:

- Você precisa de uma tarefa de implantação de Aplicativos de Contêiner do Azure em seu ambiente ADO.
- O Pipeline1 precisa implantar uma imagem de contêiner do seu registro de contêiner no seu aplicativo de contêiner usando um pool de agentes auto-hospedado.
- Você deve garantir que o pipeline implante a imagem com êxito pelo menos uma vez.

Nesse exercício, implante um aplicativo de contêiner a partir de uma imagem do Registro de Contêiner do Azure para a plataforma Aplicativos de Contêiner do Azure.

Você levará cerca de 10 minutos para executar as seguintes tarefas:

- Configurar o Pipeline1 para usar o pool de agentes auto-hospedados.
- Configure o Pipeline1 com uma tarefa de implantação de Aplicativos de Contêiner do Azure.
- Executar a tarefa de implantação do Pipeline1.

#### Tarefa 1: Configurar o Pipeline1 para usar o pool de agentes auto-hospedados

Execute as etapas a seguir para configurar seus pipelines para uso do pool de agentes auto-hospedados.

1. Certifique-se de que sua organização do Azure DevOps esteja aberta em uma guia própria do navegador.

    Se necessário, abra uma nova guia do navegador, navegue até `https://dev.azure.com`e, a seguir, abra sua organização do Azure DevOps.

1. Na sua página do Azure DevOps, para abrir seu projeto do DevOps selecione **AZ2003Project**.

1. No menu à esquerda, selecione **Pipelines**.

1. Selecione **Pipeline1**e, a seguir, selecione **Editar**.

1. Para usar o pool de agentes auto-hospedados, atualize o arquivo azure-pipelines.yml conforme mostrado no exemplo a seguir:

    ```yml
    trigger:
    - main

    pool:
      name: default

    steps:

    ```

1. Selecione **Salvar**.

1. Insira uma mensagem de confirmação e selecione **Salvar**.

#### Tarefa 2: Configurar o Pipeline1 com uma tarefa de implantação de Aplicativos de Contêiner do Azure

Execute as etapas a seguir para configurar o Pipeline1 com uma tarefa de implantação dos Aplicativos de Contêiner do Azure.

1. Certifique-se de que seu Pipeline1 esteja aberto para edição.

1. No lado direito, em Tarefas, no campo Pesquisar tarefas, insira **contêiner do azure**

1. Na lista filtrada de tarefas, selecione **Implantar Aplicativos de Contêiner do Azure**

1. Nas conexões do Azure Resource Manager, selecione a Assinatura que você está usando e, em seguida, selecione **Autorizar**.

1. Na guia do portal do Microsoft Azure, abra seu recurso Aplicativo de Contêiner e, em seguida, abra a página Contêineres.

1. Copie as informações a seguir para o Bloco de Notas.

    - Nome
    - Registro
    - Imagem
    - Tag de imagem

1. Use as informações que você copiou da página de Contêineres para configurar os seguintes campos de informações de Tarefa:

    - Imagem do Docker a ser implantada: Tag Registry/Image:Image (substitua por suas informações do Bloco de Notas)
    - Nome do Aplicativo de Contêiner do Azure: Nome (substitua por suas informações do Bloco de Notas)

    Por exemplo:

    - Imagem do Docker a ser implantada: acraz2003cah12oct.azurecr.io/az2003project:latest
    - Nome do Aplicativo de Contêiner do Azure: aca-az2003

1. No campo nome do grupo dos Recursos do Azure, insira **RG1**

    > [!NOTE]
    > Se precisar verificar o nome do grupo de recursos, você poderá encontrá-lo na página Visão geral do seu recurso Aplicativo de Contêiner.

1. Na página Implantação de Aplicativos de Contêiner do Azure, selecione **Adicionar**.

    O arquivo Yaml do seu pipeline agora deve incluir as tarefas AzureContainerApps da seguinte forma:

    ```yml
    trigger:
    - main
    pool:
      name: default
    steps:
    - task: AzureContainerApps@1
      inputs:
        azureSubscription: '<Subscription>(<Subscription ID>)'
        imageToDeploy: '<Registry>/<Image>:<Image tag>' from Container App resource
        containerAppName: '<Name>' from Container App resource 
        resourceGroup: '<resource group name>'
    
    ```

    Aqui está um exemplo que mostra um trecho da configuração do YAML:

    ```yml
    trigger:
    - main
    pool:
      name: default
    steps:
    - task: AzureContainerApps@1
      inputs:
        azureSubscription: 'Visual Studio Enterprise(1111aaaa-22bb-33cc-44dd-555555eeeeee)'
        imageToDeploy: 'acraz2003cah12oct.azurecr.io/aspnetcorecontainer:latest'
        containerAppName: 'aca-az2003'
        resourceGroup: 'RG1'
    ```

1. Na sua página Pipeline1, selecione **Salvar**, insira uma mensagem de commit e, a seguir, selecione **Salvar** novamente para fazer o commit.

#### Tarefa 3: Executar a tarefa de implantação do Pipeline1

Execute as etapas a seguir para executar a tarefa de implantação do Pipeline1.

1. Certifique-se de que seu Pipeline1 esteja aberto no Azure DevOps.

1. Para executar a tarefa AzureContainerApps, selecione **Run**.

1. Na página Executar pipeline, selecione **Executar**.

    Uma página do pipeline é aberta para exibir o trabalho associado. A seção do trabalho exibe o status do trabalho, que progride de Enfileirado para Em espera.

1. Verifique se "Permissão necessária" está aparecendo em Trabalho.

    Se o trabalho exigir uma permissão para continuar, selecione **Ver** e, a seguir, selecione **Permitir** para fornecer as permissões necessárias.

1. Monitore o status da operação de execução e verifique se a execução foi bem-sucedida.

    Pode levar alguns minutos para que o trabalho na fila de espera comece a ser executado. Após um minuto ou mais, o status do trabalho deve mudar de "Em execução" para "Sucesso".

### Exercício 7: Gerenciar revisões nos Aplicativos de Contêiner do Azure

Você foi solicitado a configurar a divisão de tráfego para seus Aplicativos de Contêiner para atender aos seguintes requisitos:

- Você precisa criar uma nova revisão do aplicativo de contêiner que usa um sufixo da v2.
- Você deve garantir que 25% das solicitações para seu aplicativo sejam direcionadas para a revisão v2.
- Você precisa rotular as revisões como "atuais" e "atualizadas" e se certificar de que as solicitações para a revisão "---updated" sejam direcionadas para a revisão rotulada como v2.

Neste exercício, você implantará uma nova revisão do aplicativo de contêiner e configurará a divisão de tráfego entre duas revisões rotuladas.

Você levará cerca de 5 a 10 minutos para executar as seguintes tarefas:

- Defina o gerenciamento de revisão como múltiplo.
- Crie uma nova revisão com um sufixo v2.
- Configure rótulos nas revisões.
- Configure um percentual de tráfego nas revisões.

#### Tarefa 1: Configurar o gerenciamento de revisões como múltiplo

Execute as etapas a seguir para configurar o gerenciamento de revisões como múltiplo.

1. No portal do Azure, abra o recurso do aplicativo de contêiner.

1. No menu esquerdo, em Aplicativo, selecione **Revisões e réplicas**.

1. Na parte superior da página Revisões, selecione **Escolher modo de revisão**.

1. Para alternar do modo de revisão única para multilinha, selecione **Confirmar**.

1. Na página Revisões, aguarde até que a configuração do **Modo de revisão** seja atualizada.

    O Modo Revisão será configurado como **Múltiplo** após a atualização ser concluída.

#### Tarefa 2: Criar uma nova revisão com um sufixo v2

Execute as etapas a seguir para criar uma nova revisão com um sufixo v2.

1. No portal do Azure, verifique se você tem a página Revisões do recurso de aplicativo de contêiner aberta.

1. Na parte superior da página, selecione **+ Criar nova revisão**.

1. Na página Criar e implantar nova revisão, conclua as seguintes etapas:

    - Nome/sufixo: Insira **v2**
    - Em Imagem de contêiner, selecione sua imagem de contêiner. Por exemplo, aca-az2003.

1. Selecione **Criar**.

1. Aguarde a conclusão da implantação.

#### Tarefa 3: Configurar rótulos nas revisões

A Entrada precisa estar habilitada antes de você poder configurar rótulos de revisão ou divisões de tráfego.

Execute as etapas a seguir para configurar rótulos nas revisões.

1. No menu esquerdo, em Configurações, selecione **Entrada**.

1. Se a entrada não estiver habilitada, selecione **Habilitado**.

1. Na página Entrada, especifique as seguintes informações:

    - Tráfego de entrada: selecione **Aceitar tráfego de qualquer lugar**.

    - Tipo de entrada: selecione **HTTP**.

    - Modo de certificado do cliente: certifique-se de que **Ignorar** esteja selecionado.

    - Transporte: certifique-se de que **Auto** esteja selecionado.

    - Conexões inseguras: certifique-se de que Permitido **NÃO** esteja selecionado.

    - Porta de destino: insira **5000**

    - Modo de restrições de segurança de IP: certifique-se de que **Permitir todo o tráfego** esteja selecionado.

1. Na parte inferior da página entrada, selecione **Salvar** e aguarde a conclusão da atualização.

1. No menu esquerdo, em Revisões, selecione **Revisões e réplicas**.

1. Para a revisão v2, em Rótulo, insira **atualizado**

1. Para a outra revisão, insira **atual**

1. Na parte superior da página, selecione **Salvar**.

1. Aguarde até que a configuração de Entrada seja atualizada.

#### Tarefa 4: Configurar um percentual de tráfego nas revisões

Execute as etapas a seguir para configurar um percentual de tráfego nas revisões.

1. Certifique-se de ter a página Revisões aberta.

1. Para a revisão v2, em Tráfego, insira **25** como o percentual.

1. Para a outra revisão, em Tráfego, insira **75** como o percentual.

1. Na parte superior da página, selecione **Salvar**.
