# Dimmerizador com Controlador PID
 
FUNDAÇÃO EDSON QUEIROZ
UNIVERSIDADE DE FORTALEZA – UNIFOR
Centro de Ciências Tecnológicas – CCT

Controle Digital
Dimmerizador PID
Prof. Afonso Henrique

# AUTORES
Lucas Bezerra Holanda
Marcos Vinicius Cândido Leitão
Pedro Benevides Cavalcante

2017
Fortaleza – CE
INTRODUÇÃO

   Com o passar dos dias é notável que a automação está se tornando mais rotineira em nossas residências. Desde tarefas complexas como o controle de temperatura para um morador com necessidades especiais, até o simples controle da iluminação de determinados cômodos.
   O projeto exposto neste relatório tem como objetivo o desenvolvimento de um dimerizador com controlador PID, podendo ser controlado tanto remotamente, utilizando um dispositivo móvel, como automaticamente, adaptando-se às condições ambientais onde o sistema de iluminação está instalado. 
   A base do projeto será a programação implementada numa plataforma Arduino, na qual efetuará todas as funções de PID do sistema, recebendo as leituras dos LDRs e convertendo em sinais controlados para que os Leds possam ser dimerizados e assim efetuar a iluminação necessária. 
   Arduino, é uma plataforma de prototipagem eletrônica de hardware livre e de placa única, projetada com um microcontrolador Atmel AVR com suporte de entrada/saída embutido, uma linguagem de programação padrão, a qual tem origem em Wiring, e é essencialmente C/C++. O objetivo do projeto é criar ferramentas que são acessíveis, com baixo custo, flexíveis e fáceis de se usar por artistas e amadores.
   Ao passar dos anos o Arduino tem sido o cérebro de milhares de projetos, tanto de objetos rotineiros como de instrumentos científicos complexos. Uma comunidade mundial de Makers, estudantes, hobistas, artistas, programadores e profissionais se juntou a esta plataforma de hardware livre, sua contribuição tem incrementado uma quantidade incrível de conhecimento acessível que pode ser de grande ajuda para novatos e experts.
   Roboremo é um aplicativo para android gratuito disponível na playstore que é utilizado para desenvolver simples interfaces e se comunicar de diferentes formas. O aplicativo se comunicará com o arduino via bluetooth, e este envia os dados inseridos na tela de acordo com o que foi programado pelo usuário. 
   O módulo HC-06 permite enviar e receber dados através da tecnologia Bluetooth sem conectar um cabo serial para computador ou microcontrolador. Este módulo pode ser conectado a qualquer dispositivo que possua comunicação serial, tais como mouse, teclado, joystick, computadores, microcontroladores, receptor GPS, controladores de equipamentos industriais e muito outros. A comunicação entre o aplicativo e o arduino será feita através deste módulo.
   Para este projeto foi utilizado apenas um sensor: o LDR (Light Dependent Resistor). O LDR é um resistor cuja resistência varia diretamente com a luminosidade incidida sobre o mesmo. Quanto maior a luminosidade, menor a resistência do sensor. O LDR servirá para captar a luz ambiente e a luz emitida pelos leds. A leitura de luminosidade capturada pelo LDR será a variável de controle do PID, que deve ser monitorada pelo arduino.
   O brilho sobre o LDR será proveniente da luz ambiente e dos oito LEDs de alto brilho, que serão controlados pelas portas PWM (Pulse Width Modulation) do arduino. Todo o circuito foi montado em uma protoboard e sua estrutura foi feita de forma compacta em uma caixa de papelão.

FUNDAMENTAÇÃO TEÓRICA

   O programa começa com a inicialização das variáveis do programa e as variáveis de cada controlador PID (um para cada ponto de luz, totalizando quatro). O setPoint, que é variável de entrada do sistema, é inicializado com o valor 85. No setup é configurado cada pino e iniciado o software serial para comunicação bluetooth e o serial padrão para visualizar os dados do projeto no monitor serial. Na função loop() o programa sempre inicia verificando se há dado serial e, caso verdadeiro, os setPoints do projeto são atualizados de acordo com o que foi recebido. Em seguida é chamado a função getPID para cada ponto de luminosidade, que realiza todos os cálculos de controle e retorna um inteiro, que é um valor de 0 a 255. Logo após, é atualizado o PWM de cada LED com os valores obtidos em seu respectivo cálculo de controle e o programa retorna entra em loop. A figura 1 representa o diagrama funcional do projeto. 

METODOLOGIA

   O dimmerizador controla o nível de luminosidade do ambiente automaticamente e se adapta a mudanças de claridade, aumentando ou diminuindo o brilho dos LEDs de acordo a luminosidade captada pelo LDR. No projeto desenvolvido, foi projetado três pontos de leitura de luminosidade e quatro prontos e iluminação, com LDRs e LEDs respectivamente. 
   No aplicativo Roboremo foi desenvolvido uma interface com três sliders, em que cada um representa o nível de luminosidade desejado em seu respectivo ponto de leitura. A aplicação envia uma string por bluetooth para o arduino, que processará a informação de acordo com o que foi programado e mudará o setPoint do ponto de luminosidade selecionado. A string enviada possui o seguinte formato: “ID do objeto” + “setPoint”, sendo setPoint um valor inteiro de 0 a 100.
   Ao aplicar o controle em malha aberta o dimmerizador não se adapta às condições de luminosidade do ambiente, fazendo com que o brilho dos LEDs seja alterado somente pelo aplicativo. Para fazer o dimmerizador se adaptar a luminosidade do ambiente, foi necessário realizar um controle de malha fechada. Dessa forma, o setPoint se torna a entrada do sistema, a luminosidade captada pelo LDR será a variável de controle e a resposta do sistema será o valor aplicado ao PWM que controla o brilho do LED. 
   Com a realimentação, o brilho led começa a variar um pouco com a luminosidade, porém, a variação é quase imperceptível. Foi então adicionado um ganho proporcional Kp =  4, o que possibilitou a luminosidade a atingir o setPoint. Utilizando somente o ganho proporcional, o LED ficou pequenos ruídos, pois a luminosidade nunca atingia o setPoint ideal. 
   Para tentar melhorar o sistema, foi adicionado um compensador derivativo de ganho Kd, que não mostrou resultado satisfatório. Com o ganho alto o compensador derivativo causa instabilidade no sistema aumentando o ruído. Já com o ganho baixo, o compensador teve influência muito baixa sobre a resposta do sistema. O motivo é que a resposta do LDR é quase instantânea, e como a velocidade do clock do arduino é limitada, o derivativo não efetua os cálculos de forma precisa. 
Foi então adicionado um compensador integrativo de ganho Ki. Com o ganho Ki = 0.5, o compensador mostrou-se bem efetivo, mantendo o brilho sempre em torno do setPoint desejado. Para causar um efeito agradável, o Ki foi fixado em 0.009, fazendo com que as alterações de luminosidade fizessem com o que o LED mudasse seu brilho gradativamente. Com Ki = 0.009, o ganho ideal encontrado para Kp foi 0.3 e para Kd foi 0.006. 
   Com o controle PID ideal encontrado para a aplicação, iniciou-se a montagem do projeto. O circuito ficou montado em uma protoboard e ficou dentro do fundo falso de uma caixa de papelão. Os LDR e LEDs ficaram fixados em cima do fundo falso, e a tampa da caixa de papelão foi usada para controlar a iluminação do sistema.

CONCLUSÃO

  É evidente que a automação está cada vez mais presente na vida das pessoas. Cada vez mais é visível a quantidade de produtos tecnológicos que estão sendo lançados para melhorar o conforto e lazer. O projeto presente neste trabalho é mais um exemplo disso.
  Este projeto pode servir tanto para uma simples casa quanto para uma fábrica ou centro de eventos, dependendo das necessidades do ambiente. No âmbito residencial como no industrial, será possível uma economia de energia baseado nas condições do ambiente onde for instalado. No caso, o sistema efetuará uma análise da iluminação do ambiente, e caso exista iluminação natural o sistema irá medir e efetuar a iluminação necessária baseado no setPoint configurado pelo usuário. Tudo isso será calculado pelo PID possibilitando uma economia de energia sem prejudicar a visibilidade da dependência onde o sistema está inserido.
  Atualmente já existem algumas soluções no mercado parecidas com a proposta deste projeto. De acordo com a empresa Lutron, se cada casa americana substituir dois interruptores comuns de lâmpada por interruptores dimmer, seria possível economizar 1.5 bilhões de dólares em energia por ano. Com um dimmerizador automático que se adapta a iluminação do ambiente essa economia em energia seria ainda maior. O investimento para a implementação de um projeto de automação destes é de baixo custo e o retorno se daria na conta de energia em alguns meses.

REFERENCIA BIBLIOGRAFICA

[1] - http://www.ni.com/white-paper/3782/pt/
[2] - https://www.arduino.cc/en/Guide/Introduction
[3] - https://produto.mercadolivre.com.br/MLB-830700015-modulo-trasceiver-bluetooth-rs232ttl-arduino-pic-avr-hc06-_JM
[4] - http://www.bulbs.com/learning/dimmers.aspx
