Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# Vehicle1 — extensão opcional RC11

Autorização de 13/11/2026: o usuário aprovou a extensão DLL incluindo pressão e temperatura. Fonte da verdade: `DLL/telemetry_vehicle.h`, integração em `DLL/scania_haptics_v2.cpp`, cabeçalhos do SDK oficial. Não altera firmware, comandos seriais, exports ou estruturas V1/V2/V3/V4.1/Signals1/Jobs1/Indicators1/Instruments1.

Mapa `Local\ScaniaETS2TelemetryVehicle1`; mutex `Local\ScaniaETS2TelemetryVehicle1.Guard`. Publicador vinculado à concessão exclusiva V4 existente. Little endian, 256 bytes, `#pragma pack(1)` e alinhamento de 8 bytes. Versão 1.0, magic `0x31484556` (VEH1).

| Offset | Tipo / tamanho | Campo |
|---:|---|---|
| 0 | uint32 | magic |
| 4 | uint32 | sequência ímpar durante escrita / par estável |
| 8 / 10 | uint16 / uint16 | major=1 / minor=0 |
| 12 | uint32 | bytes=256 |
| 16 / 24 | uint64 / uint64 | epochLo / epochHi |
| 32 / 40 / 48 | uint64 cada | geração do caminhão / frame / publicação |
| 56 / 60 / 64 | uint32 cada | registered / valid / active |
| 68 | uint32 | reservado zero |
| 72 / 76 | float32 cada | pressão de ar / óleo, psi |
| 80 / 84 | float32 cada | temperatura de água / óleo, °C |
| 88–159 | 9 × uint64 | timestamp por canal, na ordem dos bits |
| 160–255 | 96 bytes | reservados zero |

## Canais e semântica

| Bit | Canal oficial SDK | Tipo | Interpretação |
|---:|---|---|---|
| 0 | `truck.differential_lock` | bool | Bloqueio diferencial engatado |
| 1 | `truck.lift_axle` | bool | Controle do eixo do caminhão selecionado para elevar |
| 2 | `truck.lift_axle.indicator` | bool | Lâmpada do eixo do caminhão acesa |
| 3 | `truck.trailer.lift_axle` | bool | Controle do eixo do reboque selecionado para elevar |
| 4 | `truck.trailer.lift_axle.indicator` | bool | Lâmpada do eixo do reboque acesa |
| 5 | `truck.brake.air.pressure` | float | Pressão no reservatório pneumático, psi |
| 6 | `truck.oil.pressure` | float | Pressão de óleo, psi |
| 7 | `truck.water.temperature` | float | Temperatura de água, °C |
| 8 | `truck.oil.temperature` | float | Temperatura de óleo, °C |

Nomes/tipos comparados aos macros oficiais nos testes nativos; unidades confirmadas nos comentários dos cabeçalhos SDK. Nenhuma conversão implícita para bar. Seleção e lâmpada são independentes: a lâmpada não mede a altura física do eixo. Não inferir ABS/TCS/ACC ou frenagem efetiva por esses dados. Avisos mecânicos booleanos continuam vindo dos canais anteriores, sem deduzir limiares novos das medidas numéricas.

Registro individual com `EACH_FRAME | NO_VALUE`, índice NIL. Recusa de canal é opcional: limpa registered correspondente, sem impedir fonte principal/legada. Ausência da extensão ou DLL antiga produz `Vehicle=null`, indicadores `?` e medidas `—`; nunca zero inventado.

## Validação e sincronismo

Máscaras aceitas: registered até bit 8, valid subconjunto de registered, active subconjunto dos bits booleanos válidos (0–4). Float sem valid deve conter zero canônico e timestamp zero. Booleano inválido não pode manter active/timestamp. `false` válido e medida `0` válida diferem de indisponível. Tipos errados, null, bool fora de 0/1, NaN/infinitos, pressão negativa e temperatura abaixo de −273,15 °C invalidam somente o campo. Não se impõe um teto arbitrário para valores do SDK.

Frame start, configuração, pausa, estado incompleto, troca/ausência de caminhão e shutdown invalidam dados dinâmicos. Publicação carrega a mesma identidade epoch/geração/frame/tick do V4; frames incompatíveis são descartados pelo leitor. Timestamps devem pertencer ao intervalo do frame e ter idade inferior a 1500 ms. O modelo imutável reavalia validade ao consumir o snapshot, sem renovar idade por atualizar a tela.

Escrita sob mutex com espera zero, sequência ímpar/cópia/barreira/par. Leitura sob o mesmo mutex com espera zero e conferência de sequência antes/depois. Contenção ou mutex abandonado retornam indisponibilidade temporária, preservando o snapshot V4 principal. A publicação V4 precede as extensões: uma leitura entre publicações pode omitir Vehicle1 nesse ciclo; não combina frames nem bloqueia a UI para aguardar o próximo.

## Aplicativo e testes

Modelo `TelemetryVehicle` em `TelemetryV4Extras.cs`; leitor `TelemetryV4VehicleReader`; projeção `HudFrame.Vehicle`. Painel existente: diferencial, lâmpadas dos eixos e seleções independentes. Dois instrumentos opcionais: **Pressões de ar / óleo** (bit 1024) e **Temperaturas de água / óleo** (bit 2048). IDs/ordenação/legendas/fontes/posição são próprios. Flags antigas Legacy=127, All=511 e Extended=1023 mantidos; Available=4095 inclui os novos instrumentos.

Cobertura: fixtures binárias C++→C# e C#→C++, static_assert de offsets/tamanho/alinhamento, tipos/nomenclatura do SDK, callbacks, valores e unidades, null/zero/false, domínios inválidos, timestamps/identidade/reservados, pausa/configuração/shutdown, recusa de todos os canais novos, comparação dos mapas anteriores, MMF real isolada, contenção/ímpar/recuperação e cliente novo com fonte anterior sem Vehicle1. DLL compilada x64 `/MT /W4 /WX` e loader/exports verificados sem inicializar SDK real. Resultados finais no pacote de evidências.

Homologação no ETS2: conferir disponibilidade por caminhão/reboque, selecionar/desselecionar eixos e comparar lâmpadas, observar pressões e temperaturas em psi/°C, pausar/trocar caminhão/desligar o jogo e verificar invalidação. Não foi executada pelas ferramentas. A DLL compilada não é instalada automaticamente.

