# Apresentação da Solução

Use esta pasta para salvar os seguintes projetos:
- Projeto conceitual (diagrama entidade relacionamento)

[DER.pdf](https://github.com/user-attachments/files/18120245/TRABALHOIOT_DER.drawio.pdf)

![Captura de tela 2024-12-12 235948](https://github.com/user-attachments/assets/859646d2-82f1-4f9e-8f80-fd5b5958d7ab)

#
- Projeto lógico (modelo relacional)

[MER.pdf](https://github.com/user-attachments/files/18120207/trabalhoIOT_MER.1.pdf)

![Captura de tela 2024-12-12 234324](https://github.com/user-attachments/assets/535cfa3b-df27-4f0b-9752-0307cea7f5d9)

#

- Projeto físico (implementação do banco de dados no SGBD)

      CREATE TABLE IF NOT EXISTS Porta (
        id INT AUTO_INCREMENT PRIMARY KEY,
        senha INT
      );

      INSERT INTO Porta (id, senha) VALUES (1, 1234);
      CREATE TABLE IF NOT EXISTS Tentativas (
        id INT AUTO_INCREMENT PRIMARY KEY,
        horas DATETIME,
        id_porta INT,
        senha INT,
        FOREIGN KEY (id_porta) REFERENCES Porta(id)
      );

      CREATE TABLE IF NOT EXISTS Movimentos (
        id INT AUTO_INCREMENT PRIMARY KEY,
        horas DATETIME,
        id_porta INT,
        FOREIGN KEY (id_porta) REFERENCES Porta(id)
      );

#

![Imagem do WhatsApp de 2024-12-13 à(s) 11 30 51_ebd0707c](https://github.com/user-attachments/assets/fa15aecf-d736-4506-9339-9685c1f6c697)

![Imagem do WhatsApp de 2024-12-13 à(s) 11 30 31_915627c5](https://github.com/user-attachments/assets/31949311-64cf-4a11-9b7f-e553d68aa624)

![Imagem do WhatsApp de 2024-12-13 à(s) 11 29 55_ffdc95bb](https://github.com/user-attachments/assets/5da613bb-80a4-4793-b0f9-82b05607cca3)

![Imagem do WhatsApp de 2024-12-13 à(s) 11 30 13_e3958780](https://github.com/user-attachments/assets/c23cf1ba-9d55-45fc-a67e-4bd1fa18449b)



    
