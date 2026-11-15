Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# Telemetria V4.1 — contrato implementado na RC1.2

09/11/2026. Este documento substitui o [plano 4.0 de 256 bytes](PROTOCOLO_TELEMETRIA_V4_PLANO_4_0.md), que nunca foi implementado/distribuído. RC1.2 usa **384 bytes, major=4, minor=1**, para adicionar combustível/autonomia/navegação/hodômetro. V1/V2/V3 permanecem nos mesmos nomes, magic, tamanhos e offsets. Não muda firmware/serial/HID/USB nem autoriza atuação.

Fontes primárias: [SDK SCS](https://modding.scssoft.com/wiki/Documentation/Engine/SDK/Telemetry) e [ZIP oficial 1.16](https://download.eurotrucksimulator2.com/scs_sdk_1_16.zip), SHA256 `49821E25568F4D7E462135F577F8FEF2AB197326404F2C1DAEF139B8F3DC5E69`. Headers conferidos em ambiente isolado de testes; sem instalar dependências. Leitura de canais/configurações/gameplay e comparação ABI x64 contra esses headers executadas.

## Objetos e exclusividade

Windows x64, little-endian, float IEEE754 binary32. Mapa `Local\ScaniaETS2TelemetryV4`; mutex de dados `.Guard`; lease `.Writer` anexados ao nome completo do mapa. Sem ponteiros, strings, bool CLR ou std::atomic no payload. Estrutura C++ pack(1), alinhamento externo 8, tamanho 384; todos os offsets têm static_assert. C# decodifica bytes little-endian.

**Exclusividade resolvida:** CreateMutexW(initialOwner=TRUE) de Writer precisa criar objeto **novo**. ERROR_ALREADY_EXISTS é recusa, mesmo na mesma thread/processo; não se chama WaitForSingleObject em Writer, evitando aquisição recursiva por outra DLL na thread SDK. A lease é adquirida antes de tocar V1/V2/V3. Init duplicado recusa sem zerar dados/registrar callbacks. Falha da lease impede init em vez de disputar os mapas. Falha apenas da extensão V4 mantém legado sob a lease.

Somente o publicador mantém handle de Writer; leitores nunca o abrem. Ao morrer o processo, os handles do publicador desaparecem e uma nova instância consegue criar a lease. Encerramento normal libera mapas antes de soltar Writer. Handle externo retendo Writer causa recusa conservadora até ser fechado; não há takeover por PID/timeout nem alteração de ACL. Isso coordena DLLs RC1.2 cooperantes; uma DLL antiga não usa essa lease. Instalação exige jogo fechado e uma única cópia do plugin, sem backups .dll em plugins.

## Layout completo

Magic uint32 `0x344E4353`, bytes 53 43 4E 34 (SCN4). Campos reservados escritos zero. Bytes 8..107 são espelho **privado** da última publicação legada, com suas limitações/clamps; não leitura/mistura de outro mapa. Header sequence é V4, legacySequence separado preserva a sequência usada na projeção háptica.

| Offset | Bytes | Campo | Tipo/unidade |
|---:|---:|---|---|
| 0 | 4 | magic | uint32 SCN4 |
| 4 | 4 | sequence | uint32 commit V4 |
| 8 | 4 | legacyTickMs | uint32 GetTickCount |
| 12 | 4 | legacyRpm | float 0..5000 |
| 16 | 4 | engineGear | int32 |
| 20 | 1 | engineEnabled | byte 0/1 |
| 21 | 1 | paused | byte 0/1 |
| 22 | 2 | legacyReserved | uint16 zero |
| 24 | 4 | throttle | float 0..1 |
| 28 | 4 | clutch | float 0..1 |
| 32 | 4 | legacySpeed | float m/s -150..150 |
| 36 | 4 | engineWear | float 0..1 |
| 40 | 4 | legacyFlags | uint32 zero |
| 44 | 4 | legacyReserved2 | uint32 zero |
| 48 | 12 | acceleration[3] | float X/Y/Z m/s² |
| 60 | 32 | wheelDeflection[8] | float metros |
| 92 | 4 | wheelValidMask | uint32 legado |
| 96 | 4 | contactMask | uint32 legado |
| 100 | 4 | contactValidMask | uint32 legado |
| 104 | 4 | legacyRoadFlags | uint32 bit0 aceleração recebida |
| 108 | 2 | major | uint16 4 |
| 110 | 2 | minor | uint16 1 |
| 112 | 4 | bytes | uint32 384 |
| 116 | 4 | state | uint32 flags abaixo |
| 120 | 8 | epochLo | uint64 identidade opaca |
| 128 | 8 | epochHi | uint64 identidade opaca |
| 136 | 8 | generation | uint64 configuração truck |
| 144 | 8 | published | uint64 uptime ms |
| 152 | 8 | frame | uint64 frame_start local |
| 160 | 4 | registered | uint32 aquisição suportada |
| 164 | 4 | valid | uint32 validade por campo |
| 168 | 4 | rpm | float RPM bruto |
| 172 | 4 | limit | float RPM máximo configurado |
| 176 | 4 | gear | int32 marcha exibida |
| 180 | 4 | speed | float m/s assinado |
| 184 | 4 | roadLimit | float m/s |
| 188 | 4 | cruise | float m/s, zero desligado |
| 192 | 8 | rpmTick | uint64 ms |
| 200 | 8 | limitTick | uint64 ms da configuração |
| 208 | 8 | gearTick | uint64 ms |
| 216 | 8 | speedTick | uint64 ms |
| 224 | 8 | roadTick | uint64 ms |
| 232 | 8 | cruiseTick | uint64 ms |
| 240 | 4 | legacySequence | uint32 sequência legada |
| 244 | 4 | pid | uint32 diagnóstico |
| 248 | 8 | frameStarted | uint64 ms |
| 256 | 4 | fuel | float litros |
| 260 | 4 | range | float km, estimativa fornecida pelo SDK |
| 264 | 4 | navigation | float metros restantes no navegador |
| 268 | 4 | odometer | float km |
| 272 | 4 | travelled | float zero; indisponível RC1.2 |
| 276 | 4 | jobFlags | uint32 bit0 grupo job não vazio |
| 280 | 8 | fuelTick | uint64 ms |
| 288 | 8 | rangeTick | uint64 ms |
| 296 | 8 | navigationTick | uint64 ms |
| 304 | 8 | odometerTick | uint64 ms |
| 312 | 8 | travelledTick | uint64 zero |
| 320 | 8 | jobGeneration | uint64 revisão grupo job |
| 328 | 4 | jobStartOdometer | float zero |
| 332 | 4 | reserved | uint32 zero |
| 336 | 8 | jobReferenceTick | uint64 zero |
| 344 | 40 | tail | bytes zero |

Último campo termina em 384; uint64 em múltiplos de oito. NamedValue SDK tem 64 bytes, Value 48, Configuration 16, FrameStart 32, InitV100 64, confirmados contra headers oficiais. Não aplicar pack(1) aos tipos SDK. Exports e negociação SDK 1.00 preservados.

## Flags, aquisição e unidades

State bits: 0 INITIALIZED, 1 PAUSED, 2 TRUCK_CONFIGURED, 3 SHUTTING_DOWN, 4 CONFIG_PENDING, 5 STRICT_DELIVERY, 6 SDK_TIMER_RESTART, 7 FRAME_COMPLETE; superiores zero. Aceitação requer INITIALIZED/STRICT/COMPLETE, sem SHUTDOWN/PENDING, frame>0 e época não zero. PAUSED precisa coincidir com byte21. TRUCK exige generation>0.

| Bit registered/valid | Origem SDK | Tipo | Timestamp |
|---:|---|---|---|
| 0 | truck.engine.rpm | float RPM ≥0, sem clamp | 192 |
| 1 | truck / rpm.limit | atributo float >0 | 200 |
| 2 | truck.displayed.gear | s32: negativo ré, zero N | 208 |
| 3 | truck.speed | float m/s do velocímetro, assinado | 216 |
| 4 | truck.navigation.speed.limit | float m/s; depende da opção Route Advisor | 224 |
| 5 | truck.cruise_control | float m/s; zero desligado | 232 |
| 6 | truck.engine.gear, prefixo | s32 | 248 |
| 7 | truck.engine.enabled, prefixo | bool SDK | 248 |
| 8 | truck.fuel.amount | float litros ≥0 | 280 |
| 9 | truck.fuel.range | float km ≥0 | 288 |
| 10 | truck.navigation.distance | float metros ≥0 | 296 |
| 11 | truck.odometer | float km ≥0 | 304 |

Bit12 (percorrido) e superiores não são registrados/válidos nesta versão. Valid é subconjunto de registered; registered não garante sensor disponível. Todos os floats precisam ser finitos. Exceto velocidade assinada, negativos são inválidos. Zero é válido nos dinâmicos e distinto de ausência; rpm.limit zero é inválido. Não inferir via sem limite a partir de zero. RPM acima de 5000 ou do limite permanece observação válida; só o arco satura visualmente.

SDK permite um callback por canal/índice/tipo e por evento; compartilhados pelos estados legado/V4. Registros dinâmicos V4 usam EACH_FRAME|NO_VALUE=3; frame_start limpa a validade e frame_end fecha a amostra. Canais ausentes/nulos/tipo errado são canônicos zero/bit desligado, sem cache eterno. Falha de registro opcional afeta só o campo. Falha de flags estritas nos essenciais tenta flags originais para manter legado e desabilita V4 estrita; already_registered não é autorização para substituir callback. Eventos de configuração/frame_start não chamam writeMap legado. Os eventos originais continuam com a mesma cadência de publicação V1/V2/V3.

rpm.limit é lido pelo evento CONFIGURATION=5 registrado no init, grupo truck, array NamedValue terminado por name=null. Exige nome rpm.limit, índice NIL, tipo float, finito/>0 e ocorrência única; varredura defensiva até 4096 entradas. Ponteiros não são retidos. Todo evento truck incrementa generation, limpa dinâmicos/limite anteriores e aguarda frame completo, inclusive alteração de motor no mesmo truck.id. Grupo vazio/configuração inválida invalida. Outros grupos não apagam o limite; job atualiza somente contexto job. Pausa invalida dinâmicos; limite estático pode permanecer conhecido.

## Concorrência e recuperação

Guard é obrigatório, com tentativa **zero timeout** em ambos os lados. Contenção: escritor pula publicação V4 sem renovar heartbeat; leitor faz fallback V3→V2→V1, sem misturar extras. Não há espera por outro processo, garantia de tempo real nem promessa de receber cada frame.

Publicador constrói quadro privado completo. Sob Guard, grava sequence ímpar, copia os 384 bytes com sequence ainda ímpar, executa barreira completa e confirma com InterlockedExchange par. **Sequência resolvida:** próximo par vem de contador privado/NextEven, nunca de sharedSequence+2 sem corrigir paridade. Ímpar residual/corrompido é substituído por quadro integral e commit par; wrap32 termina em zero par. WAIT_ABANDONED também exige cópia integral. Guard impede ABA durante a cópia.

Nova instância gera GUID opaco 128 bits com CoCreateGuid (Ole32 do Windows). Após obter lease, tenta imediatamente publicar controle pendente da nova época, antes de abrir/zerar legado, invalidando conteúdo anterior retido por leitores. Sob contenção, próximo callback tenta novamente. Leitores não criam mapas, não abrem Writer e não escrevem na view. WaitOne(0), sequência par antes/depois/cópia iguais e barreiras; depois liberam Guard e decodificam buffer privado. AbandonedMutexException concede posse: descartar tentativa e liberar em finally. Não fazer Interlocked de escrita em view read-only.

Morte durante cópia: abandono/ímpar são rejeitados; próximo publicador reusa mapa retido, muda época e publica integralmente. Morte após par deixa último quadro somente até expirar. Não existe reparo por escrita do aplicativo; sem publicador legítimo o leitor não transforma ímpar em dado válido. Shutdown tenta marcar SHUTTING_DOWN antes de fechar; não depende de ACK do app.

## Frescor e rejeição

Tempos novos: GetTickCount64/Environment.TickCount64, ms desde boot, não UTC nem timers de simulação SDK (µs). Publicação e dinâmicos expiram com idade **≥1500 ms**; instante futuro é inválido. LegacyTick mantém idade modular uint32 e também precisa ser fresco. Cada dinâmico válido pertence ao frame: frameStarted ≤ tick ≤ published. Heartbeat/redraw não renova dados individuais. rpm.limit só expira por fonte/geração/configuração, não por falta de callback a cada frame.

Cabeçalho/versão/tamanho/flags/máscaras/época/timestamps inconsistentes ou inválidos não canônicos rejeitam todo bloco; versão futura não é presumida compatível. Valor numérico opcional fora de domínio, com estrutura íntegra, invalida só o campo gerenciado. Invalidade nova exige valor/timestamp zero; prefixo legado mantém cache e suas limitações históricas. Reservoir de job/referência/cauda precisa ser zero conforme layout; jobFlags só bit0.

Reader retorna uma única TelemetrySample com projeção legada (inclusive legacySequence) e Extras imutáveis. MainForm continua lendo uma vez e usa Rpm/Gear/Speed legados para háptica; somente Snapshot/HudFrame usam raw/displayed/limites. Sem V4 válida, extras são eliminados. STOP/ENGINE ON/TESTES/SIMULAÇÃO/GRAVAÇÃO/reconexão não são autorizados pela observação. Range/Splitter permanecem no STATUS USB existente.

## Trabalho atual e limites

SDK 1.16 tem configuração job e eventos job.delivered/job.cancelled; distance.km pertence à entrega concluída, não é referência inicial confiável do trabalho atual. A configuração inicial pode representar trabalho em andamento, e mudanças/load não certificam início. RC1.2 **não estabelece baseline arbitrário** do hodômetro, não persiste referência e não calcula planned_distance−remaining. Portanto travelled/jobReference permanecem indisponíveis. Hodômetro real é coletado como observação para futura referência comprovada. Autonomia é a estimativa do SDK, sem cálculo local. Rota restante é a navegação atual, inclusive destino manual; HUD identifica “Rota”, não prova da distância do trabalho.

Compatibilidade garantida de layout/caminho legado não corrige automaticamente os achados históricos de frescor/seqlock V1/V2/V3. Velocidades são convertidas para km/h e navegação para km só na apresentação; valores absurdamente grandes usam notação científica, sem limitar o dado bruto. Não há leitura de FFB do G923 ou nova síntese háptica.

## Evidências

`tests/Rodar_V4.ps1`: asserts SDK/layout, callbacks reais sob SDK simulado, prefixos legados, flags recusadas, configuração/ausência/zeros/NaN, duplicação, wrap/ímpar e fixtures C++↔C#. MMF real com GUID de teste, contenção, processos distintos com ≥1 milhão de snapshots aceitos, leitor morto com Guard e escritor morto ímpar, reload com handles retidos e nova época. DLL final passa loader/exports sem SDK init. Regressões antigas e HUD V4 executadas; resultados/limitações no [relatório RC1.2](RC1_2_TELEMETRIA.md). ETS2/DLL instalada/hardware reais não foram executados pelo agente. Instalação permanece separada e aguarda autorização.

