Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# RC1.4.1 — Instruments1 opcional

Auditoria: SDK oficial 1.16 local. `truck.brake.retarder` é u32 de 0 ao máximo `retarder.steps` (u32 na configuração truck). `truck.brake.motor` e `truck.wipers` são bool, sem estágio. `truck.fuel.consumption.average` é float em **litros/km**, não km/l. O primeiro já era registrado por Indicators1; reutiliza-se seu callback, sem registro concorrente. Novos registros opcionais: consumo médio e limpador. Nenhuma falha desses registros aborta a telemetria principal.

## Layout e sincronismo

Mapa `Local\ScaniaETS2TelemetryInstruments1`, 192 bytes little-endian. Mutex `Local\ScaniaETS2TelemetryInstruments1.Guard`. Publicador vinculado à concessão exclusiva V4 existente; nunca há segundo publicador paralelo.

| Offset | Campo | Tipo |
|---|---|---|
| 0 / 4 | magic 0x31534E49 / sequência | u32 |
| 8 / 10 / 12 | major 1 / minor 0 / bytes 192 | u16 / u16 / u32 |
| 16 / 24 | epochLo / epochHi | u64 |
| 32 / 40 / 48 | geração caminhão / frame / publicação | u64 |
| 56 / 60 | registered / valid | u32 |
| 64 / 68 | nível retarder / máximo configurado | u32 |
| 72 / 76 | consumo médio L/km / limpador (0 ou 1) | float / u32 |
| 80 / 88 / 96 / 104 | timestamps dos quatro campos | u64 |
| 112 | reservado zero | 80 bytes |

Bits 0/1/2/3 correspondem a nível/máximo/consumo/limpador. Ausência tem payload/timestamp zero e bit desligado. Zero de nível e máximo zero são válidos, mas máximo zero não autoriza gráfico de estágios. Consumo médio zero, negativo, NaN ou infinito é inválido. Nível acima do máximo permanece observação bruta, sem exibição de estágios.

Cada cópia usa mutex sem espera e sequência ímpar/par. O leitor exige magic/versão/tamanho, reserved zero, máscaras canônicas e identidade **exatamente igual** ao snapshot V4 aceito (epoch, geração, frame e publicação). Campos dinâmicos precisam de timestamp entre início do frame e publicação e idade inferior a 1500 ms. Máximo configurado permanece estático na mesma geração, mas nunca sobrevive à perda da fonte.

FRAME_START e pausa anulam os dinâmicos; configuração truck limpa o máximo anterior e só aceita uma ocorrência tipada/não indexada de retarder.steps, com busca limitada a 4096 atributos. Mudança de caminhão, ausência de truck e shutdown invalidam o que não pertence à nova fonte. Mutex ocupado, writer abandonado, extensão ausente ou frame não correspondente resultam em indisponibilidade, sem bloquear o mapa principal.

Não há mudança de tamanho, offsets ou semântica em V1/V2/V3/V4.1, Signals1, Jobs1 ou Indicators1. DLL antiga continua compatível: nível/médio/limpador novos ficam indisponíveis, retarder e freio motor mantêm o estado booleano existente; velocidades, cruise e consumo recente baseado nos campos V4 antigos continuam opcionais.

Testes nativos verificam nomes/tipos contra os headers oficiais, níveis 0..5, callbacks, configuração, pausa, valores inválidos e imutabilidade V4. Fixture de 192 bytes é decodificada em C#; testes incluem MMF Win32 real, extensão ausente, identidade errada, timestamp futuro, expiração, campos reservados e sequência ímpar. Não houve execução dessa DLL no ETS2 durante o desenvolvimento.

