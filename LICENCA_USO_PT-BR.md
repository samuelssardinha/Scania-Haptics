# Licença de uso — Scania Haptics v2.3.0

**MINUTA FINAL PARA APROVAÇÃO DO AUTOR — NÃO VIGENTE.** Consolidada em 11/11/2026 para a candidata com instalador Windows. Modelo e permissões abaixo foram escolhidos pelo autor; a redação final ainda depende de sua aprovação antes da publicação. A inclusão no instalador de teste não constitui liberação Stable. Esta minuta não é parecer jurídico.

## 1. Titular e objeto

Samuel Silva Sardinha é o titular declarado dos direitos sobre o código original do Scania Haptics. Copyright © 2026 Samuel Silva Sardinha. Contato público: +55 71 99697-3010, https://wa.me/5571996973010.

O produto é software independente de telemetria, instrumentação e feedback háptico para uso com Euro Truck Simulator 2. Este instrumento se refere aos binários originais expressamente incluídos na distribuição aprovada, não ao repositório de desenvolvimento. Componentes retidos ou não incluídos não são considerados licenciados para distribuição por esta minuta.

## 2. Uso gratuito e propriedade

Após aprovação e entrada em vigor, o titular concederá licença gratuita, não exclusiva, para instalar e executar os binários legitimamente obtidos para uso pessoal e profissional, inclusive em atividades comerciais, sem cobrança de licença. A licença não transfere titularidade, direitos sobre marcas ou propriedade intelectual.

O código original permanece proprietário e fechado. Não se concede acesso público aos fontes C#, C++ ou sketch próprio, nem se aplica MIT, GPL, LGPL ou outra licença aberta ao código original por este instrumento. Materiais de terceiros e objetos eventualmente fornecidos para conformidade conservam seus regimes próprios. Direitos obrigatórios de terceiros e do usuário prevalecem nas hipóteses aplicáveis.

## 3. Instalação e utilização

O usuário deve obter pacotes autênticos, conferir versão e hashes, preservar suas configurações e seguir as instruções específicas. A instalação da DLL, quando incluída e liberada, é opcional e depende de confirmação do destino. Esta distribuição não inclui atualizador, ferramentas de gravação ou binário de firmware. O jogo e o Windows devem ser obtidos e utilizados conforme suas próprias licenças. O usuário deve respeitar limites elétricos e mecânicos documentados para sua montagem.

São permitidas cópias de segurança e redistribuição gratuita de cópias íntegras dos pacotes cuja publicação tenha sido autorizada pelo titular, com autoria, licença e avisos preservados. Não cobrar pelo pacote como se concedesse licença própria, remover avisos ou apresentar o software como obra de outra pessoa. Esta permissão não publica candidatas internas, não autoriza distribuir fontes próprios e não restringe direitos concedidos por terceiros ou pela legislação.

## 4. Atualizações e suporte

Versão abrangida: v2.3.0; estado atual: Release Candidate, sem validação física final. Canal de contato: WhatsApp público acima. Propõe-se validade técnica desta versão até 14/11/2028, condicionada aos requisitos e versões de Windows/ETS2/hardware documentados na distribuição aprovada, com manutenção dos patches de dependências necessários. O prazo e o compromisso de suporte ainda dependem da aprovação final do autor. Não há expiração automática da licença de uso já concedida ao fim desse período. Não se promete atendimento ininterrupto, prazo de resposta fixo, atualização automática ou compatibilidade ilimitada com versões futuras. Obrigações legais de assistência permanecem preservadas.

As condições finais não poderão afastar assistência, garantias ou responsabilidades obrigatórias previstas na legislação aplicável. Avaliar especificamente os arts. 7º e 8º da Lei 9.609/1998 e a incidência das demais normas aplicáveis ao modo concreto de distribuição; a gratuidade não autoriza presumir a exclusão de toda obrigação legal.

## 5. Segurança, garantias e responsabilidade

O software pode comandar motores e depende de montagem, alimentação, firmware, telemetria e comunicação corretos. Não é sistema de segurança ou de controle de veículo real. A montagem confirmada possui dois motores em paralelo no canal físico M2, incluindo um nominal de 3 V; validar tensão, corrente, proteção, aquecimento e limites antes de energização contínua. Manter corte físico de alimentação acessível. STOP de software não substitui proteção elétrica e corte físico.

Enquanto candidata, não há certificação de segurança da montagem nem garantia de que testes automatizados substituam validação física. Na extensão permitida pela legislação, não se oferece garantia contratual adicional de ausência total de falhas ou de adequação a qualquer montagem particular. A limitação não exclui direitos indisponíveis, garantias legais, responsabilidades que não possam ser afastadas ou reparações legalmente exigíveis. Nenhuma cláusula pretende excluir indiscriminadamente danos pessoais, dolo, culpa grave ou vícios cuja responsabilidade seja obrigatória. A redação final deve ser revisada para o contexto brasileiro.

## 6. Terceiros e direitos preservados

Os direitos sobre o código original, a licença de uso dos binários próprios e as permissões dos componentes de terceiros são separados. .NET, runtime packs, System.IO.Ports e outras dependências são regidos por seus textos e avisos, fornecidos em `avisos/`. A licença permissiva de uma dependência não relicencia o aplicativo original, e esta minuta não restringe permissões concedidas por terceiros.

A DLL nativa contém runtime Visual C++ incorporado. Quanto às partes proprietárias Microsoft, a permissão de uso e redistribuição concedida nesta minuta abrange sua integração ao programa, não a separação e distribuição avulsa desses componentes. Devem ser preservados os avisos; não se permite contornar limitações técnicas nem realizar engenharia reversa dessas partes proprietárias, salvo quando a legislação ou licença aplicável permitir ou exigir. As condições Microsoft pertinentes constam de `avisos/MICROSOFT_VISUAL_CPP_AVISO.md`. A redistribuição gratuita de cópias íntegras do programa continua permitida nos termos da seção 3 após autorização de publicação.

Essas condições específicas não restringem .NET/Ports sob MIT, Microsoft STL sob Apache-2.0 WITH LLVM-exception, bibliotecas livres ou direitos legais. Os textos e permissões próprios desses componentes prevalecem sobre qualquer cláusula incompatível. Nenhum SDK, compilador, biblioteca estática avulsa ou runtime de depuração Microsoft integra esta distribuição.

Quando um artefato incorporar biblioteca LGPL e sua distribuição for autorizada, serão permitidas, na extensão exigida pela LGPL, a modificação do trabalho para uso próprio do destinatário, a substituição/modificação das bibliotecas e a engenharia reversa para depurar essas modificações. Isso inclui os direitos necessários para utilizar os objetos e materiais de religação fornecidos, sem impor sigilo ou vedação que frustre esses direitos. Não há proibição absoluta de engenharia reversa, modificação ou exercício de exceções legais. A publicação dos fontes próprios não se presume necessária só porque há link estático: avaliar a licença e a forma de cumprimento concreta.

Fontes correspondentes de bibliotecas livres, avisos, objetos de aplicação e instruções de religação, quando exigidos, terão acesso nas condições da respectiva licença; não serão submetidos a restrições incompatíveis desta minuta. Objetos próprios necessários à religação não equivalem à publicação do sketch ou demais fontes próprios. Não se promete fornecer materiais incompletos como se cumprissem a LGPL; firmware ainda retido até fechamento documentado.

## 7. Marcas e independência

“Scania Haptics é um projeto independente para uso com Euro Truck Simulator 2. Não possui afiliação, patrocínio ou endosso da Scania, SCS Software ou de outros fabricantes mencionados.”

As marcas citadas pertencem aos respectivos titulares. O aviso esclarece independência e não concede autorização de marca, nem certifica a liberação do nome. A identidade pública depende da decisão do autor após avaliar o risco documentado. Nenhum logotipo ou imagem oficial é licenciado por esta minuta.

## 8. Lei aplicável e interpretação

Submeter a redação final às normas aplicáveis no Brasil, preservados direitos obrigatórios do usuário e foro legal competente. Não impor renúncia genérica de direitos nem foro que suprima proteção legal. Se uma cláusula for inválida, adequar a redação sem reduzir direitos inderrogáveis ou condições de terceiros. A aprovação desta minuta não equivale à aprovação técnica da versão ou à autorização de publicação.

## Decisões expressas ainda necessárias

| Tema | Alternativas, sem seleção automática | Decisão do autor |
|---|---|---|
| Âmbito de uso | Uso pessoal e profissional/comercial gratuito | MODELO APROVADO; redação final pendente |
| Redistribuição dos binários originais por terceiros | Cópias gratuitas e íntegras de pacotes publicados com autorização, com autoria e avisos preservados | MODELO APROVADO; candidatas internas não publicadas |
| Validade técnica, manutenção e suporte | Proposta até 14/11/2028; contato público; preservar obrigações legais | PROPOSTA PARA APROVAÇÃO FINAL |
| Nome/identidade e aviso de independência | Aprovar após avaliar risco; autorização de marca não demonstrada | PENDENTE |
| Direitos LGPL | Preservar seção 6 e fornecer materiais exigidos antes de distribuir firmware | PENDENTE DE CONFORMIDADE DO ARTEFATO |
| Vigência e redação final | Data, revisão jurídica quando cabível e aprovação expressa do titular | PENDENTE |

Modelo proprietário e gratuito: **decidido pelo autor**. Redação da EULA, opções acima e publicação: **não aprovadas por esta minuta**.
