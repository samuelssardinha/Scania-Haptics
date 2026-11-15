# Guia da versão atual

Referência: RC11.20, App 2.3.0.20, gerenciador 2.3.0.17 e DLL 2.3.0.12. Instalador e portátil autocontidos Windows x64.

## Aparência, posição e padrões

O perfil de primeira execução Meu cockpit usa Nordic Light; Dark Glass Pro continua disponível na restauração explícita dos padrões; Glass Clear, Liquid Glass, Frosted Neon e os estilos anteriores permanecem disponíveis. Régua RPM ligada com 150 LEDs, legendas ocultas e Alt+A são os novos padrões; valores já salvos são respeitados. O atalho exige HUD habilitada.

Em **HUD e instrumentos → Layout e mouse**, escolha Topo/Parte inferior, margens e restauração. Restaurar somente a HUD conserva ativação, monitor e atalho. Temas, escopos e geometria ficam em Aparência e temas. [Novidades e imagens](RC11_20.md).

## Componentes e dados

```mermaid
flowchart LR
  ETS2[ETS2 x64] --> DLL[Plugin de telemetria x64]
  DLL --> MMF[Memória compartilhada local]
  MMF --> APP[WinForms .NET 10 / snapshot]
  APP --> HUD[HUD e indicadores]
  APP --> LOGI[Worker LEDs G29/G923]
  APP --> SERIAL[Worker USB CDC / fila limitada]
  SERIAL --> ESP[ESP32-S2 existente]
  ESP --> DRV[DRV8833 / dois canais]
  ESP --> HID[Range e Splitter HID]
```

A DLL registra canais do SDK SCS e publica dados em mapas locais de memória; o App confere validade e mantém um snapshot para seus módulos. A serial tem um único proprietário, evitando acesso concorrente à COM. Os seletores HID funcionam sem depender do App. A DLL e o firmware têm números de protocolo independentes da versão comercial.

## Abrir, atualizar e retornar

Baixe `Scania_Haptics_RC11_20_Portavel_win-x64.zip`, confira o SHA256, encerre a versão antiga pela bandeja e extraia tudo em pasta nova. Abra `ScaniaHaptics.exe`; .NET já está incluído. Também é possível usar o novo instalador RC11.20. Veja [instalação e atualização](INSTALAR_RC11_20.md).

O portátil usa a mesma instância única e as preferências existentes em `%LOCALAPPDATA%/ScaniaHaptics/config.json`. Exporte sua configuração em **Importar / Exportar / Perfis** antes de mudar os padrões. A importação valida, cria backup e deixa saídas paradas/desconectadas. [Exportação, importação e restauração](IMPORTAR_EXPORTAR_PERFIS.md).

Para configurar a DLL opcional incluída, feche o ETS2 e use o gerenciador. O destino continua exclusivamente `bin/win_x64/plugins/scania_haptics_v2.dll` na raiz escolhida. A versão da DLL não mudou nesta rodada; não é necessário substituí-la apenas para experimentar os novos temas. O gerenciador preserva o arquivo anterior para rollback.

Para voltar ao aplicativo anterior, encerre a candidata e abra a versão antiga. Use seu backup de configuração se precisar restaurar exatamente o perfil anterior. A DLL do jogo é gerenciada separadamente.

## Aplicativo

As páginas agrupam operação, motores/testes, dispositivos e HUD. A HUD permite ativação, legenda, fonte e formato individual, layouts, escala, alinhamento e ordem. Componentes desligados deixam de reservar espaço. Arrastar/trocar/remover componentes é uma operação do editor no desktop; a visualização durante o jogo não deve capturar controles indevidamente.

Os ajustes gráficos não alteram marcha/transmissão do jogo. Cruise, retarder, pressões e indicadores dependem de canais válidos; não se inferem ABS/TCS ou níveis ausentes. A simulação oferece valores sintéticos e pode comandar dispositivos habilitados/conectados. Inicia explicitamente, cede à telemetria real e é encerrada ao minimizar/ocultar. Não reinicia automaticamente após o jogo e não comprova recepção do ETS2.

Fechar para a bandeja e inicialização automática são preferências. Restaure pelo menu da bandeja ou pelas opções/hotkeys configuradas; para atualizar, escolha sair completamente. A proteção de instância única evita execução concorrente normal. O instalador não ativa AutoStart por conta própria; o App respeita preferências anteriores quando aberto.

O módulo Logitech tem worker e transporte próprios. Controle de LEDs não é leitura de force feedback. Não use resultado de simulação como homologação de G29/G923 físico.

## Hardware existente

| Função | GPIO |
|---|---:|
| Range / Splitter | 1 / 2 |
| DRV8833 canal A IN1 / IN2 | 9 / 10 |
| DRV8833 canal B IN3 / IN4 | 8 / 4 |

GPIO10 corresponde ao firmware; continuidade da solda deve ser confirmada, pois há desenho antigo indicando GPIO11. Não trocar pinagem com base nesse desenho. Um motor de 3 V em paralelo ao canal B acompanha PWM e polaridade desse canal. SWAP muda os papéis lógicos, sem criar um terceiro canal.

PWM é de 1 kHz e 8 bits. A montagem não fornece medição de corrente/temperatura ao software. Validar tensão, corrente de partida/travamento, proteção, dissipação e estados de boot; duty reduzido não torna uma fonte de tensão maior automaticamente adequada ao motor de 3 V. Manter corte físico de alimentação acessível. Ensaios elétricos estão pendentes.

## Firmware e serial

O firmware existente anuncia v2.2.12 e usa ESP32-S2/TinyUSB com HID e CDC. Não acompanha este pacote e não será gravado por ele. USB, pinagem e protocolos não foram alterados na preparação para GitHub.

Serial ASCII a 115200, um comando por linha CR/LF. STATUS informa capacidades; BASE1/CAL1 são negociados e não devem ser enviados a versões que não os anunciam. Consulte a [referência serial](PROTOCOLO_SERIAL.md) e a [extensão de base/calibração](RC1_4_BASE_CALIBRACAO.md) para sintaxe, argumentos, respostas e limites.

MTEST/DUAL isolam testes, com PWM direto e duração limitada; desligam geração contínua e exigem retomada por ENGINE ON válido. STOP/timeout mantêm a parada e o isolamento. LEVEL 0 bloqueia saída, mas não é uma proteção elétrica independente. O watchdog do firmware usa atividade válida; consultas STATUS não o renovam. Não execute testes de PWM alto numa montagem sem limites validados.

## DLL e contratos

Exports x64: `scs_telemetry_init` e `scs_telemetry_shutdown`. V1/V2/V3 têm 24/48/108 bytes, little endian, com prefixos preservados. V4.1 usa mapa separado de 384 bytes, guarda de sincronismo, identidade da publicação e validade por campo. Signals1, Jobs1, Indicators1, Instruments1 e Vehicle1 são extensões opcionais. Vehicle1 tem 256 bytes, permitindo pressão/temperatura e estados específicos; ausência de extensão não impede o App de apresentar os dados antigos.

Referências completas de offsets e sincronismo: [V4](PROTOCOLO_TELEMETRIA_V4.md), [Signals1](PROTOCOLO_SIGNALS1.md), [Jobs1](PROTOCOLO_JOBS1.md), [Indicators1](PROTOCOLO_INDICATORS1.md), [Instruments1](RC1_4_1_PROTOCOLO_INSTRUMENTS1.md), [Vehicle1](PROTOCOLO_VEHICLE1.md). A validade individual de RPM/ENGINE é exigida na V4 antes de liberar telemetria háptica.

## Compilação e distribuição

Os fontes originais permanecem privados. O aplicativo foi compilado com SDK .NET 10.0.403, runtime e System.IO.Ports 10.0.14, para win-x64 autocontido. A origem e o hash do manifesto dos fontes atuais constam em BUILD_METADATA.json; o repositório público contém documentação e imagens.

O aplicativo foi recompilado dos fontes atuais com o perfil incorporado. DLL/gerenciador/runtime/avisos foram conferidos e reaproveitados da candidata anterior. Nenhum compilador, fonte próprio, objeto, símbolo de depuração, configuração pessoal ou firmware integra os downloads.

## Verificação e problemas comuns

```powershell
Get-FileHash .\Scania_Haptics_RC11_20_Portavel_win-x64.zip -Algorithm SHA256
```

Compare o resultado com SHA256SUMS.txt baixado da mesma release. Os executáveis não são assinados digitalmente. Os testes e suas limitações estão em [VALIDACAO.md](VALIDACAO.md).

| Sintoma | Conferir |
|---|---|
| Sem telemetria | ETS2 x64, plugin no destino correto, jogo reiniciado após instalação, versões e validade dos canais |
| COM ocupada | Fechar outra instância, monitor serial ou ferramenta de gravação; não forçar reinstalação do dispositivo |
| Motor parado | LEVEL, seleção de canal, STOP/isolamento, necessidade de retomar, dados vencidos e alimentação |
| Nome TinyUSB HID no joy.cpl | Nome USB e nome OEM do Windows são distintos; esta publicação não altera Registro nem driver |
| Campo indisponível | Canal opcional ausente ou sem validade; não substituir por estado inventado |
| Janela na bandeja | Restaurar pelo menu; para atualizar, encerrar também o observador |

Não foram executados nesta preparação: ETS2 real, instalação pessoal em produção, alteração do Registro pessoal, firmware/bootloader, motores, LEDs físicos ou medições elétricas. Relatos anteriores e testes sintéticos não substituem esses ensaios.

