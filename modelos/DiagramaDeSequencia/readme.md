# Diagramas de Sequencia

### CSU01 - Buscar Jogadores

@startuml
title CSU01 - Buscar Jogadores

participant Jogador as J
participant "Interface de Busca" as IB
participant "Sistema de Busca" as SB
participant "Banco de Dados de Jogadores" as BD
participant "Sistema de Avaliação/Bloqueio" as SAB

J->IB: 1. Acessa funcionalidade de busca
activate IB
IB->J: 2. Exibe interface de busca com filtros
J->IB: 3. Informa filtros (jogo, estilo, plataforma, região)
deactivate IB

activate SB
IB->SB: 4. Envia filtros para processamento
activate BD
SB->BD: 5. Solicita dados de jogadores compatíveis (baseado em horários, estilos, plataformas)
BD-->SB: 6. Retorna jogadores com dados básicos
deactivate BD

activate SAB
SB->SAB: 7. Consulta status (avaliação, bloqueio) dos jogadores
SAB-->SB: 8. Retorna status e avaliações dos jogadores
deactivate SAB

SB->SB: 9. Aplica regras de matchmaking (compatibilidade, avaliação, bloqueio)
SB->SB: 10. Ordena jogadores por prioridade

alt Nenhum jogador compatível
    SB-->IB: 11.1. Retorna "Nenhum jogador encontrado com esses critérios."
    IB->J: 11.2. Exibe mensagem de "Nenhum jogador encontrado..."
else Jogadores compatíveis encontrados
    SB-->IB: 11.3. Retorna lista ordenada de jogadores compatíveis
    IB->J: 11.4. Exibe lista de jogadores compatíveis
end

deactivate SB
@enduml

### CSU04 - Bloquear Jogador

@startuml
title CSU04 - Bloquear Jogador

participant "Jogador A (Bloqueador)" as JA
participant "Interface de Perfil/Opções" as IPO
participant "Sistema de Bloqueio" as SB
participant "Banco de Dados de Bloqueios" as BDB

autonumber

JA->IPO: Acessa perfil de "Jogador B" ou opções de interação
activate IPO
IPO->JA: Exibe perfil/opções (inclui "Bloquear jogador")
JA->IPO: Seleciona "Bloquear jogador"

IPO->JA: Solicita confirmação de bloqueio (RNF05)
activate JA
JA->IPO: Confirma bloqueio
deactivate JA

IPO->SB: Envia solicitação de bloqueio (Jogador A, Jogador B)
deactivate IPO
activate SB

SB->BDB: Registra bloqueio (Jogador A bloqueia Jogador B)
activate BDB
BDB-->SB: Bloqueio registrado
deactivate BDB

SB->SB: Atualiza cache de busca/contatos (para RN03)
SB-->JA: Confirmação: "Jogador B" bloqueado.
deactivate SB

@enduml

### CSU05 - Cadastro de Horários

@startuml
title CSU05 - Cadastro de Horários

participant Jogador as J
participant "Interface de Horários" as IH
participant "Sistema de Perfil/Configurações" as SPC
participant "Sistema de Sugestão de Horários" as SSH
participant "Banco de Dados de Horários/Perfil" as BDHP
participant "Banco de Dados de Logs de Login" as BDLL

autonumber

J->IH: Acessa funcionalidade "Agenda de Disponibilidade"
activate IH
IH->J: Exibe opções: "Definir Manualmente" ou "Sugerir Automaticamente"

alt Opção: Definir Manualmente
    J->IH: Escolhe "Definir Manualmente"
    IH->J: Exibe interface para seleção manual de horários (RNF06)
    activate J
    J->IH: Preenche e envia horários desejados
    deactivate J
    IH->SPC: Envia horários para salvamento
else Opção: Sugerir Automaticamente
    J->IH: Escolhe "Sugerir Automaticamente"
    IH->SPC: Solicita sugestão de horários
    deactivate IH
    activate SPC
    SPC->SSH: Requisita análise de logs para sugestão (RN04)
    activate SSH
    SSH->BDLL: Consulta logs de login do jogador
    activate BDLL
    BDLL-->SSH: Retorna histórico de logs de login
    deactivate BDLL
    SSH-->SPC: Retorna horários sugeridos
    deactivate SSH
    SPC-->IH: Envia horários sugeridos
    deactivate SPC
    activate IH
    IH->J: Exibe horários sugeridos para aprovação (RNF06)
    activate J
    J->IH: Aprova/Ajusta e envia horários sugeridos
    deactivate J
    IH->SPC: Envia horários aprovados/ajustados para salvamento
end

activate SPC
SPC->BDHP: Salva/Atualiza horários do jogador
activate BDHP
BDHP-->SPC: Horários salvos com sucesso
deactivate BDHP

SPC-->IH: Confirmação de salvamento
deactivate SPC
IH->J: Exibe confirmação de horários salvos.
deactivate IH

@enduml

### CSU03 - Avaliar Jogador

@startuml
title CSU03 - Avaliar Jogador

participant Jogador as J
participant "Sistema de Partida/Notificação" as SPN
participant "Interface de Avaliação" as IA
participant "Sistema de Avaliação" as SA
participant "Banco de Dados de Avaliações" as BDA
participant "Banco de Dados de Jogadores/Ranking" as BDJR

autonumber

SPN->J: Partida concluída. Deseja avaliar seu oponente?
activate J
J->SPN: Sim, desejo avaliar
deactivate J

activate IA
SPN->IA: Solicita exibição da interface de avaliação (para oponente X)
IA->J: Exibe interface de avaliação (nota, comentário)

activate J
J->IA: Fornece nota e/ou comentário
deactivate J

IA->SA: Envia avaliação (jogador avaliado, nota, comentário)
deactivate IA
activate SA

SA->BDA: Registra nova avaliação
activate BDA
BDA-->SA: Avaliação registrada com sucesso
deactivate BDA

SA->BDJR: Solicita atualização do ranking/média de avaliação do jogador avaliado
activate BDJR
BDJR-->SA: Ranking/média de avaliação atualizado(a)
deactivate BDJR

SA-->J: Confirmação: Avaliação registrada com sucesso.
deactivate SA

@enduml
