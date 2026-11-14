# Scania Haptics

Telemetria, HUD e feedback háptico para Euro Truck Simulator 2. Aplicativo Windows independente desenvolvido por **Samuel Silva Sardinha**.

**Versão atual: v2.3.0 RC11 Completa — App 2.3.0.17, DLL 2.3.0.12. Pré-lançamento para validação.**

[Baixar versões](https://github.com/samuelssardinha/Scania-Haptics/releases) · [Instalação e uso](docs/GUIA_ATUAL.md) · [Validação](docs/VALIDACAO.md)

## Instalação rápida

1. Baixe `Scania_Haptics_RC11_Completo_Setup_x64.exe` e confira o SHA256 em `SHA256SUMS.txt`.
2. Feche a versão anterior, inclusive pela bandeja. Execute o instalador no Windows 11 x64. O runtime .NET já está incluído.
3. Para telemetria, feche o ETS2 e abra **Gerenciar plugin ETS2 e restaurar DLL anterior**. Escolha a pasta do jogo e confirme o destino da DLL; o gerenciador preserva a anterior para restauração.
4. Abra o App e configure HUD, porta e dispositivos. Antes de atuar nos motores, confira limites elétricos e comece com intensidade baixa. A simulação pode atuar em dispositivos conectados.

O setup inclui a DLL x64, gerenciador, ícone e avisos de terceiros. Ele não instala a DLL no jogo automaticamente. Há também versão portátil; extraia o ZIP inteiro em uma pasta nova, sem executar dentro do ZIP.

## Recursos

- HUD adaptativa: desativar um componente remove também o espaço ocupado.
- Nove temas de HUD, modos por componente, texto/dígitos, placas de cruise e régua RPM.
- Instrumentos de velocidade, RPM, marcha, combustível, consumo, trabalho e indicadores conforme a telemetria disponível.
- Editor no desktop, simulação, hotkeys, bandeja e preferências persistentes.
- Dois canais hápticos com seleção, inversão, efeitos e testes isolados.
- Integração de LEDs Logitech G29/G923 conforme modelo e configuração; validação física ainda necessária.
- DLL com mapas legados V1/V2/V3, V4.1 e extensões opcionais, incluindo Vehicle1.

## Conteúdo da release

| Arquivo | Conteúdo |
|---|---|
| `Scania_Haptics_RC11_Completo_Setup_x64.exe` | Instalador por usuário, App, runtime, gerenciador, DLL e ícone |
| `Scania_Haptics_RC11_Portavel_win-x64.zip` | Aplicativo completo para extração em pasta nova |
| `Scania_Haptics_RC11_DLL_x64.zip` | DLL, manifesto e avisos necessários; preferir instalação pelo gerenciador |
| `Scania_Haptics_Icones.zip` | Ícone e imagem usados pelo projeto |
| `Scania_Haptics_Documentacao.zip` | Este guia e documentação técnica |
| `SHA256SUMS.txt` | Hashes dos arquivos desta publicação |

Este repositório distribui **binários e documentação**. Os fontes originais permanecem privados; os arquivos automáticos “Source code” do GitHub contêm somente o conteúdo público deste repositório.

## Limites e créditos

Esta candidata não é uma certificação de hardware. Não foram executados ensaios físicos nem ETS2 durante a preparação de 14/11/2026. O setup não tem assinatura digital. Não há firmware ou atualizador USB nesta distribuição.

Pista é estimada pela telemetria; não é leitura do force feedback do volante. Canais ausentes aparecem como indisponíveis. Um motor pequeno em paralelo ao segundo canal acompanha esse canal e não tem controle independente. PWM não regula a tensão de pico nem substitui proteção de corrente.

Código original proprietário, com distribuição gratuita autorizada pelo autor. Nenhuma licença aberta foi aplicada aos fontes. O texto de licença incluído ainda se identifica como minuta; não se presume aprovação do prazo de suporte proposto. Componentes de terceiros mantêm suas próprias licenças, em `avisos/` nos pacotes.

Projeto independente e não oficial, sem afiliação, patrocínio ou endosso de Scania, SCS Software, Logitech ou demais fabricantes citados. As marcas pertencem aos respectivos titulares; a inclusão de arquivos de arte não constitui concessão de direitos sobre marcas.

Copyright © 2026 Samuel Silva Sardinha. [Contato público](https://wa.me/5571996973010).
