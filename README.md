#include <Arduino.h>
#include <MFRC522.h>

#define SS_PIN 10
#define RST_PIN 9
MFRC522 mfrc522(SS_PIN, RST_PIN);

void setup() {
  pinMode(5, OUTPUT);
  pinMode(6, OUTPUT);
  pinMode(7, INPUT);
  pinMode(8, INPUT);

  Serial.begin(9600);
  SPI.begin();
  mfrc522.PCD_Init();
  mfrc522.PCD_SetRegisterBitMask(mfrc522.RFCfgReg, (0x07 << 4)); // Aumentar distância de leitura
}

void loop() {
  int fc1 = digitalRead(7);
  int fc2 = digitalRead(8);

  if (!mfrc522.PICC_IsNewCardPresent())
  {
    return;
  }

  if (!mfrc522.PICC_ReadCardSerial())
  {
    return;
  }

  String conteudo = "";
  byte letra;
  for (byte i = 0; i < mfrc522.uid.size; i++)
  {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
    Serial.print(mfrc522.uid.uidByte[i], HEX);
    conteudo.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
    conteudo.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  conteudo.toUpperCase();

  if (fc1 == HIGH)
  {
    do{digitalWrite(5, HIGH);  
    }while (conteudo.substring(1) == "BC 3B 3A E9");

    do{digitalWrite(6, LOW);  
    }while (conteudo.substring(1) != "BC 3B 3A E9");
  }

  if (fc2 == HIGH)
  {
    do{digitalWrite(5, LOW);  
    }while (conteudo.substring(1) == "BC 3B 3A E9");

    do{digitalWrite(6, HIGH);  
    }while (conteudo.substring(1) != "BC 3B 3A E9");
  } 
}
