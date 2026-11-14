Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# RC1.4 — base e calibração

Nova base desligada por padrão. Nenhuma etapa aciona motores ao abrir a tela. Requer confirmação de segurança, conexão negociada e clique explícito. Não há sensor de corrente, tensão, temperatura, vibração ou ressonância: números são comandos PWM, não medições físicas.

## Protocolo aditivo

`BASE <enable 0|1> <idle1 0..180> <idle2 0..180> <resonance 0..100>` exige exatamente quatro argumentos válidos. Resposta `OK BASE e p1 p2 r`. Configurar interrompe efeitos anteriores, não inicia movimento e não renova a autorização temporal. STATUS mantém o prefixo existente e acrescenta `CAPS_BASE1=1 CAPS_CAL1=1 TELEM_TIMEOUT_MS=600 BASE_ENABLED=... BASE_M1=... BASE_M2=... BASE_RESONANCE=...`.

App verifica capacidades na sessão atual antes de enviar BASE. Firmware antigo não recebe esse comando; assistente fica bloqueado. MODE desativa BASE para proteger clientes antigos; app novo reaplica apenas a opção confirmada. Reinício do firmware volta ao modo legado. Não houve alteração do transporte/worker serial.

Somente TELEM válido renova o watchdog independente de 600 ms. STATUS, ROADFX e outros comandos não mantêm a base viva. Timeout, STOP, ENGINE OFF e TELEM com motor desligado limpam efeitos/envelopes. Timeout genérico legado permanece 2 s para modos manuais. Em telemetria real, a nova base também exige RPM e ENGINE V4 válidos por campo; heartbeat sem RPM fresco não autoriza saída. Simulação é explícita.

A envoltória Q8 suaviza por 150 ms; progressão de rotação aumenta intensidade e textura triangular, sem sincronismo mecânico 1:1. Seleção, SWAP, direção, ganhos, POWER, LEVEL e teto PWM continuam no caminho comum de saída. O limite BASE de 180 é anterior aos ganhos: ganho existente pode levar a saída ao teto legado 255. PWM não regula a tensão de pico da fonte.

## Assistente exclusivo

Entrar envia STOP e bloqueia fontes automáticas. Pulsos MTEST/DUAL duram 800 ms, com PWM direto e regras legadas desses testes; não equivalem ao ganho da operação normal. Progressão usa quatro patamares de 800 ms (650/1400/2400/650 RPM simulados), com ganhos normais. STOP fixo permanece visível fora da área de rolagem. Sair, systray, perda COM, encerramento ou STOP cancelam inclusive escritas assíncronas pendentes. Retomada automática exige ação explícita depois.

Rascunho separado: mínimos e valores confortáveis são registrados pelo usuário. Salvar exige confirmar os dois motores, mínimos coerentes e a confirmação final. Falha na persistência restaura a configuração em memória. Cancelar preserva valores anteriores. A configuração confirmada é restaurada após teste sem reautorizar movimento.

Motor 3 V paralelo em OUT3/OUT4 não é independente; a carga conjunta exige fonte/driver adequados. SWAP muda a associação lógica. Nem um mínimo registrado nem uma combinação simulada comprovam segurança elétrica. Testes físicos e ETS2 não foram executados nesta entrega.

