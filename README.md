# Dimensionamento rede
Série de modelos para dimensionamento de rede de esgoto pelo método convencional para introduzir no Qgis e tratar no Excel

Não esqueça de mudar o sistema de coordenadas do projeto e das camadas e de nomear o modelo digital de elevação como 'MDE'.

![image](https://github.com/user-attachments/assets/8cba5a99-0617-497e-ab85-7aeae8ff7d43)

A pasta 'Projeto padrão' são os arquivos modelo para iniciar um novo projeto. Consta nessa pasta, também, os códigos que devem ser reproduzidos nas colunas da tabela de atributos da camada 'TRECHO RETO', a camada dos coletores da rede.

Os códigos foram inseridos nas propriedades da camada e, ao inserir um trecho, ele já recebe valores calculados com base nos códigos. No entanto, sugiro rodar os códigos, no caso dos valores resultarem inconsistentes, por algum motivo. O caminho para acessar a calculadora de campo e inserir os códigos está indicada nas próximas 3 imagens. 

![image](https://github.com/user-attachments/assets/c961d7b5-9907-4bdc-947c-0ec5b87df69d)


![image](https://github.com/user-attachments/assets/b1c5ef10-fd58-4abd-9949-1864fda7aea7)


![image](https://github.com/user-attachments/assets/7c78e7bc-cda6-4cbf-8caf-ce2b19fdf4b4)

A tabela de atributos do 'TRECHO RETO' deve ser exportada em formato xls e copiada para a aba da planilha do Excel de dimensionamento 'Planilha esgoto' no mesmo formato que o gerado pela exportação.

Na planilha de dimensionamento, as vazões devem ser definidas na aba 'VAZÕES' conforme as populações das últimas apurações e a população de saturação conforme estudo da cidade ou bairro referente ao perfil de ocupação, do uso e ocupação do solo e das disposições do plano diretor estratégico da cidade.

Sobre o dimensionamento, as duas primeiras linhas do dimensionamento são explicadas na tabela imediatamente abaixo, as demais células abaixo seguem os mesmo cálculos

| CÉLULA | FÓRMULA | COMENTÁRIO |
|:---|:---|:---|
| N13 |	=SE(C13=0;"";SE(T13=0;"";T13/U13))	| Verifica a velocidade máxima do esgoto de início de plano |
| N14 |	=SE(C13=0;"";SE(T13=0;"";T13/U13))	| Verifica a velocidade máxima do esgoto de final de plano |
| O13 |	=SE(C13=0;"";1000*W13*H13*10)	| Verifica a tensão trativa de início de plano, a crítica nessa avaliação |
| P13 |	=SE(C13=0;"";SE(W14=0;"";6*RAIZ(9,81*W14)))	| Velocidade crítica no trecho, para mudança de regime de escoamento |
| Q13 |	0,010	| Coeficiente de Manning da tubulação, nesse caso de PVC rígido |
| Q14 |	0,010 |	Coeficiente de Manning da tubulação, nesse caso de PVC rígido |
| R13 |   |	Observações |
| S13 |	=SE(C13=0;0;SE(B13=0;0;calculaB(G13/1000;H13;F13;Q13)))	| Calcula  o ângulo a de início de plano da tubulação, com base na macro adaptada do Tsutiya (1999) | 
| S14 |	=SE(C14=0;0;SE(B13=0;0;calculaB(G13/1000;H13;F14;Q14)))	| Calcula  o ângulo a de final de plano da tubulação, com base na macro adaptada do Tsutiya (1999) |
| T13 |	=SE(F13<1,5;1,5/1000;F13/1000) |	Calcula a vazão de início de plano no trecho e, se menor que 1,5 l/s, escolhe-se esse valor |
| T14 |	=SE(F14<1,5;1,5/1000;F14/1000) |	Calcula a vazão de final de plano no trecho e, se menor que 1,5 l/s, escolhe-se esse valor |
| U13 |	=(S13-SEN(S13))*POTÊNCIA((G13/1000);2)/8	| Calcula a área molhada de início de plano do trecho com base no ângulo a |
| U14 |	=(S14-SEN(S14))*POTÊNCIA((G13/1000);2)/8	| Calcula a área molhada de final de plano do trecho com base no ângulo a |
| V13 |	=S13*G13/1000/2	| Calcula o perímetro molhado de início de plano do trecho com base no ângulo a |
| V14 |	=S14*G13/1000/2	| Calcula o perímetro molhado de final de plano do trecho com base no ângulo a |
| W13 |	=SE(V13=0;0;U13/V13)	| Calcula o raio hidráulico de início de plano do trecho com base na área e perímetro molhado |
| W14 |	=SE(V14=0;0;U14/V14)	| Calcula o raio hidráulico de final de plano do trecho com base na área e perímetro molhado |
| X13 |	=SE(E(N14<P13;ARREDONDAR.PARA.CIMA(O13;1)>=1;L14<0,75;N14<=5);"OK!";"NÃO OK") |	Verifica se a velocidade do escoamento no trecho é menor do que a crítica; se a tensão trativa é pelo menos 1 Pa, com alguma tolerância; se a lâmina d'água é menor que 0,75; e, se a velocidade de escoamento no trecho é menor do que 5 m/s. Se todos esses requisitos forem atendidos, a tubulação é tida como verificada |
| Y13 |	=PROCV(A13;'TRECHO RETO'!$A$2:$Q$50000;14)	| Consulta na tabela extraída do QGIS, qual o tipo da singularidade à montante do trecho, se Terminal de limpeza ou Terminal de inspeção e limpeza. Na realidade alguns trechos ao invés de Terminal de inspeção e limpeza serão Poços de visita, no entanto, o intuito dessa coluna, já utilizada nos cálculos, é verificar se o dispositivo à montante é ou não um terminal de limpeza, ou melhor, uma ponta seca |
| Z13 |	=SE(M13>3;"POÇO DE VISITA";"TERMINAL DE INSPEÇÃO E LIMPEZA")	| Verifica quais das singularidades à jusante ultrapassam 3 metros de profundidade; no caso de superar, a norma ABNT NBR 9649 recomenda o uso de poços de visita. |
| AA13 |	=SE(OU(SEERRO(ÍNDICE($Z$13:$Z$50000;CORRESP(ÍNDICE('TRECHO RETO'!$R$2:$R$50000;CORRESP(A13;'TRECHO RETO'!$A$2:$A$50000;0));$A$13:$A$50000;0));"")="POÇO DE VISITA";SEERRO(ÍNDICE($Z$13:$Z$50000;CORRESP(ÍNDICE('TRECHO RETO'!$S$2:$S$50000;CORRESP(A13;'TRECHO RETO'!$A$2:$A$50000;0));$A$13:$A$50000;0));"")="POÇO DE VISITA";SEERRO(ÍNDICE($Z$13:$Z$50000;CORRESP(ÍNDICE('TRECHO RETO'!$T$2:$T$50000;CORRESP(A13;'TRECHO RETO'!$A$2:$A$50000;0));$A$13:$A$50000;0));"")="POÇO DE VISITA";Z13="POÇO DE VISITA");"POÇO DE VISITA";"TERMINAL DE INSPEÇÃO E LIMPEZA")	| Consulta quais trechos terminam no mesmo ponto e atribui um poço de visita se em algum deles a profundidade da singularidade à jusante for maior que 3 metros. Se não for maior em nenhum dos trechos que chegam naquele ponto, o tipo do dispositivo de inspeção deve ser o mais econômico, o terminal de inspeção e limpeza |
| AB13 |	=SE(M13>5;"PROF. > 5M";"")	| Consulta se algum dos trechos ultrapassa os 5 metros de profundidade. Em casos como esse podem ser necessários adaptações como escadas para acesso e segurança |
| AC13 |	=PROCV(A13;'TRECHO RETO'!$A$1:$AA$50000;27;FALSO)	| Verifica quantos trechos terminam junto com o trecho em análise. Foi realizado para evitar duplicatas no momento de fazer a contagem de cada tipo de dispositivo |
| AD13 |	=SE(E(Y13="TERMINAL DE INSPEÇÃO E LIMPEZA";E13=0);"ERRO";"")	| Verifica se algum trecho que não começa com um terminal de limpeza está recebendo a contribuição de outros trechos |

