# Testes do Projeto

#### **Metodologia dos Testes**  
Os testes foram realizados para avaliar o funcionamento de cada componente individualmente e a integração completa do sistema de segurança residencial. As etapas incluíram:  

1. **Testes Individuais:**  
   - **ESP32-CAM:** Verificação da captura e transmissão de imagens via rede Wi-Fi, analisando a qualidade da imagem e o tempo de resposta.  
   - **HC-SR04 (sensor de distância):** Avaliação da precisão na detecção de objetos próximos e ajuste do intervalo de sensibilidade.  
   - **HC-SR501 (sensor de presença):** Testes em diferentes ambientes internos para validar a detecção de movimentos e evitar falsos positivos.  
   - **Teclado Matricial:** Validação da entrada e leitura de senhas, garantindo que os comandos acionem corretamente os servo motores.  
   - **Servo Motores:** Testes de abertura e fechamento da porta para confirmar a força, precisão e alinhamento mecânico.  

2. **Testes de Integração:**  
   - Montagem completa do sistema para verificar a comunicação entre os dispositivos e o funcionamento em cenários reais.  
   - Simulação de entrada de senha correta e incorreta para testar o controle de acesso.  
   - Simulação de invasões externas e internas para validar o monitoramento e a resposta do sistema.  

#### **Resultados Obtidos**  
Os testes mostraram que o sistema funciona de maneira eficaz em condições normais. A ESP32-CAM capturou imagens de boa qualidade em ambientes bem iluminados, e os sensores detectaram movimentos com precisão em suas respectivas áreas de cobertura. O teclado matricial respondeu adequadamente às entradas, e os servo motores executaram o movimento de abertura e fechamento da porta de forma confiável.  

#### **Limitações Encontradas**  
1. **Conexão Wi-Fi:** A ESP32-CAM apresentou instabilidade em redes com sinal fraco, comprometendo a transmissão de imagens.  
2. **Iluminação:** A qualidade das imagens da ESP32-CAM diminuiu significativamente em ambientes com baixa iluminação.  
3. **Falsos Positivos:** O sensor de presença HC-SR501 detectou movimentações indesejadas em algumas situações, como variações térmicas causadas por equipamentos próximos.  
4. **Configuração Inicial:** Usuários sem experiência em tecnologia podem enfrentar dificuldades durante a configuração inicial do sistema, especialmente na configuração da ESP32 e ESP32-CAM.  
