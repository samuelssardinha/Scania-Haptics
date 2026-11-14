Nota da publicacao: este documento descreve contratos tecnicos e seu historico. Para versoes atuais, instalacao e limites, consulte [GUIA_ATUAL.md](GUIA_ATUAL.md). Referencias a fontes ou relatorios privados nao fazem parte do repositorio publico.

# Jobs1 — RC1.3.1

Mapa opcional `Local\ScaniaETS2TelemetryJobs1`, proteção `...Jobs1.Guard`, 1024 bytes little endian. Nenhum byte/offset/semântica dos mapas V1/V2/V3/V4.1/Signals1 foi alterado. Publicador subordinado à lease V4.Writer; não há COM ou comandos novos. Versão 1.0, magic JOB1 (0x31424F4A).

| Offset | Tipo | Campo |
|---|---|---|
| 0 / 4 | u32 | magic / sequência par |
| 8 / 10 / 12 | u16/u16/u32 | major 1 / minor 0 / bytes 1024 |
| 16 / 24 | u64 | época V4 lo/hi |
| 32 / 40 / 48 | u64 | geração truck / frame / published V4 |
| 56 / 64 | u64 | geração de configuração / geração job |
| 72 / 76 | u32 | máscara válida / estado |
| 80 | u64 | receita prevista |
| 88 / 92 / 96 / 100 | u32 | distância planejada km / reboques / moeda / zero |
| 104 | u64 | instante da configuração GetTickCount64 |
| 112 / 272 | char[160] | origem / destino |
| 432 / 624 | char[192] / char[384] | carga / resumo dos reboques |
| 1008 | byte[16] | zero |

Máscara: origem bit0, destino bit1, carga bit2, receita bit3, distância bit4, reboques bit5. Estado: bit0 job ativo, bit1 callback gameplay registrado, bit2 situação job conhecida por callback/configuração; demais bits zero. Sem suporte ao callback gameplay, ou antes de observar a situação job, app ignora Jobs1. Ausência de callback não significa ausência de trabalho. DLL e V4.1 continuam operacionais.

Strings UTF-8 estrito, terminadas em NUL, resto zero. Origem/destino/carga excedentes são descartados como desconhecidos, não copiados por aproximação. Resumo de múltiplos reboques tem truncamento UTF-8 seguro e `...`; máximo dez slots. Controles/UTF-8 inválido e duplicação de atributo invalidam o respectivo campo. Tipos errados não são convertidos implicitamente. Ausência é null/—, não zero inventado.

Leitura: mutex com tentativa imediata, sequência par antes/depois e cópia privada. Época, geração truck, frame e published devem coincidir exatamente com V4.1; fonte <1500 ms, sem renovação ao renderizar. Jobs mantém atributos estáticos enquanto a fonte está viva; configuração pode ter idade maior que um frame. Gerações permitem distinguir substituição/invalidação. Em transição ou contenção, retorna extensão ausente sem bloquear ou retirar a V4.1. DLL antiga não oferece Jobs1, portanto dados de trabalho ficam indisponíveis e podem ser ocultados.

## SDK auditado

Headers oficiais SDK 1.16 locais, `common/scssdk_telemetry_common_configs.h`, `common/scssdk_telemetry_common_gameplay_events.h`, `scssdk_telemetry_event.h` e `scssdk_value.h`. [Fonte oficial](https://modding.scssoft.com/wiki/Documentation/Engine/SDK/Telemetry). Testes nativos comparam constantes e ABI ao SDK.

- `job`: `source.city`, `destination.city`, `cargo` são strings UTF-8. `income` é **u64**, receita prevista sem penalidades. `planned_distance.km` é **u32**, distância planejada em km simulados. Os nomes com pontos são os atributos reais; nomes das macros em snake_case não são strings do protocolo.
- `trailer` equivale a `trailer.0`; `trailer.0`…`trailer.9` são slots, não dez cargas. Coleta `brand`, `name`, `body.type`; não usa `cargo.accessory.id` como se fosse modelo de reboque. Alias não duplica a contagem. Slots vazios removem o reboque. Resumo conserva as descrições disponíveis; identificação ausente é explicitada.
- Configuração `job` vazia remove o trabalho. Qualquer configuração job substitui integralmente os dados, incrementando geração. Entrega/cancelamento nos eventos `job.delivered`/`job.cancelled` (evento SDK gameplay=6) limpa imediatamente Jobs1, sem publicar/modificar artificialmente a V4.1.
- Troca de truck também invalida Jobs1. Por cautela, toda configuração job descarta os reboques anteriores; exige callbacks de trailer subsequentes. Se o jogo enviar trailer somente antes do job, o resumo ficará indisponível até novo callback. Não se reutiliza descrição possivelmente pertencente ao trabalho anterior.
- ETS2 `game_id=eut2` usa **EUR interno do SDK**, ATS `ats` usa USD; outro game_id é moeda desconhecida. Não há atributo auditado com a conversão monetária de apresentação selecionada pelo jogador. Valores são identificados `EUR (SDK)`/`USD (SDK)` ou `unid. SDK (moeda desconhecida)`, nunca presumidos em reais. Não são receita final ou moeda convertida da interface.
- Valor/km = receita prevista positiva / distância planejada positiva, calculado com decimal no app. Não usa navegação restante, hodômetro ou distância efetivamente percorrida.

## Testes

Nativo: ABI gameplay/config, tipos u64/u32/string, nomes oficiais, job→trailer e múltiplos slots/alias, entrega/cancelamento sem tocar V4, troca, empty, moeda desconhecida/EUR, limites/UTF-8/tipos inválidos; fixture binária para C#. Gerenciado: offsets/UTF-8/razão, ausente versus inativo, validações de cabeçalho, época/frescor, MMF real, sequência ímpar/recuperação. Integração e apresentação descritas em RC1_3_1_HUD.md.

