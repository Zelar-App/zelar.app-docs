# Modelo conceitual do domínio

Backend `e0d47f2`, em 27/10/2026. Este modelo enfatiza entidades e invariantes; controllers, DTOs e adaptadores estão na [arquitetura](../architecture/ARQUITETURA.md). Não representa assinatura completa de todas as classes Java.

```mermaid
classDiagram
    class Usuario {
        UUID id
        String nome
        String email
        String senhaHash
        String telefone
        PerfilUsuario perfil
        LocalDateTime criadoEm
    }
    class Ocorrencia {
        UUID id
        CategoriaOcorrencia categoria
        String descricao
        StatusOcorrencia status
        PrioridadeOcorrencia prioridade
        LocalDateTime dataCriacao
        LocalDateTime dataAtualizacao
        atualizarStatus(novoStatus)
        atualizarPrioridade(novaPrioridade)
    }
    class Localizacao {
        Double latitude
        Double longitude
        String enderecoAproximado
    }
    class Anexo {
        UUID id
        String bucket
        String chaveObjeto
        String tipoConteudo
        Long tamanhoBytes
        Integer largura
        Integer altura
        EstadoUpload estadoUpload
        LocalDateTime dataCriacao
        LocalDateTime dataAtualizacao
    }
    Usuario "1" -- "0..*" Ocorrencia : registra
    Ocorrencia "1" *-- "1" Localizacao : embute
    Ocorrencia "1" -- "0..1" Anexo : vincula
```

Enums: perfil `CIDADAO/ADMINISTRADOR`; categoria `BURACO/ILUMINACAO/LIXO/ALAGAMENTO/CALCADA/SINALIZACAO/AMBIENTAL`; status `ENVIADA/EM_ANALISE/RESOLVIDA`; prioridade `BAIXA/MEDIA/ALTA`; estado do upload `PENDENTE/DISPONIVEL/LIMPEZA_PENDENTE`.

Autorização administrativa é regra de serviço, não uma associação exclusiva de tabelas. Prioridade pode ser nula na criação. `Localizacao` não tem tabela própria. Uma fotografia pendente já ocupa o vínculo único; somente disponível é apresentada ao usuário. O banco mapeia `perfil` para `papel` e `criadoEm` para `data_cadastro`.

A cardinalidade de múltiplos anexos da documentação anterior foi substituída pelo contrato do MVP: uma fotografia opcional, conforme RF03 e DB-09/BE-13. [Decisões](../decisions/DECISOES-MVP.md).
