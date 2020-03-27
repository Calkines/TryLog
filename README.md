# TryLog

O [TryLog é uma Central de Erros](https://is.gd/y85CbV), responsável por centralizar os registros de erros de várias aplicações. Ele têm as principais funcionalidades das Centrais de Erro presentes no mercado, além de possuir características pertinentes ao Banco Itaú, conforme nossa interpretação do desafio proposto pela Codenation. 

A inspiração para o desenvolvimento do TryLog foi atender ambientes complexos (vários serviços, diversas aplicações com diferentes camadas). Tais ambientes são comuns em grandes empresas como o Banco Itaú, por isso, imaginamos possíveis cenários em que o TryLog poderia ser utilizado, como por exemplo: identificar falhas, intermitências e gargalos. Estas informações contribuiriam na evolução das aplicações, tanto para desenvolvimento como no gerenciamento das mesmas.


### Propósito do projeto

Este projeto deve servir para capturar erros vindos de qualquer aplicação. Para permitir que isso aconteça,  disponibilizaremos endpoints REST para tratar as mensagens de erro capturadas em diversas plataformas, como: mobile, site e o próprio backend desta aplicão. Tentaremos seguir o mais próximo da teoria REST apresentada por Roy Fielding.

#### Sobre conceitos descritos

Como se trata de um projeto vinculado a Codenation/Itaú e possui um intuito de aprendizado/demonstração de conhecimento, nos propomos a descrever com detalhes os conceitos que vamos utilizar, mostrando como os entendemos.

### Pilares do TryLog

Etapa     |  Descrição |
--------- | -----------
Monitoramento | Armazenar e acompanhar, em tempo real, eventos que são gerados por todas as aplicações cadastradas
Triagem | Separar os eventos recebidos pelo Monitoramento de acordo com o tipo de ambiente, severidade e prioridade de cada um deles
Diagnóstico | Identificar possíveis falhas de desenvolvimento ou intermitências das aplicações conforme dados catalogados pela Triagem
Ação | Agir de acordo com a necessidade identificada pela etapa de Diagnóstico, acionando os recursos disponíveis para a solução dos problemas, além de definir e atualizar o status de cada evento
Manutenção | Propor resoluções para inconsistências e utlizar os dados de todas as etapas anteriores para sugerir melhorias e manutenções preventivas

#### Arquitetura do sistema

Decidimos separar as camadas de projeto, tendo como orientação o modelo Clean Architecture, de Bob Martin, mas usado aqui na visão de Steve Smith. Info: [Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures).

Através desta modelo arquitetural, esperamos melhorar a forma como o projeto é separado e compreendido. Tendo também vantagens como uso de injeção de dependência, fazendo a inverção do controle e viabilizando testes automatizados (unitários a princípio).

Também nos serviu de base o material apresentado no livro Asp.NET Core Architecture e-book, disponível em [sítio Microsoft](https://dotnet.microsoft.com/download/e-book/aspnet/pdf).

##### Camada de Infraestrutura

São itens desta camada, aqueles relativos à:

* Conexão com banco de dados
* Tipos representando o banco de dados
* Implementações de acesso a dados (das Interfaces definidas no Core)
* Serviços específicos de infraestrutura

##### Camada de Webapi

Esta camada será responsável por receber as requisições dos clientes e endereçá-las.

Vamos tratar aqui:

* Autenticação e autorização
* Rotas
* Bindings
* Injeção de dependência
* Ativação de serviços que afetam o pipeline HTTP


##### Camada Core

Esta camada é responsável por armazenar as abstrações do nosso universo. Por exemplo, o que é um log de erro será representado aqui por uma classe.

Nesta camada você vai encontrar os seguintes itens:

* Entidades
* Interfaces
* Servicos
* DTOs


#### Camada UseCases

Embora não esteja descrita nos materiais de arquitetura mencionados, achamos por bem adicionar uma camada para isolar as funcionalidades do sistema. Nela você vai encontrar:

* Funcionalidades descritas em casos de uso

#### MessageBroker

Vamos utilizar o conceito de MessageBroker para dar confiabilidade na entrega e gravação dos logs, fazendo com que nossa API apenas faça uma publicação do log em sistema de fila. Este, por sua vez, será o responsável por acionar a gravação do banco de dados.

Esperamos assim, resolver possíveis problemas com não gravação de log, ou incosistência e perda de logs.

#### Tests

Por fim uma camada para separar os testes será utilizada.

## Criando as camadas

Camada | Comando |
------ | --------
Core | dotnet new classlib -n TryLog.Core -o ./src/Core
Infraestructure | dotnet new classlib -n TryLog.Infraestructure -o ./src/Infraestructure
UseCase | dotnet new classlib -n TryLog.UseCase -o ./src/UseCase
WebApi | dotnet new webapi -n TryLog.WebApi -o ./src/WebApi
MessageBroker | dotnet new console -n TryLog.MessageBroker -o ./src/MessageBroker
Sentinela | dotnet new xunit -n TryLog.Sentinela -o ./tests/Sentinela