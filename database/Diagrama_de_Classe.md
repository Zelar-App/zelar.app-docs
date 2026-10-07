# Diagrama de Classe - Sistema de Registro de Ocorrências Urbanas

Diagrama de classe elaborado a partir do levantamento de requisitos (RF01–RF06 e RNFs), cobrindo cadastro/autenticação de usuários, registro e consulta de ocorrências, gestão de status pelo administrador e painel administrativo.

```mermaid
classDiagram
    direction LR

    class Usuario {
        -UUID id
        -String nome
        -String email
        -String senhaHash
        -String telefone
        -Papel papel
        -LocalDateTime dataCadastro
        +cadastrar()
        +autenticar(email, senha) String
        +registrarOcorrencia(dto) Ocorrencia
    }

    class Papel {
        <<enumeration>>
        CIDADAO
        ADMINISTRADOR
    }

    class Ocorrencia {
        -UUID id
        -Categoria categoria
        -String descricao
        -Localizacao localizacao
        -String fotoUrl
        -StatusOcorrencia status
        -Prioridade prioridade
        -LocalDateTime dataCriacao
        -LocalDateTime dataAtualizacao
        +criar()
        +consultar()
        +alterarStatus(novoStatus) void
        +anexarFoto(arquivo) void
    }

    class Categoria {
        <<enumeration>>
        BURACO
        SINALIZACAO_DANIFICADA
        LIXO
        OUTROS
    }

    class StatusOcorrencia {
        <<enumeration>>
        ENVIADA
        EM_ANALISE
        RESOLVIDA
    }

    class Prioridade {
        <<enumeration>>
        BAIXA
        MEDIA
        ALTA
    }

    class Localizacao {
        -Double latitude
        -Double longitude
        -String enderecoAproximado
    }

    class Anexo {
        -UUID id
        -String nomeArquivo
        -String tipoMime
        -Long tamanhoBytes
        -String caminhoArmazenamento
        +validarExtensao() boolean
        +validarMimeReal() boolean
        +validarTamanho() boolean
    }

    class DashboardAdministrativo {
        +listarOcorrencias() List~Ocorrencia~
        +filtrarPorStatus(status) List~Ocorrencia~
        +filtrarPorPrioridade(prioridade) List~Ocorrencia~
        +obterIndicadores() IndicadoresSistema
    }

    class IndicadoresSistema {
        -int totalOcorrencias
        -int totalEnviadas
        -int totalEmAnalise
        -int totalResolvidas
    }

    class AutenticacaoService {
        +gerarTokenJWT(usuario) String
        +validarToken(token) boolean
        +criptografarSenha(senha) String
    }

    Usuario "1" --> "0..*" Ocorrencia : registra
    Usuario --> Papel
    Ocorrencia --> Categoria
    Ocorrencia --> StatusOcorrencia
    Ocorrencia --> Prioridade
    Ocorrencia "1" --> "1" Localizacao : possui
    Ocorrencia "1" --> "0..1" Anexo : contém
    Usuario "1" --> "0..*" Ocorrencia : altera status (ADMINISTRADOR)
    DashboardAdministrativo "1" --> "0..*" Ocorrencia : monitora
    DashboardAdministrativo --> IndicadoresSistema
    Usuario --> AutenticacaoService : usa
```

## Observações de modelagem

- **Usuario/Papel**: um único modelo de usuário com o atributo `papel` (CIDADAO ou ADMINISTRADOR) evita duplicação de tabelas e simplifica a autenticação via JWT (RNF-S01), já que a checagem de permissão (ex.: alterar status) é feita pelo papel.
- **Ocorrencia**: concentra os dados do RF03 (categoria, descrição, localização, foto opcional) e do RF05 (status controlado pelo administrador).
- **Anexo**: isolado da Ocorrencia para acomodar as regras do RNF-S04 (validação de extensão, MIME real, tamanho e armazenamento sem execução de scripts).
- **Localizacao**: modelada como classe embutida (value object) para facilitar futura integração com módulos de mapas (RNF-D02).
- **DashboardAdministrativo/IndicadoresSistema**: suportam o RF06, agregando dados sem duplicar informação já presente em Ocorrencia.
- **AutenticacaoService**: separa a lógica de segurança (hash de senha, emissão/validação de JWT) das entidades de domínio, alinhado ao RNF-S01 e RNF-S02.

O diagrama acima está em sintaxe Mermaid, então é renderizado automaticamente em visualizadores compatíveis (GitHub, VS Code, Notion, etc.) ou pode ser colado em ferramentas como o Mermaid Live Editor.
