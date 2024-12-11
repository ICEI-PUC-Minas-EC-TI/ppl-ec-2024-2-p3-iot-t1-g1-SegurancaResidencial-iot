# Introdução

Este projeto consiste no desenvolvimento de um sistema de segurança residencial inteligente, que integra monitoramento remoto, controle de acesso e detecção de movimentação. Utilizando componentes como ESP32, ESP32-CAM, sensores de distância (HC-SR04) e presença (HC-SR501), teclado matricial e servo motores, o sistema proporciona uma solução acessível e prática para a proteção de residências. Com a interface no aplicativo MQTT Painel, o usuário tem controle e visualização em tempo real das atividades ao redor e dentro da residência.  

A motivação do projeto surge do aumento da preocupação com a segurança em ambientes residenciais, especialmente em áreas urbanas. A proposta justifica-se pela necessidade de sistemas personalizados e de baixo custo, que possam ser implementados mesmo por usuários com conhecimentos técnicos limitados. O público-alvo inclui proprietários de imóveis que buscam alternativas eficientes e adaptáveis para a segurança de suas residências.  

### **Problema**  

A segurança de residências enfrenta desafios como a falta de monitoramento contínuo, controle de acesso eficiente e detecção de presença interna. Sistemas comerciais disponíveis frequentemente possuem custo elevado e exigem infraestrutura complexa, o que os torna inacessíveis para muitas pessoas.  

O projeto visa resolver esses problemas ao oferecer uma solução integrada, que combina tecnologias acessíveis e de fácil configuração. No contexto do projeto, a ESP32-CAM é usada para capturar imagens externas, os sensores para monitorar movimentos, e o aplicativo MQTT Painel para gerenciar e visualizar as informações em tempo real.  

---

### **Objetivos**  

**Objetivo Geral:**  
Desenvolver um sistema de segurança residencial integrado, acessível e funcional, capaz de oferecer monitoramento remoto, controle de acesso e detecção de movimentos.  

**Objetivos Específicos:**  
1. Criar um sistema de controle de acesso baseado em senha utilizando teclado matricial e servo motores.  
2. Implementar um sistema de monitoramento externo com captura e transmissão de imagens em tempo real pela ESP32-CAM.  
3. Configurar sensores para detectar aproximações externas e presença interna com precisão e eficiência.  
4. Integrar o sistema com o aplicativo MQTT Painel para facilitar o controle e o monitoramento pelo usuário.  

---

### **Público-Alvo**  

O sistema foi projetado para atender proprietários de residências que buscam soluções acessíveis e personalizáveis para segurança. O público-alvo inclui pessoas com conhecimentos básicos em tecnologia, que possam configurar e operar o sistema com apoio de guias claros, bem como entusiastas de automação residencial, interessados em expandir as funcionalidades do sistema conforme suas necessidades.  

Ao priorizar facilidade de uso e compatibilidade com dispositivos amplamente disponíveis, o projeto atende usuários que desejam adotar soluções tecnológicas sem depender de serviços de segurança caros ou complexos.  
