# Créditos e licenças de componentes

Copyright © 2026 Samuel Silva Sardinha — Scania Haptics. Contato autorizado: +55 71 99697-3010, https://wa.me/5571996973010.

**Atualização de 10/11/2026: código original proprietário e fechado; uso gratuito decidido. EULA em minuta, sujeita à aprovação.** Não foi atribuída automaticamente MIT, GPL ou outra licença ao projeto. Os textos abaixo tratam somente de componentes de terceiros; não concedem direitos sobre o conjunto do projeto, nome ou arte.

| Componente | Versão nesta candidata | Licença/condição e uso |
|---|---|---|
| .NET Runtime | 10.0.14 win-x64 | MIT e avisos próprios de terceiros, incluídos em avisos/ |
| Windows Forms / WindowsDesktop | 10.0.14 win-x64 | Licença do pacote Microsoft incluída em avisos/ |
| System.IO.Ports e runtime.native | 10.0.14 | MIT e avisos do pacote incluídos; atualizado na migração .NET 10 |
| APIs Windows/GDI+ | Plataforma Windows | APIs do sistema; não se distribui o Windows |
| MSVC | Compilador x64 local | Uso/redistribuição sujeitos aos termos Microsoft/Visual Studio; nenhum instalador VC Redistributable é incluído |
| Microsoft STL | Headers/libs usados pelo MSVC 14.52 | Apache-2.0 WITH LLVM-exception, copyright Microsoft Corporation; texto integral em avisos/MSVC_STL_LICENSE.txt |
| SCS Telemetry SDK | Headers 1.16 nos testes | Usado para conferir ABI/canais; headers oficiais não são incluídos nos ZIPs de distribuição |
| Arduino-ESP32 | 3.3.12 | LGPL-2.1; fonte/core e obrigações de ligação precisam integrar a revisão antes da distribuição de binários de firmware |
| ESP-IDF/TinyUSB e toolchain | Dependências do core 3.3.12 | Licenças por componente; bibliotecas/bootloader/toolchain não são redistribuídos nesta candidata |

As notas e textos copiados dos pacotes .NET/System.IO.Ports estão em `avisos/` nos ZIPs e em `distribuicao/legal/avisos/` no repositório. Esta candidata inclui o aviso Visual C++ e a licença Microsoft STL; não inclui firmware, sketch, objetos de religação ou ferramentas de gravação.

Fontes primárias para revisão: [Arduino-ESP32 3.3.12 — licença](https://github.com/espressif/arduino-esp32/blob/3.3.12/LICENSE.md), [redistribuição MSVC](https://learn.microsoft.com/en-us/cpp/windows/redistributing-visual-cpp-files), [.NET — suporte](https://dotnet.microsoft.com/en-us/platform/support/policy), [System.IO.Ports](https://www.nuget.org/packages/System.IO.Ports/10.0.14).

O feed NuGet foi consultado para dependências diretas/transitivas em 09/11/2026, sem pacotes vulneráveis reportados naquele resultado. Isso não equivale a garantia de ausência de vulnerabilidades e deve ser repetida para atualizações futuras. A primeira consulta restrita à sandbox falhou por rede; a consulta pública posterior concluiu e foi registrada na auditoria interna.

Projeto independente e não oficial. Scania, Volvo, SCS Software e Logitech são nomes de terceiros e não patrocinam, certificam ou endossam o projeto. O nome/artes ainda precisam de decisão antes da publicação. O ícone original do projeto foi habilitado por solicitação expressa do autor para esta candidata local, sem alterar a imagem original.

A revisão NET10 e os inventários anteriores documentam o histórico. Em 11/11/2026 o autor confirmou desenvolvimento individual, aceitou os termos do Community e autorizou sua instalação estável; a DLL nova foi recompilada e incluída opcionalmente após os testes. O código próprio permanece proprietário e gratuito, com minuta ainda sujeita à aprovação final. Firmware permanece fora do pacote. Nenhum fonte próprio é distribuído.

Estado atual e termos exatos: AUDITORIA_REDISTRIBUICAO.md e distribuicao/legal/avisos/. Referências anteriores a ZIPs de fontes descrevem o histórico local, não a nova distribuição proprietária.
