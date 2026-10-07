# 🗄️ Modelo de Dados Relacional (DER / Lógico) - MVP Zelar.App



Este diretório contém a modelagem lógica do banco de dados relacional (PostgreSQL) desenvolvida para o MVP do **Zelar.App**, gerada a partir da especificação de requisitos e do diagrama de classes inicial.



---



## 📐 Diagrama do Banco de Dados

<img width="1461" height="504" alt="Untitled" src="https://github.com/user-attachments/assets/9efc6218-b98d-418f-a82e-05853f704fce" />

> **Nota:** O diagrama acima foi construído utilizando a ferramenta [dbdiagram.io](https://dbdiagram.io).
---

## 📋 Mapeamento de Tabelas e Entidades



O modelo relacional traduz as entidades de domínio em 3 tabelas principais, aplicando chaves primárias do tipo `UUID`, restrições de integridade e tipos genéricos para os Enums de negócio.



### 1. Tabela `usuario`

Armazena os dados cadastrais de cidadãos e administradores do sistema.

| Coluna | Tipo | Restrições | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | `UUID` | `PK`, `NOT NULL` | Identificador único (UUID) |
| `nome` | `VARCHAR` | `NOT NULL` | Nome completo do usuário |
| `email` | `VARCHAR` | `NOT NULL`, `UNIQUE` | E-mail para autenticação |
| `senha_hash` | `VARCHAR` | `NOT NULL` | Senha criptografada (BCrypt) |
| `telefone` | `VARCHAR` | *Nullable* | Telefone para contato |
| `papel` | `papel_usuario` | `NOT NULL` | Nível de acesso (`CIDADAO`, `ADMINISTRADOR`) |
| `data_cadastro` | `TIMESTAMP` | `NOT NULL` | Data/hora de registro |

### 2. Tabela `ocorrencia`

Registra os problemas urbanos relatados pelos cidadãos.



| Coluna | Tipo | Restrições | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | `UUID` | `PK`, `NOT NULL` | Identificador único (UUID) |
| `usuario_id` | `UUID` | `FK`, `NOT NULL` | Referência ao usuário criador (`usuario.id`) |
| `categoria` | `categoria_ocorrencia` | `NOT NULL` | Classificação do problema |
| `descricao` | `TEXT` | `NOT NULL` | Detalhamento da ocorrência |
| `latitude` | `DOUBLE` | `NOT NULL` | Coordenada geográfica (Latitude) |
| `longitude` | `DOUBLE` | `NOT NULL` | Coordenada geográfica (Longitude) |
| `endereco_aproximado` | `VARCHAR` | *Nullable* | Endereço descritivo opcional |
| `status` | `status_ocorrencia` | `NOT NULL` | Estado (`ENVIADA`, `EM_ANALISE`, `RESOLVIDA`) |
| `prioridade` | `prioridade_ocorrencia`| *Nullable* | Prioridade definida pelo admin (`BAIXA`, `MEDIA`, `ALTA`) |
| `data_criacao` | `TIMESTAMP` | `NOT NULL` | Data/hora de envio da ocorrência |
| `data_atualizacao` | `TIMESTAMP` | *Nullable* | Data/hora do último ajuste de status |



### 3. Tabela `anexo`

Gerencia arquivos de imagens vinculados às ocorrências para validação e auditoria de segurança (RNF-S04).



| Coluna | Tipo | Restrições | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | `UUID` | `PK`, `NOT NULL` | Identificador único (UUID) |
| `ocorrencia_id` | `UUID` | `FK`, `NOT NULL` | Referência à ocorrência (`ocorrencia.id`) |
| `nome_arquivo` | `VARCHAR` | `NOT NULL` | Nome original do arquivo |
| `tipo_mime` | `VARCHAR` | `NOT NULL` | Tipo MIME validado |
| `tamanho_bytes` | `BIGINT` | `NOT NULL` | Tamanho do arquivo em bytes |
| `caminho_armazenamento`| `VARCHAR` | `NOT NULL` | Caminho de armazenamento seguro |
---



## 🔄 Tipos Customizados (Enums)



**`papel_usuario`**: `CIDADAO`, `ADMINISTRADOR`

**`categoria_ocorrencia`**: `BURACO`, `ILUMINACAO`, `LIXO`, `ALAGAMENTO`, `CALCADA`, `SINALIZACAO`, `AMBIENTAL`

**`status_ocorrencia`**: `ENVIADA`, `EM_ANALISE`, `RESOLVIDA`

**`prioridade_ocorrencia`**: `BAIXA`, `MEDIA`, `ALTA`

---

## 🔗 Relacionamentos e Integridade

**`usuario` 1 : N `ocorrencia`**
