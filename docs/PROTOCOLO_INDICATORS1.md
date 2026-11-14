Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# RC1.4 — Indicators1

Extensão opcional independente: `Local\ScaniaETS2TelemetryIndicators1`, mutex de mesmo nome com sufixo `.Guard`. Não altera os mapas V1/V2/V3/V4.1, Signals1 ou Jobs1. Registro opcional recusado pelo SDK resulta em indisponibilidade, sem abortar a telemetria principal.

## Contrato binário

Little endian, 256 bytes; magic 0x31444E49, versão 1.0. Offsets: magic 0, sequência 4, major/minor 8/10, tamanho 12; epochLo/Hi 16/24, geração 32, frame 40, publicação 48 (uint64); máscaras registered/valid/active 56/60/64, reservado zero 68 (uint32); 16 timestamps uint64 em 72; 56 bytes reservados zero em 200.

Publicador exclusivo vinculado à concessão V4; cópia protegida por mutex sem espera e sequência ímpar/par. Leitor exige identidade exata do snapshot V4, máscaras canônicas e timestamps entre início do frame e publicação, com idade inferior a 1500 ms. Pausa, troca de caminhão, ausência e valores inválidos anulam o estado. `false` válido significa apagado; `null` significa indisponível, nunca apagado presumido.

## Canais SDK (bits 0–15)

| Bit | Canal | Tipo |
|---|---|---|
| 0 | truck.light.parking | bool |
| 1 | truck.light.beam.low | bool |
| 2 | truck.light.beam.high | bool |
| 3 | truck.fuel.warning | bool |
| 4 | truck.adblue.warning | bool |
| 5 | truck.brake.retarder | u32, nível > 0 |
| 6 | truck.brake.motor | bool |
| 7 | truck.light.aux.front | u32, nível > 0 |
| 8 | truck.light.aux.roof | u32, nível > 0 |
| 9 | truck.light.beacon | bool |
| 10 | truck.brake.parking | bool |
| 11 | truck.oil.pressure.warning | bool |
| 12 | truck.water.temperature.warning | bool |
| 13 | truck.brake.air.pressure.warning | bool |
| 14 | truck.brake.air.pressure.emergency | bool |
| 15 | truck.battery.voltage.warning | bool |

Nomes e tipos verificados contra os cabeçalhos SDK locais 1.16. Auxiliares não significam necessariamente farol de neblina. Retarder indica nível selecionado, sem medição de torque. Setas usam Signals1; cruise usa o valor válido existente. ABS, estabilidade, injeção, portas e cinto permanecem indisponíveis na leitura real; teste visual explicitamente identificado pode acender qualquer símbolo.

O painel tradicional mostra apagados e indisponíveis distintamente. O automático omite os símbolos inativos sem reposicionar seus espaços durante piscadas. Espaço insuficiente é informado; não se inventa estado para preencher o painel. Testes: fixtures nativas/gerenciadas recíprocas, MMF real, identidade, expiração, máscaras e ausência da extensão.

