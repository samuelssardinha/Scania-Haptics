Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# Protocolo serial efetivamente implementado

Conjunto 3 não cria comandos/IDs de transação. Sintaxe, argumentos e respostas do firmware permanecem; um fragmento sem terminador que fica parado por mais de 500 ms agora é descartado até CR/LF. Para recuperar após fragmento expirado, envie um terminador e uma linha nova completa. Até 256 bytes são processados por loop; buffer útil continua 127 caracteres.

No PC há uma fila de 128 comandos, validade local padrão de 1 s e coalescência de TELEM/HAPTIC/ROADFX pendentes. STOP/LEVEL 0 descartam os pendentes anteriores; não preemptam escrita nativa iniciada. Pulsos e BOOTLOADER não são repetidos automaticamente. Send retorna admissão, SendAsync retorna escrita, RequestAsync exige frame completo após escrita e predicado esperado. Abertura, leitura, escrita e fechamento têm um único proprietário.

STATUS é sondado a cada 700 ms; 2.500 ms sem STATUS válido derrubam sessão e fila. Configuração exige STATUS correspondente antes de liberar comandos; ENGINE ON e BOOTLOADER usam ACK exato. ERROS inesperados ou banner de reinício invalidam conexão; ERR TEST MODE é tratado como bloqueio de modo. Frames de resposta têm limite de 4.096 caracteres e fragmentos expiram em 500 ms. Nenhum ACK idêntico atrasado pode ser distinguido universalmente de outro sem IDs; a proteção combina sessão, escrita, predicado, ausência de retry e parada conservadora. STATUS não renova o watchdog de motores do ESP32.

Atualização do conjunto 2: após MTEST/DUAL, firmware entra em isolamento persistente. TELEM/HAPTIC/ROADFX e outros geradores não substituem o pulso e retornam `ERR TEST MODE (ENGINE ON para retomar)`. STOP e timeout não liberam isolamento. Use `ENGINE ON` válido antes de retomar operação; o app atualizado envia esse comando. Sintaxes/faixas e ACKs dos testes permanecem; clientes antigos que dependem de TELEM para sair do diagnóstico precisam adaptar a sequência. USB, HID e mapa da DLL permanecem intactos.

ROADTEST BUMP mantém sintaxe/amplitudes/duração, mas passa pelos limites de efeitos (EV_PULSE), sem bypass de seleção/POWER/GAIN SHIFT/LEVEL e sem ativar isolamento. RUMBLE/ROUGH e demais efeitos também passam pelo caminho normal. MTEST/DUAL continuam permitindo diagnóstico de motor desabilitado para efeitos e ignorando ganhos, com PWM direto e LEVEL 0 como bloqueio. Isso não é um limite de corrente ou tensão.

Fonte: sketch v2.2.12, `handleCommand:599`, `readSerial:942`. USB CDC, SerialPort 115200, ASCII, uma linha por comando. CR ou LF encerra linha; vazios são ignorados. Buffer de 128 bytes admite até 127 caracteres, depois descarta até terminador e responde `ERR LINE TOO LONG`. Tokens usam espaço/tab; comando e argumentos textuais relevantes aceitam minúsculas por conversão interna. Não há número de transação, checksum ou negociação formal de versão.

Conjunto 1: formato e semântica dos comandos no firmware preservados. STOP limpa efeitos imediatamente quando processado; outro comando explícito pode iniciar efeito novamente. No aplicativo corrigido, a ação STOP também trava o gerador automático até retomada explícita na UI, evitando reenvio indevido de TELEM/HAPTIC/ROADFX. Nenhum comando RESUME foi adicionado.

Colchetes indicam argumento opcional de consulta. Todos os comandos abaixo foram lidos no parser; não foram enviados à placa. Em geral sucesso é `OK <comando>` e erro `ERR <sintaxe>`; exceções e valores estão indicados. OK confirma processamento do comando, não corrente elétrica ou movimento. Com ENGINE OFF, vários testes respondem OK sem executar.

| Comando e argumentos | Resposta normal / comportamento | Linha |
|---|---|---:|
| HELP | linha com lista de comandos, sem prefixo OK | 625 |
| STATUS | linha de pares CHAVE=VALOR | 627 |
| MODE [NORMAL\|BRUTO] | OK MODE perfil | 629 |
| LEVEL [0..5] | OK LEVEL n (PWM_CEILING=255); zero bloqueia tudo | 639 |
| GAIN IDLE\|DRIVE\|SHIFT 0..100 | OK GAIN tipo valor | 649 |
| DEBOUNCE [5..100] | OK DEBOUNCE ms | 660 |
| BRAKE [0..20] | OK BRAKE ms; zero libera frenagem | 669 |
| MOTORS M1\|M2\|BOTH | OK MOTORS seleção | 679 |
| SWAP 0\|1 | OK SWAP n; coast antes de permutar | 690 |
| DIR 1\|2 NORMAL\|REVERSE | OK DIR motor sentido | 700 |
| MOTOR2 ON\|OFF | OK MOTOR2 estado; alias legado só M2 | 715 |
| MIX [0..200] | OK MIX n; alias POWER 2 | 723 |
| POWER 1\|2 0..200 | OK POWER motor percentual | 733 |
| NORMMIN 1\|2 0..180 | OK NORMMIN motor pwm | 733 |
| GALLOP [0..100] | OK GALLOP n | 747 |
| MISFIRE [0..100] | OK MISFIRE n | 756 |
| NEUTRALBOOST [0..100] | OK NEUTRALBOOST n | 756 |
| CHAOS [0..100] | OK CHAOS n | 756 |
| LOWRPM [500..1400] | OK LOWRPM n | 756 |
| STOP | OK STOP; limpa efeitos e coast, sem trava persistente | 776 |
| ENGINE ON\|OFF | OK ENGINE estado; limpa efeitos | 779 |
| TEST | OK TEST; 3 ciclos 250 ms on / 150 off, p1=110/p2=145 | 790 |
| SHIFT | OK SHIFT; 1 ciclo 120/20 ms, p1=110/p2=170 | 794 |
| BRAKETEST | OK BRAKETEST; 1 ciclo 180/20 ms, p1=110/p2=160 e freio | 798 |
| ERROR | OK ERROR; 3 ciclos 200/120 ms, p1=110/p2=200 | 802 |
| PULSE 0..255 1..1000 | OK PULSE; p2=75% de p1, sujeito a ganhos | 806 |
| MTEST 1\|2 0..255 1..1000 | OK MTEST motor; PWM direto no lógico selecionado | 816 |
| DUAL 0..255 0..255 1..1000 | OK DUAL; dois PWMs diretos | 830 |
| VIB 0..255 | OK VIB; limpa base anterior, vibração contínua | 841 |
| IDLE 0..3000 | OK IDLE; limpa efeitos, aplica RPM | 849 |
| STARTFX 0..100 150..1000 | OK STARTFX; envoltória de partida | 857 |
| STOPFX 0..100 150..1000 | OK STOPFX; envoltória de desligamento | 857 |
| ROADFX 0..100 0..100 | sem ACK; impacto e rumble, validade 350 ms | 872 |
| ROADTEST BUMP\|RUMBLE\|ROUGH | OK ROADTEST; eventos demonstrativos | 884 |
| HAPTIC 0..100 0..100 0\|1 | sem ACK; redução embreagem, rough, reversão global | 894 |
| TELEM 0..5000 -20..30 0\|1 | OK TELEM; RPM, marcha, motor; pode disparar engate | 907 |
| BOOTLOADER | OK BOOTLOADER: reiniciando em modo de gravacao USB | 606 |
| REBOOT | OK REBOOT: reiniciando firmware e joystick HID | 606 |

POWER e NORMMIN exigem os dois argumentos; os colchetes no cabeçalho antigo do sketch não representam consulta aceita pelo parser. Comando desconhecido responde `ERR UNKNOWN (HELP)`. Inteiros são analisados com strtol e rejeitam sufixos; overflow não é verificado por errno, mas as faixas pequenas posteriores rejeitam os extremos retornados.

Atividade que renova watchdog: ENGINE, TEST, SHIFT, BRAKETEST, ERROR, PULSE, MTEST, DUAL, VIB, IDLE, STARTFX/STOPFX, ROADFX, ROADTEST, HAPTIC, TELEM. HELP, STATUS, STOP e ajustes não o renovam. Sem essa atividade por mais de 2 s, clearEffects para motores. Consultar STATUS repetidamente não mantém VIB ligado. HAPTIC/ROADFX repetidos podem, porém, manter uma base TELEM antiga: não são watchdogs separados.

STATUS inclui RANGE_A_LOW/RANGE_B_LOW/RANGE_VALID/RANGE_HID/RANGE_POS, grupo equivalente SPLITTER, EXTRAS, MOTOR_PWM/MOTOR2_PWM, PWM_OK/PWM2_OK/PWM_HZ, ENGINE/IDLE_RPM/GEAR/GEAR_KNOWN/LEVEL/PWM_CEILING/MODE, IDLE_GAIN/DRIVE_GAIN/SHIFT_GAIN/DEBOUNCE_MS/BRAKE_MS/BRAKING, MOTOR2/M1_ENABLE/M2_ENABLE/SWAP/DIR1/DIR2/M1_PHYS/M2_PHYS, MIX/POWER1/POWER2/NORMMIN1/NORMMIN2/GALLOP/MISFIRE/LOWRPM/NEUTRALBOOST/CHAOS/CLUTCH/ROAD/REVERSE/EVENT. PWM é o estado calculado do canal físico, não uma medição. A_LOW é leitura instantânea, HID/POS usa debounce/inversão. B_LOW=0, VALID=1 e EXTRAS=0 são valores de compatibilidade, não sensores extras. Não há campo dedicado para roadImpact/roadRumble/frescor.

Exemplo de sequência planejada para diagnóstico isolado (não executada):

```text
STOP
ENGINE ON
LEVEL 1
MTEST 1 60 300
STATUS
STOP
```

LEVEL 1 não reduz PWM 60 nesse teste; apenas LEVEL 0 bloqueia. O host deve suspender TELEM/HAPTIC/ROADFX antes e manter a suspensão até STOP. Não assumir que MTEST elimina base anterior: para clientes diretos, usar STOP primeiro. Testar apenas com fonte adequada e confirmação da montagem.

Compatibilidade: MODO, anunciado no TXT antigo, não existe; usar MODE. MIX aceita 200, não apenas 100. Documentos 2.2.9 dizem que LEVEL limita MTEST: isso mudou em 2.2.11. BOOTLOADER/REBOOT são anunciados a partir de 2.2.8; POWER/NORMMIN de 2.2.9; ROADFX/ROADTEST de 2.2.12. Isso é histórico documental, não ensaio de firmwares antigos. O app detecta STATUS por tokens, sem validar essas capacidades; aplicar app novo a firmware antigo pode produzir ERR sem bloquear a operação.

Alguns ramos legados aceitam argumentos excedentes (STOP, TEST, SHIFT, ERROR, HELP, STATUS); MODE e ENGINE chegam a modificar estado antes de rejeitar argumento excedente. Não usar ACK de erro como garantia de ausência de efeito. Riscos e propostas constam na auditoria.

