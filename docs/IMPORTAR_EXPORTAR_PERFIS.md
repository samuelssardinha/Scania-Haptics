# Perfis completos — RC11.20

Abra **Importar / Exportar / Perfis → Perfis e backups** no aplicativo completo. O F9 permanece um painel passivo de ajustes rápidos; o botão de configurações completas abre o aplicativo, onde estão os seletores de arquivo.

## Exportar

1. Informe o nome do perfil, por exemplo, `Meu cockpit ideal`.
2. Clique em **Exportar todas as configurações…** e escolha um JSON fora do `config.json` ativo.
3. Guarde esse arquivo para reaplicar seus valores em futuras versões. Ao substituir uma exportação, o arquivo anterior fica em `.bak`.

A exportação serializa o objeto inteiro `AppSettings`; não usa uma lista parcial de opções visuais. Inclui HUD, componentes, fontes, legendas, formatos, posições/ordens, marchas personalizadas, régua, quantidade de LEDs, painéis, sons, volumes, alertas, cores, temas independentes, geometria, zoom do aplicativo, F9, atalhos, COM, motores, pista, calibração BASE, volante/G29/G923, preferências de inicialização, cenário, motor/ciclo do simulador e favoritos. Campos futuros de topo também são preservados.

Não exporta telemetria, estado de uma conexão, worker em execução, simulação ativa, contadores da sessão, conteúdo do Registro, executáveis, DLL ou firmware. Esses itens não são preferências do perfil. Sons personalizados ficam referenciados pelo caminho; o WAV externo precisa ser levado separadamente para outro computador.

## Formato versão 1

| Campo | Conteúdo |
|---|---|
| `Format` | `ScaniaHaptics.Profile` |
| `SchemaVersion` | Inteiro `1` |
| `ApplicationVersion` | Versão informacional do aplicativo exportador |
| `ExportedAtUtc` | Data/hora UTC em ISO 8601 |
| `ProfileId` | GUID da exportação |
| `ProfileName` | Nome, até 80 caracteres |
| `Settings` | Objeto completo `AppSettings`, com seus objetos aninhados |

Os nomes de propriedades existentes do `config.json` foram mantidos. Os campos novos são aditivos: as preferências dos materiais, fonte geral e cenários convivem com os ajustes antigos. O envelope do perfil é separado do formato interno de persistência.

Não há carregamento de tipos arbitrários nem execução de comandos a partir do JSON. O leitor exige objetos nos módulos principais, rejeita propriedades duplicadas, tipos incompatíveis, JSON truncado, profundidade superior a 64 níveis e arquivos acima de 4 MiB. Valores reconhecidos são normalizados pelos limites existentes e pelos limites dos controles gerais, com relatório das mudanças.

## Importar

1. Clique em **Selecionar e validar perfil / backup…**.
2. Leia o relatório. A seleção valida em memória antes de aplicar. Também aceita `config.json` legado, avisando da conversão; campos ausentes usam os padrões desta versão.
3. Clique em **Aplicar perfil validado** para substituir as preferências completas.

Antes da gravação, o aplicativo para os efeitos, termina a simulação, invalida operações antigas e desconecta a COM. Cria uma cópia `config.json.before-import-DATA-GUID.json` e compara o SHA256 da cópia com o original. A persistência usa o mecanismo atômico já existente: arquivo temporário no mesmo diretório, flush e replace; não há exclusão do arquivo anterior como alternativa à falha.

Somente após gravar com sucesso o objeto importado passa a ser o objeto ativo. Os controles são sincronizados com os eventos operacionais bloqueados. A reconexão COM automática fica suspensa até **Conectar / Desconectar**; o worker Logitech recebe as opções e uma desconexão/suspensão no mesmo bloqueio. Use os fluxos normais de conexão e **Retomar** explicitamente. A importação não inicia a simulação.

Preferências de inicialização com Windows/ETS2 e localização do aplicativo para a DLL são transportadas, mas não alteram o sistema durante a importação. Os fluxos existentes as aplicarão no próximo início do aplicativo. O atalho da HUD e o F9 são atualizados pelos mesmos gerenciadores existentes; conflitos são informados, preservando o registro anterior quando possível.

Para esquema futuro, o leitor aplica os campos reconhecidos e informa a diferença de versão. Campos futuros de topo são preservados como extensão; campos aninhados sem suporte não são aplicados e aparecem no relatório. Não prometa equivalência completa com funcionalidades de uma versão futura desconhecida.

## Restaurar

**Restaurar configuração anterior à importação** seleciona o backup anterior desta sessão para validação. Depois clique em **Aplicar perfil validado**. Em outra sessão, selecione diretamente um `.before-import-….json` ou um `config.json.bak` copiado para arquivo `.json`. Restaurar também cria um backup do estado que será substituído.

Se validação, cópia, comparação de integridade ou gravação falhar, o relatório informa a falha. Falhas de gravação não substituem o objeto ativo nem removem o arquivo anterior. Depois de iniciada a aplicação, as saídas podem permanecer desconectadas/paradas por segurança; o relatório não promete mantê-las operando durante uma falha de importação.

## Validação

Exportação completa, roundtrip, compatibilidade, falhas injetadas, backup/restauração e importação pela interface foram verificados com arquivos e transportes de teste. Consulte [VALIDACAO.md](VALIDACAO.md).
## Perfil inicial distribuído

A RC11.20 inclui Meu cockpit como padrão somente sem config.json e sem backup. Conecta automaticamente a manopla e o G923, mantendo os motores no Nível 0 até validação. Não inicia testes ou simulação. Configuração existente é preservada. Consulte [instalação e atualização](INSTALAR_RC11_20.md).
