hacka:

Checklist software architecture:

fase 01:
- DDD: Desenhar o event storming e tmb o desenho da arquitetura, contendo os domínios e subdomínios.
- Docker: vamos usar Docker e docker-compose para rodar aplicação em containers; boas praticas e multistage nos dockerfile;
- Arquitetura de software: vamos utilizar arquitetura hexagonal e fazer documentação, testes, modularização(microsservicos) etc...

fase 02:
- kubernates: vamos utilizar kubernates(EKS) e entendemos que a empresa a qual estamos desenvolvendo a solução já possua essa infraestrutura.
- clean arquitecture: se trata de um designer diferente da aplicação, mas optamos por manter arquitetura hexagonal para a solução em questão por maior familiaridade do time de desenvolvimento.

fase 03: 
- serverless: utilizamos cógnito para auxiliar no processo de integração para autenticação e integração com usuários.
- ci/cd: temos esteiras robustas conforme a fase 4
- Data Engineering: observando a necessidade especifica do desafio, optamos em utilizar apenas o SGBD relacional. Uma tabela principal com data de inicio e fim do processamento, id, usuário, nome do arquivo processado e nome do arquivo zip gerado. Outra tabela com as etapas desse processamento, para conseguir entender o status do processamento.

fase 04:
- qualidade de software: testes unitários com cobertura mínima de 80%, testes de integração e testes de aceitação (1ex apenas devido ao tempo...).
- estrutura de microsservicos: nesse caso o desenho de arquitetura demonstra com mais clareza essa estrutura, basicamente um projeto sendo a API que recebe o upload do arquivo, e realiza consultas no SGBD para consultar os status do processamento e tmb disponibilizar o download do arquivo. Outro microsservico responsável por apenas processar o arquivo e gerar o zip para download. Ambos microsservicos compartilham o mesmo volume de dados ou bucket s3. Possibilidade de trabalhar com sidecar, ou seja, dois container no mesmo pod ou dois namespaces diferentes para a solução. Os serviços utilizam mensagearia entre si.

fase 05:
- SAGA: garantir o isolamento de transações (excluir os arquivos em casos de falha, salvar dados no banco informando falha etc...);
- Desenvolvimento seguro: Sanatizar e validar os dados de entrada (Buffer overflows, cross-site scripting, SQL injection etc..), Analise estática de vulnerabilidades, validar as licenças das bibliotecas de software utilizadas.
- LGPD: Se trata de um contexto delimitado (DDD) da solução como um todo, e entemos que deva existir uma solução genérica que seja utilizada por todos os demais domínios e subdominos da empresa. Se trata de um acordo ao qual o usuário deve permirir ou selecionar o que deseja permitir em relação ao uso dos seus dados, e esse acordo deve ser elaborado com conjunto com o time jurídico. Para ter certeza de que o usuário está de acordo com a utilização dos dados de identificação do usuário e vídeos que serão enviados para o processamento e posteriormente excluídos, entendemos que o frontend da aplicação iria redirecionar o cliente a outro contexto para habilitar essa configuração ao usuário e essa role/permissão seria carregada no JWT do usuário, e a API que desenvolvemos deve certificar de que o usuário que está realizando a ação possua essa role especifica, se caso não existir o serviço não pode ser executado.

checklist hacka:
● A nova versão do sistema deve processar mais de um vídeo ao mesmo tempo;
R: Evidenciar com prints locais mesmo, utilizando um sleep/timeout para forçar a demora do processamento e mostrar que uma replica da aplicação executa em paralelo.

● Em caso de picos o sistema não deve perder uma requisição;
R: Uso das filas garante maior disponibilidade e tmb configuração de um HPA;

● O Sistema deve ser protegido por usuário e senha;
R: uso do cógnito para simplificar todo processo

● O fluxo deve ter uma listagem de status dos vídeos de um usuário;
R: endpoint par listar os vídeos do usuário (passando o id do usuário criptografado?)

● Em caso de erro um usuário pode ser notificado (email ou um outro meio de comunicação):
R: consultar configurações SMTP para possível solução.

Requisitos técnicos:
● O sistema deve persistir os dados;
R: Mysql

● O sistema deve estar em uma arquitetura que o permita ser escalado;
R: kubernates

● O projeto deve ser versionado no Github;
R: aplicar ci/cd e proteção nos branchs;

● O projeto deve ter testes:
R: cobertura mínima de 80%;