Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# Signals1 — extensão opcional da RC1.3

Fonte: headers do SDK oficial 1.16 em tests/outputs/rc12/sdk/include, common/scssdk_telemetry_truck_common_channels.h:442–488. [SDK oficial e documentação](https://modding.scssoft.com/wiki/Documentation/Engine/SDK/Telemetry). `truck.lblinker` e `truck.rblinker` são comandos contínuos, não fase luminosa. `truck.light.lblinker` e `truck.light.rblinker` são lâmpadas acesas. `truck.hazard.warning` é comando do pisca-alerta, acrescentado na versão **de dados do jogo 1.17** (não confundir com a versão 1.16 do pacote SDK ou a ABI de inicialização 1.00). Todos são bool, índice NIL.

Os cinco canais são registrados EACH_FRAME|NO_VALUE. Falha de registro é opcional, não ativa valor substituto, não impede V4.1 ou legado. A disponibilidade efetiva retornada pelo jogo decide o suporte; não se deduz pela versão comercial do ETS2. Null, tipo incorreto ou bool fora de 0/1 invalidam individualmente. A máscara dinâmica é limpa a cada FRAME_START; pause/configuração/shutdown invalidam os sinais. O HUD usa **somente as duas lâmpadas**, inclusive no pisca-alerta; comandos são observações diagnósticas, nunca geram fase artificial ou luz presumida.

Nenhum tamanho, offset ou significado V1/V2/V3/V4.1 é alterado; DLL/telemetry_v4.h permanece idêntico. Nome novo: `Local\ScaniaETS2TelemetrySignals1`, 128 bytes, little endian, versão 1.0, leitura apenas. Proteção `...Signals1.Guard` com tentativa imediata, paridade de sequência e cópia integral. O publicador é subordinado à lease exclusiva V4.Writer já existente. Um segundo plugin recusado não toca o mapa novo.

| Offset | Campo | Tipo |
|---|---|---|
| 0 / 4 | magic SIG1 (0x31474953) / sequência par | u32 / u32 |
| 8 / 10 / 12 | major=1 / minor=0 / bytes=128 | u16 / u16 / u32 |
| 16 / 24 | epochLo / epochHi da V4.1 | u64 / u64 |
| 32 / 40 / 48 | geração truck / frame / published da V4.1 | u64 cada |
| 56 / 60 | registered / valid, bits 0–4 | u32 cada |
| 64–68 | left command, right command, left lamp, right lamp, hazard command | bool u8 cada |
| 69–71 | zero | reservado |
| 72 / 80 / 88 / 96 / 104 | timestamp de cada sinal, GetTickCount64 | u64 cada |
| 112–127 | zero | reservado |

Vínculo exige época, geração, frame e published **iguais** ao snapshot V4.1 aceito. Cada tick deve estar entre frameStarted e published e ter idade <1500 ms; fonte também <1500 ms. Não se renova frescor ao renderizar. False válido é distinto de desconhecido. Campos inválidos são canonicamente zero; reservados/versão/máscaras/seq ímpar incorretos rejeitam o pacote. Reader nunca escreve.

As publicações dos dois mapas são sequenciais, não uma transação única: se um leitor atravessar o limite entre frames, descarta a extensão desencontrada e conserva V4.1; uma lâmpada pode desaparecer por um tick, jamais ser associada a outra época/frame. Contenção no mapa novo também resulta em extensão ausente sem bloquear ou retirar dados V4.1. DLL antiga + app novo: setas indisponíveis. DLL nova + app antigo: mapa novo ignorado. Legado continua independente.

