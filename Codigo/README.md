# Código do Arduino/ESP

## Código total


    import paho.mqtt.client as mqtt
    import mysql.connector
    from datetime import datetime

    # Create a connection object
    con = mysql.connector.connect(host='localhost', database='trabalhoiot', user='root', password='ECGJOOAB')

    # Check if the connection was successful
    if con.is_connected():
    db_info = con.get_server_info()
    print("Connected successfully to the server ", db_info)

    # Create a cursor object
    cursor = con.cursor()

    # Check the current database
    cursor.execute("select database();")
    linha = cursor.fetchone()
    print("Connected to DB", linha)

    # SQL commands to create the tables
    createTablePorta = """
                CREATE TABLE IF NOT EXISTS Porta (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    senha INT
                );
            """
    insertPorta = """
                INSERT INTO Porta (id, senha) VALUES (1, 1234);
    """
    createTableTentativas = """
                CREATE TABLE IF NOT EXISTS Tentativas (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    horas DATETIME,
                    id_porta INT,
                    senha INT,
                    FOREIGN KEY (id_porta) REFERENCES Porta(id)
                );
            """
    createTableMovimentos = """
                CREATE TABLE IF NOT EXISTS Movimentos (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    horas DATETIME,
                    id_porta INT,
                    FOREIGN KEY (id_porta) REFERENCES Porta(id)
                );
    """
    createTablePresenca = """
                CREATE TABLE IF NOT EXISTS Presenca (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    horas DATETIME,
                    id_porta INT,
                    FOREIGN KEY (id_porta) REFERENCES Porta(id)
                )
    """

    # Execute the SQL commands
    try:
        cursor.execute(createTablePorta)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(insertPorta)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(createTableTentativas)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(createTableMovimentos)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(createTablePresenca)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    print("Tables created successfully.")

    def on_connect(client, userdata, flags, rc, properties=None):
      print("Connected with code "+str(rc))
      client.subscribe("sensor/dados/#")

    def on_message(client, userdata, msg):
      print("Message received on topic: " + msg.topic)
      print("Message: " + str(msg.payload.decode()))

    cursor = con.cursor()
    if msg.topic == 'sensor/dados/senha':
        cursor.execute("INSERT INTO Tentativas (id_porta, horas, senha) VALUES (1, %s, %s)", (datetime.now().strftime('%Y-%m-%d %H:%M:%S'), str(msg.payload.decode())))
        con.commit()
    elif msg.topic == 'sensor/dados/distancia':
        if int(str(msg.payload.decode())) <= 30:
            cursor.execute("INSERT INTO Movimentos (horas, id_porta) VALUES (%s, 1)", (datetime.now().strftime('%Y-%m-%d %H:%M:%S'),))
            con.commit()
    elif msg.topic == 'sensor/dados/presenca':
        if str(msg.payload.decode()) == 'Movimento detectado':
            cursor.execute("INSERT INTO Presenca (horas, id_porta) VALUES (%s, 1)", (datetime.now().strftime('%Y-%m-%d %H:%M:%S'),))
            con.commit()
    cursor.close()

    if _name_ == '_main_':
      client = mqtt.Client()
      client.on_connect = on_connect
      client.on_message = on_message

    client.connect("test.mosquitto.org", 1883, 60)

    client.loop_forever()

#

## Código Camera 
    #include <WiFi.h>
    #include <PubSubClient.h>
    #include "esp_camera.h"
    #include "esp_timer.h"
    #include "img_converters.h"
    #include "Arduino.h"
    #include "fb_gfx.h"
    #include "soc/soc.h" 
    #include "soc/rtc_cntl_reg.h"  
    #include "esp_http_server.h"
    #include <Base64.h>

    // Definicoes para a camera
    #define PWDN_GPIO_NUM     32
    #define RESET_GPIO_NUM    -1
    #define XCLK_GPIO_NUM      0
    #define SIOD_GPIO_NUM     26
    #define SIOC_GPIO_NUM     27
    #define Y9_GPIO_NUM       35
    #define Y8_GPIO_NUM       34
    #define Y7_GPIO_NUM       39
    #define Y6_GPIO_NUM       36
    #define Y5_GPIO_NUM       21
    #define Y4_GPIO_NUM       19
    #define Y3_GPIO_NUM       18
    #define Y2_GPIO_NUM        5
    #define VSYNC_GPIO_NUM    25
    #define HREF_GPIO_NUM     23
    #define PCLK_GPIO_NUM     22

    #define PART_BOUNDARY "123456789000000000000987654321"
    static const char* _STREAM_CONTENT_TYPE = "multipart/x-mixed-replace;boundary=" PART_BOUNDARY;
    static const char* _STREAM_BOUNDARY = "\r\n--" PART_BOUNDARY "\r\n";
    static const char* _STREAM_PART = "Content-Type: image/jpeg\r\nContent-Length: %u\r\n\r\n";


    httpd_handle_t stream_httpd = NULL;

    static esp_err_t stream_handler(httpd_req_t *req){
      camera_fb_t * fb = NULL;
      esp_err_t res = ESP_OK;
      size_t _jpg_buf_len = 0;
      uint8_t * _jpg_buf = NULL;
      char * part_buf[64];

      res = httpd_resp_set_type(req, _STREAM_CONTENT_TYPE);
      if(res != ESP_OK){
        return res;
      }

      while(true){
    fb = esp_camera_fb_get();
    if (!fb) {
      Serial.println("Camera capture failed");
      res = ESP_FAIL;
    } else {
      if(fb->width > 400){
        if(fb->format != PIXFORMAT_JPEG){
          bool jpeg_converted = frame2jpg(fb, 80, &_jpg_buf, &_jpg_buf_len);
          esp_camera_fb_return(fb);
          fb = NULL;
          if(!jpeg_converted){
            Serial.println("JPEG compression failed");
            res = ESP_FAIL;
          }
        } else {
          _jpg_buf_len = fb->len;
          _jpg_buf = fb->buf;
        }
      }
    }
    if(res == ESP_OK){
      size_t hlen = snprintf((char *)part_buf, 64, _STREAM_PART, _jpg_buf_len);
      res = httpd_resp_send_chunk(req, (const char *)part_buf, hlen);
    }
    if(res == ESP_OK){
      res = httpd_resp_send_chunk(req, (const char *)_jpg_buf, _jpg_buf_len);
    }
    if(res == ESP_OK){
      res = httpd_resp_send_chunk(req, _STREAM_BOUNDARY, strlen(_STREAM_BOUNDARY));
    }
    if(fb){
      esp_camera_fb_return(fb);
      fb = NULL;
      _jpg_buf = NULL;
    } else if(_jpg_buf){
      free(_jpg_buf);
      _jpg_buf = NULL;
    }
    if(res != ESP_OK){
      break;
    }
      }
      return res;
    }

    /* Definicoes para o MQTT */
    #define TOPICO_CAPTURE_PHOTO   "capture_photo"
    #define TOPICO_PUBLISH_PHOTO   "iot_photo"
    #define ID_MQTT  "IoT_PUC_SG_mqtt"     //id mqtt (para identificação de sessão)

    const char* BROKER_MQTT = "test.mosquitto.org";
    int BROKER_PORT = 1883; // Porta do Broker MQTT

    const char* SSID = "Alex";
    const char* PASSWORD = "12341234";

    // const char* SSID = "2G-NETVIRTUA-1602";
    // const char* PASSWORD = "1682499700";

    WiFiClient espClient; 
    PubSubClient MQTT(espClient); 

    long numAleatorio;

    void initWiFi(void);
    void initMQTT(void);
    void mqtt_callback(char* topic, byte* payload, unsigned int length);
    void reconnectMQTT(void);
    void reconnectWiFi(void);
    void VerificaConexoesWiFIEMQTT(void);

    void initWiFi(void){
      delay(10);
      Serial.println("------Conexao WI-FI------");
      Serial.print("Conectando-se na rede: ");
      Serial.println(SSID);
      Serial.println("Aguarde");

      reconnectWiFi();
    }

    void initMQTT(void){
      MQTT.setServer(BROKER_MQTT, BROKER_PORT);   //informa qual broker e porta deve ser conectado
      MQTT.setCallback(mqtt_callback);            //atribui função de callback (função chamada quando qualquer informação de um dos tópicos subescritos chega)
    }

    void capturePhotoAndSend() {
      camera_fb_t * fb = esp_camera_fb_get();
      if (!fb) {
        Serial.println("Camera capture failed");
    return;
      }
      String base64Image = base64::encode(fb->buf, fb->len);
    
      // --------------------------------esse aki funciona:
      // const char* a = "aa";
      // MQTT.publish(TOPICO_PUBLISH_PHOTO, a);

      // -------------------------------esse aki nn:
      MQTT.publish(TOPICO_PUBLISH_PHOTO, base64Image.c_str());

      Serial.println(base64Image.c_str());
      esp_camera_fb_return(fb);
    }

    void mqtt_callback(char* topic, byte* payload, unsigned int length){
      String msg;

      for (int i = 0; i < length; i++)
      {
        char c = (char)payload[i];
        msg += c;
      }
      Serial.print("Chegou a seguinte string via MQTT: ");
      Serial.println(msg);
      if(msg[0] == '1'){
        capturePhotoAndSend();
      }
    }

    void reconnectMQTT(void){
      while (!MQTT.connected())
      {
        Serial.print("* Tentando se conectar ao Broker MQTT: ");
    Serial.println(BROKER_MQTT);
    if (MQTT.connect(ID_MQTT))
    {
      Serial.println("Conectado com sucesso ao broker MQTT!");
      MQTT.subscribe(TOPICO_CAPTURE_PHOTO);
    }
    else
    {
      Serial.println("Falha ao reconectar no broker.");
      Serial.println("Havera nova tentativa de conexao em 2s");
      delay(2000);
    }
      }  
    }

    void VerificaConexoesWiFIEMQTT(void){
      if (!MQTT.connected())
        reconnectMQTT(); //se não há conexão com o Broker, a conexão é refeita

      reconnectWiFi(); //se não há conexão com o WiFI, a conexão é refeita
    }

    void reconnectWiFi(void){
      //se já está conectado à rede WI-FI, nada é feito.
      //Caso contrário, são efetuadas tentativas de conexão
      if (WiFi.status() == WL_CONNECTED)
    return;

      WiFi.begin(SSID, PASSWORD); // Conecta na rede WI-FI

      while (WiFi.status() != WL_CONNECTED)
      {
    delay(100);
    Serial.print(".");
      }

      Serial.println();
      Serial.print("Conectado com sucesso na rede ");
      Serial.print(SSID);
      Serial.println("\nIP obtido: ");
      Serial.println(WiFi.localIP());
    }

    void setup() {
      WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0);
      Serial.begin(9600);
      Serial.setDebugOutput(false);
      camera_config_t config;
      config.ledc_channel = LEDC_CHANNEL_0;
      config.ledc_timer = LEDC_TIMER_0;
      config.pin_d0 = Y2_GPIO_NUM;
      config.pin_d1 = Y3_GPIO_NUM;
      config.pin_d2 = Y4_GPIO_NUM;
      config.pin_d3 = Y5_GPIO_NUM;
      config.pin_d4 = Y6_GPIO_NUM;
      config.pin_d5 = Y7_GPIO_NUM;
      config.pin_d6 = Y8_GPIO_NUM;
      config.pin_d7 = Y9_GPIO_NUM;
      config.pin_xclk = XCLK_GPIO_NUM;
      config.pin_pclk = PCLK_GPIO_NUM;
      config.pin_vsync = VSYNC_GPIO_NUM;
      config.pin_href = HREF_GPIO_NUM;
      config.pin_sscb_sda = SIOD_GPIO_NUM;
      config.pin_sscb_scl = SIOC_GPIO_NUM;
      config.pin_pwdn = PWDN_GPIO_NUM;
      config.pin_reset = RESET_GPIO_NUM;
      config.xclk_freq_hz = 20000000;
      config.pixel_format = PIXFORMAT_JPEG; 
  
      config.frame_size = FRAMESIZE_UXGA;
      config.jpeg_quality = 40;
      config.fb_count = 2;

      // Inicia a câmera
      esp_err_t err = esp_camera_init(&config);
      if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return;
      }

      /* Inicializa a conexao wi-fi */
      initWiFi();
      /* Inicializa a conexao ao broker MQTT */
      initMQTT();
    }

    void loop() {
      VerificaConexoesWiFIEMQTT();
      /*  Publica a temperatura */
      /* keep-alive da comunicação com broker MQTT */
      MQTT.loop();
      /* Refaz o ciclo após 2 segundos */
      delay(2000);
    }

  #
  
  ## Código do template em python

      import paho.mqtt.client as mqtt
    import mysql.connector
    from datetime import datetime

    # Create a connection object
    con = mysql.connector.connect(host='localhost', database='trabalhoiot', user='root', password='ECGJOOAB')

    # Check if the connection was successful
    if con.is_connected():
    db_info = con.get_server_info()
    print("Connected successfully to the server ", db_info)

    # Create a cursor object
    cursor = con.cursor()

    # Check the current database
    cursor.execute("select database();")
    linha = cursor.fetchone()
    print("Connected to DB", linha)

    # SQL commands to create the tables
    createTablePorta = """
                CREATE TABLE IF NOT EXISTS Porta (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    senha INT
                );
            """
    insertPorta = """
                INSERT INTO Porta (id, presenca, senha) VALUES (1, '0000-01-01 00:00:00', 1234)
                ON DUPLICATE KEY UPDATE presenca=VALUES(presenca), senha=VALUES(senha);
    """
    createTableTentativas = """
                CREATE TABLE IF NOT EXISTS Tentativas (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    horas DATETIME,
                    id_porta INT,
                    senha INT,
                    FOREIGN KEY (id_porta) REFERENCES Porta(id)
                );
            """
    createTableMovimentos = """
                CREATE TABLE IF NOT EXISTS Movimentos (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    horas DATETIME,
                    id_porta INT,
                    FOREIGN KEY (id_porta) REFERENCES Porta(id)
                );
    """

    # Execute the SQL commands
    try:
        cursor.execute(createTablePorta)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(insertPorta)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(createTableTentativas)
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    try:
        cursor.execute(createTableMovimentos) # se distancia < 30cm
        con.commit()
    except mysql.connector.Error as err:
        print(f"Error: {err}")
    print("Tables created successfully.")

    def on_connect(client, userdata, flags, rc, properties=None):
    print("Connected with code "+str(rc))
    client.subscribe("sensor/dados/#")

    def on_message(client, userdata, msg):
    print("Message received on topic: " + msg.topic)
    print("Message: "+ str(msg.payload.decode()))

    cursor = con.cursor()
    if msg.topic == 'sensor/dados/senha':
        cursor.execute(f"INSERT INTO Tentativas (id_porta, horas, senha) VALUES (1, {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}, {str(msg.payload.decode())})")
        con.commit()
    elif msg.topic == 'sensor/dados/distancia':
        if(int(str(msg.payload.decode())) <= 30):
            cursor.execute(f"INSERT INTO Movimentos (horas, id_porta) VALUES ({datetime.now().strftime('%Y-%m-%d %H:%M:%S')}, 1)")
            con.commit()
    myresult = cursor.fetchall()
    print(myresult)

    if _name_ == '_main_':
    client = mqtt.Client()
    client.on_connect = on_connect
    client.on_message = on_message

    client.connect("test.mosquitto.org", 1883, 60)

    client.loop_forever()
