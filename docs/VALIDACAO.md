# Validação RC11.20 — 15/11/2026

App 2.3.0.20 recompilado dos fontes locais atuais com Meu cockpit e mostradores 113H opcionais. Gerenciador 2.3.0.17, DLL x64 2.3.0.12 e runtime 10.0.14 reaproveitados e conferidos. Firmware e protocolos preservados.

| Verificação | Grupos aprovados | Falhas finais |
|---|---:|---:|
| HUD, aparência, simulação, perfis e 113H | 202 | 0 |
| STOP e isolamento | 15 | 0 |
| Temas e persistência | 12 | 0 |
| Régua RPM e temas | 19 | 0 |
| G29/G923 com transporte substituído | 15 | 0 |
| Serial com transporte substituído | 10 | 0 |
| Memória compartilhada | 4 | 0 |
| Instância única entre processos | 1 | 0 |
| Firmware em harness de computador, sem gravação | 19 | 0 |
| Instalador em fixture isolada | 7 | 0 |
| Carregamento e exports da DLL x64, sem iniciar SDK | 1 | 0 |
| **Total, sem contar repetições** | **305** | **0** |

Os seis grupos 113H foram repetidos depois dos últimos ajustes de posição do valor, fontes e seleção visual; passaram novamente. Aplicativo e suítes afetadas compilaram sem avisos/erros. O manifesto SHA256 dos fontes compilados foi comparado ao workspace após o build; seu hash está em BUILD_METADATA.json. A referência privada anterior é origem histórica, não afirma que os binários novos são aquele snapshot.

## Perfil e gráficos

Sem configuração nem backup, a primeira execução carrega Meu cockpit. O perfil externo corresponde ao JSON incorporado ao binário. Conexão automática; motores iniciam no Nível 0. Testes e simulação exigem ação explícita. Preferências existentes, campos futuros e backup são preservados. Testados: primeira gravação/reinício, configuração existente, principal ausente com backup e arquivos corrompidos. O handshake com transporte substituído envia Nível 0 antes de liberar operação.

113H: psi para bar, limites numéricos, ausentes/NaN/infinito/negativos, zero distinto de ausência, ponteiros, clipping, áreas pequenas, alinhamentos, DPI 96/144/192, persistência individual e prioridade posterior de Somente texto. Imagens simuladas; escalas/setores são referências gráficas, sem alterar alarmes reais. Não são inferidos dois circuitos de freio.

## Instalação e atualização

Um instalador de teste foi compilado e executado com o payload final e o mesmo código de cópia, backup e verificação da distribuição. Usa diretório próprio e atalhos internos; não registra desinstalador nem muda inicialização pessoal. A consulta WMI é desativada somente nessa compilação de teste. O executável de produção não foi executado.

Passaram: mutex, instalação limpa com todos os hashes/perfil e sem criar configuração pessoal, arquivo em uso, falha de backup antes da substituição, atualização preservando principal/backup e binário anterior, desinstalação preservando dados/arquivo desconhecido/backups e reinstalação. App e gerenciador não foram abertos pelo instalador. DLL não instalada no ETS2.

Uma fixture com caminho muito longo abortou com segurança ao copiar um recurso do runtime para backup. A tentativa foi preservada; repetição em caminho curto passou. Caminhos excepcionalmente longos não estão homologados. Houve falha intermitente de pixel em scrollbar nativa numa tentativa; a regressão integral serializada passou depois, sem mudança da pintura de produção. Tentativas com fixtures ausentes e expectativas anteriores foram preservadas e não contadas como aprovação.

## Integridade e limites

Portátil e instalador usam o mesmo payload. Cada entrada do portátil foi comparada por SHA256. Pacotes excluem fontes próprios, PDB, objetos, configuração pessoal, histórico privado e firmware. DLL e ícones separados foram comparados ao payload. SHA256SUMS.txt é separado da documentação para evitar autorreferência.

Não executados: ETS2 real, G29/G923 físicos, manopla/motores, medições elétricas, múltiplos monitores físicos, tela cheia exclusiva e instalação de produção num Windows limpo. Software e dados simulados não homologam tensão, corrente ou desligamento físico. A entrega permanece pré-lançamento.
