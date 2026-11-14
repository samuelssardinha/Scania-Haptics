# Validação RC11.18 — 14/11/2026

App 2.3.0.18; gerenciador 2.3.0.17; DLL x64 2.3.0.12. Atualização visual: posição Topo/Parte inferior, restauração da HUD e padrões Topo, Dock compacta, Titanium e placas do limite/cruise.

| Suíte reexecutada | Grupos aprovados |
|---|---:|
| HUD completa, incluindo quatro grupos novos de posição/restauração | 169 |
| Temas, persistência e restauração | 12 |
| App STOP, isolamento e estados operacionais | 15 |
| **Total desta atualização** | **196** |

As três execuções finais terminaram com código 0 e nenhuma falha. A HUD inclui 6.000 frames de ensaio de recursos. Os testes usam transportes simulados e formulários de teste, sem COM física ou motores.

Os grupos novos verificam composição com painel, régua e luzes; três DPI, três layouts e monitores com origens negativas; posição e margens persistentes; atualização real da prévia sem serial; e restauração preservando todos os ajustes não visuais. As expectativas antigas de layout/tema padrão foram atualizadas para Dock/Titanium; cenários específicos de faixa agrupada passaram a selecionar esse layout explicitamente. A primeira regressão identificou essas premissas antigas e falta de ajuda em um botão novo; as correções foram verificadas na execução completa final.

App Release win-x64 autocontido compilado com SDK 10.0.403 e runtime 10.0.14. Instalador Inno Setup gerado com versão numérica 2.3.0.18, mesmo AppId e opções de atualização/backup anteriores; não foi executado na instalação pessoal. A DLL, o gerenciador, runtime, avisos e ícone foram reaproveitados do pacote anterior verificado. O portátil novo teve todas as 295 entradas verificadas e não contém fontes, objetos, configuração pessoal ou firmware.

Fonte privada compilada: `a830083d0dd55eb3e19c1ade91993c413c20ea5a`. Backup anterior com 397 arquivos comparados por SHA256; commit-base privado `727f2e5fdd5eb387b8c9cf540914dbe4c5aea753`. Os 39 arquivos protegidos de firmware/DLL, telemetria, serial, Logitech e instalador original permanecem iguais ao backup.

A DLL continua com SHA256 `BEC485B524300EED2C447642475A86E80E0AA677905DFA2AA1FEB2B2CC19405E`. Nenhum ensaio de DLL nativa ou firmware foi reexecutado nesta atualização visual; os resultados anteriores pertencem à RC11.17.

Não foram executados ETS2 real, instalação pessoal, gravação de firmware, alterações de Registro/dispositivos, motores ou LEDs físicos. O setup continua sem assinatura digital e a entrega permanece pré-lançamento. Conferir os SHA256 dos downloads da mesma release. Configurações antigas são preservadas; use os botões do novo menu para aplicar os novos padrões.
