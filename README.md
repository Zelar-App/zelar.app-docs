# Zelar.app — Documentation

> "Conectando pessoas, dados e cidades."

Repositório central de documentação da plataforma **Zelar.app**, reunindo especificações, decisões técnicas, arquitetura, regras de negócio e demais informações necessárias para o desenvolvimento e manutenção do projeto.

## O Projeto

O Zelar.app é uma plataforma que conecta cidadãos e responsáveis pela infraestrutura urbana, permitindo o registro, acompanhamento e gerenciamento de ocorrências como:

* Buracos em vias;
* Problemas de iluminação;
* Acúmulo de lixo;
* Alagamentos;
* Problemas em calçadas;
* Sinalização danificada;
* Ocorrências ambientais.

O projeto é dividido em três repositórios principais:

* **zelar.app-backend** — API, regras de negócio, autenticação e acesso aos dados;
* **zelar.app-frontend** — Interface da aplicação e experiência do usuário;
* **zelar.app-docs** — Documentação e especificações do projeto.

## Objetivo da Documentação

Este repositório tem como objetivo centralizar informações que precisam ser compartilhadas entre as diferentes partes do projeto.

A documentação busca facilitar:

* Entendimento do projeto;
* Comunicação entre os integrantes da equipe;
* Definição e registro de requisitos;
* Padronização do desenvolvimento;
* Manutenção do sistema;
* Onboarding de novos integrantes;
* Registro das decisões técnicas.

## Estrutura da Documentação

A organização dos documentos seguirá uma estrutura que poderá evoluir conforme o projeto crescer.

```text
docs/
│
├── requirements/     → Requisitos funcionais e não funcionais
├── architecture/     → Arquitetura e decisões estruturais
├── api/              → Documentação da API
├── database/         → Modelagem e documentação do banco de dados
├── business-rules/   → Regras de negócio
├── ux-ui/            → Fluxos, telas e especificações de interface
└── decisions/        → Decisões técnicas e arquiteturais
```

## Documentação Técnica

A documentação técnica deverá acompanhar a evolução do sistema e poderá incluir:

* Diagramas de arquitetura;
* Diagramas de banco de dados;
* Diagramas de fluxo;
* Contratos e endpoints da API;
* Modelagem de dados;
* Regras de negócio;
* Fluxos de usuário;
* Decisões arquiteturais;
* Padrões e convenções adotados no projeto.

## Qualidade da Documentação

A documentação busca seguir algumas práticas para manter as informações organizadas e confiáveis:

* Documentação versionada com Git;
* Padronização dos documentos;
* Organização por domínio;
* Registro das decisões importantes;
* Atualização conforme o sistema evolui;
* Evitar duplicação de informações entre documentos.

## Relação com os Outros Repositórios

```text
zelar.app-backend
        │
        │ API / regras de negócio
        ▼
zelar.app-frontend
        │
        │ Interface / experiência do usuário
        │
        └──────────────┐
                       ▼
                zelar.app-docs
                       │
                       └── Documentação compartilhada
```

O `zelar.app-docs` não contém a implementação do backend ou frontend. Ele funciona como **fonte central de documentação e referência para o desenvolvimento do projeto**.

## Estratégia de Branches

```text
main      → documentação estável
develop   → integração de alterações

feature/* → novas documentações ou alterações estruturais
fix/*     → correções de documentação
```

## Contribuição

Alterações na documentação devem seguir o mesmo processo de organização utilizado no desenvolvimento do projeto:

* Controle de versão com Git;
* Desenvolvimento em branches;
* Pull Requests;
* Revisão das alterações;
* Padronização dos documentos;
* Manutenção contínua da documentação.

## Status

Em desenvolvimento.
