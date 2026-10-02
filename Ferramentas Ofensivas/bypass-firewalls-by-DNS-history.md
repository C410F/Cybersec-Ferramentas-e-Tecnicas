# Análise de Ferramenta Ofensiva: bypass-firewalls-by-DNS-history

## 1. Resumo
O bypass-firewalls-by-DNS-history, desenvolvido por Vincent Cox, é uma ferramenta voltada à descoberta de possíveis servidores de origem de aplicações web através da análise de informações históricas de DNS.

A ferramenta parte de um conceito simples: um domínio pode ter utilizado diferentes endereços IP ao longo de sua existência. Mesmo após a migração para uma CDN ou WAF, um endereço IP anteriormente associado ao domínio pode continuar acessível ou permanecer registrado em fontes de histórico DNS.

Do ponto de vista defensivo, essa técnica é relevante porque demonstra que a utilização de um WAF ou CDN não garante, por si só, que a infraestrutura de origem esteja inacessível diretamente pela Internet.

O projeto pode ser utilizado em avaliações autorizadas para identificar possíveis exposições de infraestrutura e validar controles de proteção do servidor de origem.

## 2. Visão Geral da Ferramenta

O projeto utiliza informações históricas relacionadas ao DNS para identificar endereços IP que já estiveram associados a determinado domínio.

A ferramenta consulta diferentes fontes de informações de DNS e infraestrutura para obter possíveis endereços históricos.

Entre as fontes utilizadas pelo projeto estão serviços como:

- SecurityTrails
- CrimeFlare
- CertSpotter
- DNSDumpster
- IPinfo
- ViewDNS

Após obter possíveis endereços, a ferramenta pode verificar se esses servidores ainda respondem e comparar o conteúdo retornado com o domínio analisado.

Essa comparação é importante porque um endereço IP histórico pode pertencer atualmente a outra infraestrutura. Portanto, simplesmente encontrar um IP antigo não é suficiente para concluir que ele ainda representa o servidor de origem.

## 3. Papel na Cadeia de Ataque

A ferramenta se encaixa principalmente na fase de Reconhecimento / Descoberta de infraestrutura, antes do acesso inicial. O objetivo nesse contexto não é explorar diretamente uma vulnerabilidade, mas obter informações que possam revelar uma superfície de ataque que não deveria estar exposta.

Caso um endereço de origem seja identificado e esteja indevidamente acessível, essa informação poderia posteriormente ser utilizada em outras etapas de uma operação ofensiva. Portanto, a ferramenta deve ser entendida principalmente como um recurso de reconhecimento, e não como uma ferramenta de exploração ou pós-exploração.

## 4. Uso da Ferramenta

O projeto pode ser obtido diretamente através do GitHub:

git clone https://github.com/vincentcox/bypass-firewalls-by-DNS-history.git

cd bypass-firewalls-by-DNS-history

O script principal é:

bypass-firewalls-by-DNS-history.sh

O uso básico documentado pelo projeto utiliza o domínio como parâmetro:

bash bypass-firewalls-by-DNS-history.sh -d exemplo.com

Também existem opções relacionadas à geração de resultados e à análise de subdomínios.

## 5. Oportunidades de Detecção

A atividade realizada por essa ferramenta ocorre principalmente no lado externo da infraestrutura. Portanto, a detecção pode ser mais efetiva através da combinação de telemetria de rede, DNS e exposição de ativos.

Possíveis indicadores:

Grande quantidade de consultas relacionadas a subdomínios;
Enumeração sistemática de hosts;
Acessos repetidos a diferentes endereços IP associados à organização;
Tentativas de conexão direta com servidores que normalmente deveriam receber tráfego apenas de uma CDN/WAF.

Um único evento dificilmente representa uma atividade maliciosa. A correlação de múltiplos eventos aumenta a qualidade da análise.

Uma estratégia defensiva interessante é manter um inventário dos IPs atualmente utilizados e dos IPs anteriormente associados à organização. Caso um endereço antigo continue aceitando conexões externas, ele deve ser investigado.

Servidores de origem protegidos por CDN/WAF podem ser configurados para registrar a origem das conexões. Conexões diretas provenientes da Internet podem indicar que o servidor de origem está acessível fora do caminho esperado.

Além da detecção tradicional baseada em logs, organizações podem executar periodicamente seus próprios processos de reconhecimento. Esse tipo de validação pode revelar exposições antes que sejam exploradas.

## 6. Mitigações e Controles

A principal mitigação é garantir que descobrir o endereço IP do servidor de origem não seja suficiente para contornar a camada de proteção. Servidores que não são mais utilizados devem ser desativados ou removidos da exposição pública.

Um IP histórico não deveria continuar apontando para uma aplicação funcional sem necessidade operacional.

As regras de firewall do servidor devem limitar o acesso aos serviços necessários.

Especialmente em ambientes protegidos por CDN/WAF, deve-se avaliar se portas e serviços internos estão inadvertidamente expostos à Internet.

Alterações de DNS devem fazer parte do processo de gerenciamento da infraestrutura.

Também é importante entender que uma mudança de DNS não necessariamente elimina informações anteriormente publicadas.

O histórico pode permanecer disponível em serviços de terceiros.

Manter um inventário atualizado de:

- Domínios;
- Subdomínios;
- Endereços IP;
- Servidores;
- Serviços expostos;
- CDNs;
- WAFs;
- Ambientes antigos.

Isso facilita identificar ativos esquecidos ou que deveriam ter sido retirados da Internet.

Uma organização pode executar avaliações autorizadas de sua própria superfície externa para verificar:

- IPs históricos;
- DNS atual;
- Subdomínios;
- Serviços expostos;
- Servidores de origem;
- Configurações de firewall.

Essa abordagem transforma uma técnica de reconhecimento ofensivo em uma atividade de validação defensiva de exposição.

## 7. Referências

[GitHub — bypass-firewalls-by-DNS-history](https://github.com/vincentcox/bypass-firewalls-by-DNS-history)

[MITRE ATT&CK — Reconnaissance](https://attack.mitre.org/tactics/TA0043/)

[MITRE ATT&CK — Gather Victim Network Information](https://attack.mitre.org/techniques/T1590/)

[MITRE ATT&CK — Gather Victim Host Information](https://attack.mitre.org/techniques/T1592/)

[OWASP — Web Application Security](https://owasp.org/)
