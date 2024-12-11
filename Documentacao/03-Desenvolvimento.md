
# Materiais

Os materiais utilizados no projeto foram:

- **ESP32** (para controle e comunicação entre os dispositivos)
- **ESP32-CAM** (para captura de imagens)
- **HC-SR501 PIR** (sensor de presença)
- **HC-SR04** (sensor de distância)
- **Teclado Matricial** (para digitação de senha)
- **Servo Motores** (para abrir e fechar a porta)
- **Protoboard e Fios** (para montagem e conexões dos componentes)
- **Fonte de alimentação estável** (para garantir o funcionamento contínuo do sistema)

#

### **Desenvolvimento**  

#### **Desenvolvimento do Aplicativo**  

**Interface:**  
O desenvolvimento da interface do aplicativo **MQTT Painel** foi focado em garantir a usabilidade e visualização em tempo real dos dados e do status dos dispositivos. A tela inicial mostra a captura de imagens da **ESP32-CAM**, permitindo que o usuário veja as imagens registradas na frente da residência. As telas seguintes permitem o monitoramento do status do sensor de presença e sensor de distância, além de possibilitar o controle remoto da abertura da porta via **teclado matricial**.

**Código:**  
O código do aplicativo foi desenvolvido para se comunicar com o sistema usando o protocolo **MQTT**, facilitando o controle remoto e a atualização de status. Foram implementadas funções para exibir as imagens da **ESP32-CAM**, monitorar os dados dos sensores de presença e distância, e controlar os atuadores, como o **servo motor** para abrir e fechar a porta com base na entrada da senha. A interface também permite a visualização de eventos, como a detecção de presença interna.

#

#### **Desenvolvimento do Hardware**  

**Montagem:**  
A montagem do hardware envolveu a instalação dos sensores e atuadores de forma estratégica para garantir a máxima eficiência do sistema. O **ESP32-CAM** foi posicionado na frente da casa para capturar imagens, enquanto o **sensor de distância HC-SR04** foi colocado próximo à entrada para detectar aproximações. O **teclado matricial** foi fixado próximo à porta principal, facilitando a inserção de senhas. O **sensor de presença HC-SR501** foi instalado no interior da casa, enquanto os **servo motores** foram acoplados à estrutura da porta para realizar o movimento de abertura e fechamento.

#

#### **Desenvolvimento do Código**  

O código foi desenvolvido no **Arduino IDE**, utilizando as bibliotecas necessárias para os módulos utilizados no projeto, como o **ESP32**, **ESP32-CAM**, **HC-SR04**, **HC-SR501**, **teclado matricial** e **servo motores**. O código foi dividido em diferentes funções: captura de imagens pela **ESP32-CAM**, controle dos sensores de presença e distância, verificação da senha digitada no teclado, e acionamento dos **servo motores** para abrir e fechar a porta. Além disso, o código implementa a comunicação entre os dispositivos e o aplicativo **MQTT Painel** por meio do protocolo MQTT.

#

#### **Comunicação entre App e Hardware**  

A comunicação entre o aplicativo **MQTT Painel** e o hardware (ESP32 e ESP32-CAM) foi estabelecida através do protocolo **MQTT**, que permite troca de dados em tempo real. A **ESP32** se conecta ao broker MQTT, e o aplicativo se inscreve nos tópicos de interesse para receber informações, como as imagens capturadas, status dos sensores e o controle de abertura da porta. O aplicativo envia comandos, como a abertura da porta, por meio do protocolo MQTT, e o ESP32 executa os comandos no hardware, acionando os **servo motores** e realizando a atualização de status no aplicativo.
