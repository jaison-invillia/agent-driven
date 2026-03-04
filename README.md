# 📚 Educational Portal

Portal educacional para disponibilização de cursos online baseados em
videoaulas hospedadas no YouTube.

O sistema permitirá que alunos se cadastrem, acessem cursos organizados
em trilhas de aprendizado e obtenham certificados após completar todas
as aulas de um curso.

------------------------------------------------------------------------

# 🎯 Objetivo do Projeto

Criar uma plataforma educacional onde:

-   Alunos possam **se cadastrar e acessar cursos**
-   Cursos sejam compostos por **listas de aulas em vídeo**
-   As aulas sejam **incorporadas do YouTube**
-   O sistema **controle o progresso do aluno**
-   Após completar todas as aulas, o aluno **receba um certificado
    digital**

------------------------------------------------------------------------

# 🧩 Funcionalidades Principais

## 👤 Cadastro de Alunos

O sistema permitirá que usuários criem uma conta para acessar os cursos.

Funcionalidades previstas:

-   Cadastro de aluno
-   Login
-   Autenticação de sessão
-   Perfil do aluno

------------------------------------------------------------------------

## 📚 Catálogo de Cursos

Cursos disponíveis serão listados para alunos autenticados.

Cada curso conterá:

-   Nome
-   Descrição
-   Lista de aulas
-   Progresso do aluno

Exemplo de estrutura de curso:

    HTML Básico
    ├── Aula 1: Introdução ao HTML
    ├── Aula 2: Estrutura de uma página
    ├── Aula 3: Tags principais
    └── Aula 4: Formulários

------------------------------------------------------------------------

## 🎥 Videoaulas

As aulas serão hospedadas no **YouTube** e exibidas dentro da
plataforma.

Cada aula possuirá:

-   Título
-   Descrição
-   URL do vídeo no YouTube
-   Ordem dentro do curso

------------------------------------------------------------------------

## 📊 Controle de Progresso

O sistema deverá registrar:

-   Quais aulas o aluno assistiu
-   Qual o progresso no curso

Somente após completar **100% das aulas**, o aluno poderá receber o
certificado.

------------------------------------------------------------------------

## 🏆 Emissão de Certificado

Após completar todas as aulas de um curso:

-   O sistema gera um **certificado digital**
-   O certificado conterá:
    -   Nome do aluno
    -   Nome do curso
    -   Data de conclusão
    -   Identificador único

------------------------------------------------------------------------

# 🧱 Arquitetura (Visão Geral)

O projeto será dividido em duas aplicações principais:

    educational-portal
    │
    ├── frontend
    │   └── Next.js
    │
    ├── backend
    │   └── Node.js API
    │
    └── database
        └── MySQL

------------------------------------------------------------------------

# ⚙️ Stack Tecnológica

## 🎨 Design

Ferramenta utilizada:

**Figma**

Responsável por:

-   Layouts
-   Componentes visuais
-   Fluxos de navegação
-   Design system

------------------------------------------------------------------------

## 💻 Front-end

Tecnologia principal:

**Next.js**

Características:

-   Server Side Rendering (SSR)
-   Rotas baseadas em páginas
-   Integração com API backend
-   Renderização de páginas de cursos

------------------------------------------------------------------------

## 🔧 Back-end

Tecnologia principal:

**Node.js**

Responsabilidades:

-   API de autenticação
-   Gestão de alunos
-   Gestão de cursos
-   Controle de progresso
-   Emissão de certificados

------------------------------------------------------------------------

## 🗄 Banco de Dados

Banco escolhido:

**MySQL 8**

Principais entidades previstas:

-   Users
-   Courses
-   Lessons
-   CourseProgress
-   Certificates

------------------------------------------------------------------------

## 📈 Monitoramento

Ferramenta:

**New Relic**

Monitoramento de:

-   Performance da API
-   Erros da aplicação
-   Métricas de uso

------------------------------------------------------------------------

## 🪵 Logs

Ferramenta ainda **a definir**.

Possíveis opções:

-   Winston
-   Pino
-   OpenTelemetry
-   ELK Stack

------------------------------------------------------------------------

# 🤖 Desenvolvimento Assistido por IA

Este projeto será desenvolvido utilizando **agentes especializados**
para acelerar a construção da aplicação.

Agentes previstos no fluxo de desenvolvimento:

-   Orchestrator Agent
-   Product Agent
-   Architecture Agent
-   Database Agent
-   Backend Agent
-   Frontend Agent
-   Code Review Agent
-   Test Agent
-   DevOps Agent
-   Observability Agent
-   Security Agent

------------------------------------------------------------------------

# 📅 Roadmap Inicial

### Fase 1 --- Fundação

-   Definição da arquitetura
-   Modelagem do banco de dados
-   Estrutura do repositório

### Fase 2 --- Backend

-   API de autenticação
-   API de cursos
-   API de progresso

### Fase 3 --- Frontend

-   Tela de login
-   Listagem de cursos
-   Player de aulas

### Fase 4 --- Certificados

-   Geração de certificado
-   Download do certificado

### Fase 5 --- Observabilidade

-   Monitoramento
-   Logs
-   Métricas

------------------------------------------------------------------------

# 📄 Licença

A definir.
