
### Instruções de Utilização  

#### 1. **Configuração do Hardware**  
1. Conecte o ESP32-CAM em uma posição estratégica na frente da casa para capturar imagens do ambiente externo.  
2. Instale o sensor de distância HC-SR04 próximo à entrada para monitorar aproximações.  
3. Fixe o teclado matricial na porta principal para digitação da senha de acesso.  
4. Acople os servo motores à estrutura da porta para controlar o movimento de abertura e fechamento.  
5. Posicione o sensor de presença HC-SR501 no interior da residência para detecção de movimentações internas.  
6. Utilize uma protoboard e fios para realizar as conexões dos componentes, garantindo fixação segura e isolada.  

#### 2. **Configuração do Software**  
1. Instale o **Arduino IDE** no computador.  
2. Baixe e configure as bibliotecas necessárias para ESP32, ESP32-CAM, teclado matricial, sensores e servo motores.  
3. Conecte a ESP32 e a ESP32-CAM ao computador por USB.  
4. No Arduino IDE, abra o código do projeto, insira as credenciais de rede Wi-Fi para a ESP32-CAM e ajuste os parâmetros de funcionamento dos sensores.  
5. Compile e faça o upload do código para cada módulo.  
6. Realize testes individuais para verificar o funcionamento dos sensores, teclado e servo motores, e certifique-se de que a ESP32-CAM está enviando imagens corretamente para o destino configurado.  

#### 3. **Informações Importantes**  
- Utilize uma senha segura e armazene-a em local confiável.  
- Calibre os sensores HC-SR04 e HC-SR501 para evitar falsos positivos.  
- Garanta que a fonte de energia seja estável para o funcionamento contínuo do sistema.  
- Em caso de falha na conexão Wi-Fi, configure uma alternativa para que o sistema de abertura de porta funcione localmente.  
 
