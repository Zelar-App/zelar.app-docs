# Regras de negócio do MVP

Versão: backend `e0d47f2`, 27/10/2026. Complemento do [contrato HTTP](../api/README.md).

| ID | Regra implementada |
| --- | --- |
| RN-01 | Cadastro público sempre cria `CIDADAO`; email único, senha BCrypt. Administrador é provisionado por configuração controlada, sem credencial padrão. |
| RN-02 | Identidade vem do JWT validado. Conta removida não mantém acesso; o perfil atual no banco prevalece nas verificações de acesso. |
| RN-03 | Criação exige categoria, descrição e coordenadas válidas. Autor vem da sessão; status inicial `ENVIADA`, prioridade nula e foto opcional. |
| RN-04 | Cidadão consulta apenas suas ocorrências; administrador consulta todas. ID alheio ou inexistente retorna o mesmo 404. Não há consulta pública. |
| RN-05 | Somente administrador altera status: `ENVIADA → EM_ANALISE → RESOLVIDA`, sem salto, retorno, repetição ou reabertura; fora do fluxo retorna 409. |
| RN-06 | Somente administrador atribui/troca `BAIXA`, `MEDIA` ou `ALTA`, em qualquer status. Não aceita nulo; repetir valor não muda a data. |
| RN-07 | Alterações preservam outros atributos e atualizam `dataAtualizacao` quando efetivas. Bloqueio serializa alterações concorrentes; PATCHs separados não são atômicos entre si. |
| RN-08 | Indicadores são contagens globais por status para administradores; independem dos filtros da lista. |
| RN-09 | Zero ou uma fotografia por ocorrência. Só o autor envia; autor e administrador consultam. Sem substituição/exclusão por endpoint. |
| RN-10 | JPEG/PNG pelo conteúdo, até 5 MiB, 8192 por lado e 16 MP; regravação remove metadados. Sem persistir nome original ou URL pública. |
| RN-11 | Bucket privado; somente `DISPONIVEL` é publicado. Reserva anterior ao upload e recuperação eventual, sem transação distribuída. |

## Autorizações de fotografia

Cidadão acessando ocorrência alheia recebe 404 antes do Storage. Administrador pode consultar foto disponível de outro autor, mas recebe 403 ao tentar enviar em seu nome. Se a conta administrativa for autora, a regra de autoria permite envio. Foto ausente/não disponível retorna 404 para quem pode consultar a ocorrência.

## Recuperação de envio

Estados: `PENDENTE` (reserva confirmada), `DISPONIVEL` (upload confirmado), `LIMPEZA_PENDENTE` (compensação necessária). Reserva em transação independente, HTTP fora da transação e confirmação em nova transação.

Com fotos habilitadas, job a cada minuto processa até 20 registros pendentes/interrompidos cuja última atualização ocorreu há pelo menos 10 minutos. Reivindica com bloqueio, confirma intenção de limpeza, exclui o objeto e depois remove o metadado. Falhas preservam vínculo para tentativa posterior; fotos disponíveis ficam fora da limpeza. Alterações manuais de banco/bucket não recebem garantias automáticas.

## Limites e decisões pendentes

Não há exclusão de conta, retenção automática, anonimização, recuperação de senha, refresh token, SLA operacional ou reabertura implementados. Não prometer esses recursos. A [decisão de dados e retenção](../decisions/DECISOES-MVP.md#dados-pessoais-e-retenção) registra medidas existentes e assuntos a acordar.
