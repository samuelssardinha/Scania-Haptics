# Validação da publicação — 14/11/2026

Versão candidata RC11 Completa: App/gerenciador 2.3.0.17; DLL x64 2.3.0.12. Resultados abaixo foram executados nesta sessão com fontes locais e fixtures isoladas. Não incluem instalação pessoal, ETS2 ou hardware.

| Suíte | Grupos aprovados | Ambiente |
|---|---:|---|
| Firmware host | 19 | Sketch real compilado com stubs, sem ESP32 |
| App STOP/isolamento | 15 | Transportes simulados |
| Serial | 10 | Worker real, transporte simulado, sem COM física |
| Temas/persistência | 12 | Após correção do teste das cores do sistema |
| HUD | 165 | Renderização e formulários com doubles; inclui 6.000 frames de teste |
| Mapas legados MMF | 4 | Mapas isolados |
| Instância única | 1 | Processos de teste |
| LEDs G29/G923 | 15 | Transporte simulado |
| Régua RPM | 19 | Renderização e limites |
| Serviço de plugin | 14 | Diretórios e PE sintéticos, sem jogo real |
| Contratos nativos/C#, extensões e carregamento DLL | 21 | Headers SDK locais, fixtures recíprocas e DLL real |
| **Total** | **295** | **Nenhum desses grupos terminou com falha** |

A execução inicial parou no teste que aplicava contraste >=4,5 às cores escolhidas pelo Windows. Nesta sessão, Highlight=#0078D7 e HighlightText=#FFFFFF produzem razão ligeiramente inferior a 4,5. O teste foi corrigido para exigir preservação das cores de acessibilidade do sistema; o contraste mínimo continua exigido das paletas escolhidas pelo aplicativo. A suíte de temas foi reexecutada e passou. Não houve alteração das cores do App ou recompilação dos binários distribuídos.

Na primeira chamada do smoke test da DLL foi informado um caminho inexistente na pasta histórica da release. Após extrair a DLL real do portátil verificado e informar seu caminho correto, o carregamento/exports e a suíte completa passaram. Essa tentativa inicial não foi contabilizada como aprovação.

## Integridade

- Hashes de todos os arquivos da release de origem conferidos.
- Todos os fontes/recursos listados no manifesto de compilação correspondem ao workspace atual por SHA256; conferido também o conjunto de arquivos de produção, excluindo objetos gerados.
- Portátil: 294 entradas; as 293 entradas do manifesto foram lidas e verificadas individualmente. O arquivo adicional é o próprio manifesto SHA256.
- Setup e portátil preservam os bytes originais. DLL nativa e ícone estão no portátil e no payload do setup segundo os manifestos e a receita verificada. A DLL separada foi extraída desse mesmo portátil, não de uma pasta build antiga.
- DLL real: SHA256 `BEC485B524300EED2C447642475A86E80E0AA677905DFA2AA1FEB2B2CC19405E`.
- Ícone: SHA256 `920D88139D7434AEF561AB772113738BCA93346F62194B4DB0792674A50E77BA`.
- Foram excluídos fontes próprios, bundles Git, PDB/OBJ/LIB/EXP, configuração pessoal, firmware e logs brutos dos arquivos públicos.

O relatório anterior de 13/11/2026 registra testes de instalador em fixture; é evidência histórica, não reexecução nesta sessão. O instalador de produção não foi executado aqui. Build metadata preserva o commit privado original: `3f1d92380a33b0e450604ebbaf790331b7e131e4`.

## Pendências manuais

Validar instalação/atualização em Windows limpo; janela/bandeja/F9 e DPI reais; recepção dos canais na versão instalada do ETS2; LEDs por modelo; STOP/watchdog/desconexão e calibração; tensão, corrente e temperatura da montagem, especialmente as cargas paralelas. A candidata não recebe classificação Stable por testes sintéticos.

Fontes e evidências completas permanecem no workspace privado. Nenhum driver, Registro, biblioteca global, firmware ou instalação pessoal foi alterado durante esta preparação.
