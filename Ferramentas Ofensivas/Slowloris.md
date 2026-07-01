# Análise de Ferramenta Ofensiva: Slowloris

## 1. Resumo
O Slowloris é uma ferramenta conhecida por explorar uma característica do protocolo HTTP para manter conexões abertas durante longos períodos, consumindo gradualmente a capacidade de atendimento de um servidor web. Diferentemente de ataques volumétricos tradicionais, ele não depende de alta largura de banda, mas sim da ocupação prolongada dos recursos responsáveis pelo gerenciamento das conexões.

Do ponto de vista defensivo, compreender seu funcionamento é importante para identificar indicadores de comprometimento da disponibilidade, validar configurações de servidores web e implementar mecanismos de mitigação adequados. Neste laboratório, foi realizado um ambiente controlado para observar seus efeitos sobre um servidor HTTP simples em Python.

## 2. Visão Geral da Ferramenta
O Slowloris foi desenvolvido para demonstrar uma técnica de negação de serviço baseada em conexões HTTP incompletas. Seu funcionamento consiste em estabelecer diversas conexões TCP com o servidor e enviar cabeçalhos HTTP de maneira extremamente lenta, mantendo cada conexão ativa pelo maior tempo possível.

Enquanto o servidor aguarda a conclusão da requisição, recursos internos permanecem alocados para cada conexão aberta. Quando o número de conexões cresce além da capacidade de atendimento do servidor, novos clientes podem sofrer degradação de desempenho ou indisponibilidade.

Uma das principais características dessa técnica é que ela gera pouco tráfego de rede quando comparada a ataques de negação de serviço tradicionais, dificultando sua identificação apenas por volume de pacotes.

## 3. Uso da Ferramenta
Para os testes realizados neste laboratório, foi utilizado uma máquina cliente rodando Kali Linux e um servidor local python rodando no Fedora Server. Para fins de monitoramento do impacto, foi utilizada a ferramenta Btop. 

Foi observado que o impacto ocorreu principalmente no gerenciamento das conexões simultâneas do servidor, e não no consumo elevado de CPU ou memória. 

Na imagem a seguir, é possível observar que a ferramenta consumiu 150 threads do servidor, o que pode ser ampliado a partir de infraestruturas destinadas para ataques de DDoS, exaurindo servidores vulneráveis

<p align="center">
  <img src="Screenshots/Fedora ataque.png" width="800">
</p>

<p align="center">
  <em><strong>Figura 1.</strong>Impacto da ferramenta, explícito no número de threads consumidas pelo processo do servidor python</em>
</p>

## 4. Papel na Cadeia de Ataque
O Slowloris normalmente aparece durante a fase de Impacto da cadeia de ataque, quando o objetivo é comprometer a disponibilidade de um serviço.

Embora o MITRE ATT&CK trate ataques de negação de serviço de forma limitada, essa técnica está alinhada ao objetivo de impedir ou degradar a disponibilidade de aplicações expostas.

Sua utilização normalmente ocorre após o reconhecimento da infraestrutura do alvo, quando o atacante identifica um serviço HTTP suscetível ao gerenciamento inadequado de conexões persistentes.

## 5. Oportunidades de Detecção
A detecção desse comportamento deve ser baseada principalmente na análise do padrão das conexões, e não apenas no volume de tráfego.

Alguns indicadores incluem:

- Grande quantidade de conexões HTTP simultâneas provenientes do mesmo endereço IP;
- Conexões que permanecem abertas por longos períodos;
- Aumento contínuo do número de sessões TCP estabelecidas;
- Crescimento anormal da quantidade de threads ou workers responsáveis pelo atendimento das requisições;
- Aumento no tempo de resposta para clientes legítimos.

## 6. Mitigações e Controles

Diversas medidas podem reduzir significativamente a eficácia dessa técnica.

Entre as principais estão:

- Redução do tempo máximo permitido para conexões inativas;
- Limitação do número de conexões simultâneas por cliente;
- Utilização de módulos específicos para controle de requisições lentas;
- Implementação de proxies reversos para absorção de conexões;
- Utilização de balanceadores de carga;
- Monitoramento contínuo do número de conexões TCP;
- Configuração adequada dos parâmetros de Keep-Alive e timeout;
- Utilização de Web Application Firewalls (WAF);
- Aplicação de mecanismos de rate limiting.

Também é recomendável que ambientes críticos possuam monitoramento em tempo real dos seguintes indicadores:

- Quantidade de conexões TCP;
- Número de workers ou threads ativos;
- Tempo médio de resposta das aplicações;
- Taxa de criação de novas conexões;
- Disponibilidade do serviço.

## 7. Referências

- [Slowloris no Github](https://github.com/gkbrk/slowloris)
- [Ataques DDoS Slowloris Explicados | Akamai](https://www.akamai.com/pt/glossary/what-is-a-slowloris-ddos-attack)
- [Ataque DDoS Slowloris | Cloudflare](https://www.cloudflare.com/pt-br/learning/ddos/ddos-attack-tools/slowloris/)
