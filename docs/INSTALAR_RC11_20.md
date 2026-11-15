# Instalar e atualizar a RC11.20

Windows 11 x64, aplicativo 2.3.0.20, gerenciador 2.3.0.17 e DLL opcional 2.3.0.12. Runtime .NET 10.0.14 incluído. Candidata sem assinatura digital.

## Instalador

Confira o SHA256 da mesma release. Feche a versão anterior pela bandeja e encerre o observador ETS2. Execute `Scania_Haptics_RC11_20_Setup_x64.exe`. A instalação é por usuário, sem exigir administrador. Abrir o aplicativo e abrir o gerenciador ao terminar são opções desmarcadas.

O instalador preserva `config.json`, seu `.bak` e arquivos desconhecidos. Antes de substituir arquivos existentes, cria backups e confere SHA256. Arquivos bloqueados ou falha no backup interrompem a atualização. Nenhum processo é encerrado à força. A DLL não é copiada automaticamente para o jogo.

## Portátil

Extraia todo `Scania_Haptics_RC11_20_Portavel_win-x64.zip` em pasta nova e abra `ScaniaHaptics.exe`. Instalador e portátil usam as mesmas preferências em `%LOCALAPPDATA%/ScaniaHaptics/config.json` e a mesma proteção de instância única.

## Perfil inicial Meu cockpit

Somente quando não existem `config.json` nem `config.json.bak`, o aplicativo carrega o perfil **Meu cockpit**, com tema Nordic Light e os ajustes visuais do perfil fornecido pelo autor. Há uma cópia em `Profiles/Meu-cockpit.json`; o padrão efetivo também está incorporado ao executável, para não depender da alteração de um arquivo externo.

Reconhecimento e conexão da manopla e do G29/G923 são automáticos. **A conexão não constitui validação elétrica. Novas instalações começam no Nível 0**, que bloqueia a saída dos motores pelo contrato já existente no firmware. Após conferir alimentação, corrente, pinagem e montagem, escolha deliberadamente o nível apropriado no aplicativo. Base contínua e LEDs por RPM ficam desativados; simulação e testes exigem início explícito. A simulação não inicia automaticamente. Os valores de teste armazenados não são uma recomendação de PWM seguro.

Preferências existentes, incluindo conexão automática, nível, seleção de motores, tema e atalhos, são preservadas. Um backup existente é recuperado antes de considerar padrões. Configuração corrompida gera aviso e segue a recuperação anterior; não é substituída silenciosamente por Meu cockpit. Restaurar os padrões pela interface continua sendo uma ação explícita com os padrões internos anteriores; não reaplica automaticamente o perfil de primeira execução.

## DLL, retorno e desinstalação

Para instalar a DLL opcional, feche o ETS2 e use `ScaniaHaptics.PluginManager.exe`. Confira o destino `bin/win_x64/plugins/scania_haptics_v2.dll` na raiz escolhida. A DLL 2.3.0.12 é a mesma da release anterior e não precisa ser substituída para usar os novos temas. O gerenciador oferece backup e restauração.

Exporte seu perfil antes de mudar ajustes. Para retornar, feche a candidata e reinstale/abra a versão anterior; restaure o backup do aplicativo ou do perfil quando necessário. Backups do instalador ficam em `%LOCALAPPDATA%/ScaniaHaptics/InstallerBackups`.

A desinstalação preserva dados e backups por padrão. Na desinstalação interativa, a opção de apagar somente `config.json` e `.bak` exige escolha explícita; deixe a resposta padrão NÃO para conservá-los. O portátil pode ser mantido como alternativa de retorno.

Firmware, USB/HID, protocolos, pinagem e dependências não mudaram. Não há gravador, instalador de driver ou atualização automática de firmware. Testes em máquina Windows limpa, ETS2 real e hardware físico continuam pendentes. A validação do instalador foi feita em fixture isolada, com os mesmos arquivos e código de cópia/backup, sem atalhos ou Registro da instalação pessoal.
