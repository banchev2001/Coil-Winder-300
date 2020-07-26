/*      Coil Winder Software
        Developer: Stoian Banchev
        Year Started 2017

 *      This is second version with two steper motors!
 *      Is started in 2019.

*/

//Include LCD library
#include <LiquidCrystal.h>
#include <EEPROM.h>
// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(14, 15, 16, 17, 18, 19);

// defines pins numbers INPUTS

const int LeftButton = 5;
const int RightButton = 6;
const int StartButton = 7;
const int StopButton = 4;
const int Up_button = 8;
const int Down_button = 9;

const int Right_ES = 3;
const int Left_ES = 2;


// defines pins numbers OUTPUTS
const int stepPin = 13;
const int dirPin = 12;
const int stepPinM = 11;
const int ePinM = 10;

//Communication varibels
//========================================================================
 char recived_byte; //приемания по сериния порт символ
 char msg [50]=" "; //Масив от символи, в които се съдържат параметрите
 char TempParam [20] = " "; //Променлива за времено съхранение на параметъра, като променлива 
 int index_com = 0; // индексна променлива за приемане на данните от серииния порт

// Coil Parameters and varibels
//======================================================
unsigned int Counts = 100;
unsigned int Width = 7000;  // Width of coil 100 = 1mm
unsigned int Pitch = 150;    //Pitch of coil 100 = 1mm
unsigned int MaxSpeed = 20;
unsigned int StopInLyer = 1; // Stop evry lyer
signed int ZeroOffset = 0; //offset of home

unsigned long inxTemp;//Ползва се запазване на направените стъпки в момент на авариино спиране


int CountPerLyer;// Показва колко навивки има в един слой
int PitchRatio; //Показва за колко стътпки на главния мотор слайдера прави една негова
int CurrentPitchRatio;//Ползва се за моментно следене на достигнатия брой стъпки при, който слайдера трябва да направи неговата
int CurrentCountsSteps;// Спомагателна променлива отброява 3200 стъпки и отчита направата на една навивка
int pulseWidth = 150; //Определя стартовата скорост на мотора
int startSteps = 0; // използва се отброяване на стъпките до пълното ускоряване на мотора, ползват се за спирането
char MaxSpeedFlagBit = 0; //флаг установява се в еденица при достигане на максимална скорост
char StopFlag = 0;

float CountPerLyer_f;//Ползва се за закръгляване на бройките в слоя
float PitchRatio_f; //Ползва се за закръгляване на стъпките на слайдера за един оборот

//Използват се за визуализиране на реалните параметри
float Width_mm;
float Pitch_mm;
float ZeroOffset_mm;
//======================================================


int CurrentCounts = 0;
int LyerCounts;
//int i = 0; // declare index varible
unsigned long CountSteps;
int button_i = 0;
int menu_index = 0;
char emergStop = 0;

//процедура показване на екран при стартиране
//======================================================
void  starting_display() {

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Coil Winder 300");
  lcd.setCursor(0, 1);
  lcd.print("ver. 2.0");
  lcd.setCursor(0, 2);
  lcd.print("Created by:");
  lcd.setCursor(0, 3);
  lcd.print("S. Banchev 2019");



}

//процедура показване на основен екран
//======================================================
void  main_display() {

  if (Pitch == 0) CountPerLyer = 1;
  else {
    //CountPerLyer_f = Width / (float)Pitch;
    //CountPerLyer = CountPerLyer_f + 0.5;
    CountPerLyer = Width / Pitch;
  }

  Width_mm =  Width * 0.01;
  Pitch_mm = Pitch  * 0.01;
  //lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("  Summary Settings");

  lcd.setCursor(0, 1);
  lcd.print("====================");

  lcd.setCursor(0, 2);
  lcd.print("W:");
  lcd.setCursor(2, 2);
  lcd.print(Counts);

  lcd.setCursor(10, 2);
  lcd.print("P:");
  lcd.setCursor(12, 2);
  lcd.print(Pitch_mm);
  lcd.print("mm");

  lcd.setCursor(0, 3);
  lcd.print("L:");
  lcd.setCursor(2, 3);
  lcd.print(Width_mm);
  lcd.print("mm WPL:");
  lcd.print(CountPerLyer);

}

//процедура показване 1 точка от менюто Slider Set
//======================================================
void  menu1_display() {

  ZeroOffset_mm = ZeroOffset * 0.01;


  lcd.setCursor(0, 0);
  lcd.print("1. Slider set:");
  lcd.setCursor(0, 1 );
  lcd.print("====================");
  lcd.setCursor(0, 2);
  lcd.print("< Go to zero");
  lcd.setCursor(0, 3);
  lcd.print("> Set zero");
}

//процедура показване 2 точка от менюто Windings set
//======================================================
void  menu2_display() {

  //lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("2. Windings set:");
  lcd.setCursor(0, 1 );
  lcd.print("====================");
  lcd.setCursor(0, 2);
  lcd.print("                    ");
  lcd.setCursor(0, 2);
  lcd.print(Counts);
  lcd.print(" Counts");
  //lcd.setCursor(0,3 );
  //lcd.print("<Dec  Inc>");
}

//процедура показване 3 точка от менюто Pitch set
//======================================================
void  menu3_display() {

  Pitch_mm = Pitch * 0.01;

  //lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("3. Pitch set:");
  lcd.setCursor(0, 1 );
  lcd.print("====================");
  lcd.setCursor(0, 2);
  lcd.print(Pitch_mm);
  lcd.print(" mm");
  // lcd.setCursor(0,3 );
  // lcd.print("<Dec  Inc>");
}


//процедура показване 4 точка от менюто Coil Length
//======================================================
void  menu4_display() {

  Width_mm =  Width * 0.01;

  //lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("4. Coil Length:");
  lcd.setCursor(0, 1 );
  lcd.print("====================");
  lcd.setCursor(0, 2);
  lcd.print("                    ");
  lcd.setCursor(0, 2);
  lcd.print(Width_mm);
  lcd.print(" mm");
  //lcd.setCursor(0,3 );
  //lcd.print("<Dec  Inc>");
}

//процедура показване 5 точка от менюто Stop every layer
//======================================================
void  menu5_display() {

  Width_mm =  Width * 0.01;

  //lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("5. Stop every layer:");
  lcd.setCursor(0, 1 );
  lcd.print("====================");
  lcd.setCursor(0, 2);
  lcd.print("                    ");
  lcd.setCursor(0, 2);
  if (StopInLyer == 1 )lcd.print("Yes"); else lcd.print("No");


  //lcd.setCursor(0,3 );
  //lcd.print("<Dec  Inc>");
}
//процедура показване 6 точка от менюто Save settings
//======================================================
void  menu6_display() {

  //lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("6. Save settings:");
  lcd.setCursor(0, 1 );
  lcd.print("====================");
  lcd.setCursor(0, 2);
  lcd.print("Press < or > to save");
  lcd.setCursor(0, 3);
  lcd.print("Settings in EEPROM");
}

//процедура паркиране на слайдера в нулева точка
//======================================================
void go_home() {

  if (ZeroOffset < 0) {

    digitalWrite(dirPin, HIGH);
    for (int x = 0; x < abs(ZeroOffset); x++) {


      digitalWrite(stepPin, HIGH);
      delayMicroseconds(250);
      digitalWrite(stepPin, LOW);
      delayMicroseconds(250);

    }

  }


  digitalWrite(dirPin, LOW);
  for (int x = 0; x < ZeroOffset; x++) {


    digitalWrite(stepPin, HIGH);
    delayMicroseconds(200);
    digitalWrite(stepPin, LOW);
    delayMicroseconds(200);


  }

  ZeroOffset = 0;

}

//Продцедура по записване на настройките в еепром паметта
//===========================================================================
void save_settings() {


  EEPROM.put(0, Counts);
  EEPROM.put(5, Width);
  EEPROM.put(10, Pitch);
  EEPROM.put(15, StopInLyer );

}


//Продцедура по записване на настройките в еепром паметта
//===========================================================================
void ini_settings() {


  EEPROM.put(0, 0);
  EEPROM.put(5, 0);
  EEPROM.put(10, 0);
  EEPROM.put(15, StopInLyer );

}

//Продцедура по записване на настройките в еепром паметта
//===========================================================================
void load_settings() {

  EEPROM.get(0, Counts);
  EEPROM.get(5, Width);
  EEPROM.get(10, Pitch);
  EEPROM.get(15, StopInLyer );


}

//===========================================================================
//Start configuration
//===========================================================================

void setup() {

  // Start serial communication for bluethooth 
  Serial.begin(9600);
  
  // Sets the two pins as Outputs
  pinMode(stepPin, OUTPUT);
  pinMode(dirPin, OUTPUT);

  pinMode(stepPinM, OUTPUT);
  pinMode(ePinM, OUTPUT);


  pinMode(LeftButton, INPUT);
  pinMode(RightButton, INPUT);
  pinMode(StartButton, INPUT);
  pinMode(StopButton, INPUT);
  pinMode(Up_button, INPUT);
  pinMode(Down_button, INPUT);

  pinMode(Right_ES, INPUT);
  pinMode(Left_ES, INPUT);

  digitalWrite(dirPin, HIGH);

  //Махат се коментарите, когато се записва за първи път след това пак се слагат
  //ini_settings();




  // set up the LCD's number of columns and rows:
  lcd.begin(20, 4);

  starting_display();
  load_settings();

  delay(2500);

  lcd.clear();
  main_display();

}



//====================================================================================================
//ОСНОВНИЯ ЦИКЪЛ НА ПРОГРАМАТА ЗАПОЧВА ОТ ТУК
//====================================================================================================




void loop() {

//Стартира кода за получаванне на данни чрез блутут
//===============================================================
  if (Serial.available()&& emergStop ==0)
  {

    recived_byte = Serial.read();

    if (recived_byte == 'X') {

      msg [index_com] = recived_byte;
      index_com++;
      msg [index_com] = '\0';
      index_com = 0;
      Serial.println(msg);

      //  /*
      //Тук трябва да се помести кода за напъхването на данните в променливите
      //----------------------------------------------------------------------
      byte i2 = 0; // брои символите на всеки параметър
      byte pi = 0; // брои параметрите

      for (byte i1 = 0 ; i1 < sizeof(msg) - 2; i1++) {


        if (msg[i1] == 'X') break; //ако си получил край на съобщението излезни от цикъла

        if (msg[i1] != '|') { //Получил ли си символ за края на параметър НЕ

          //Тъй като понякога праща символи проверяваме символа дали е число
          if (isDigit(msg[i1])) { //само ако е число се съхранява в полученото съобщение
            TempParam [i2] = msg[i1];
            i2++;
          }
        }

        else { //Поличил съм символ за край на параметър

          if (pi == 0) { //Ако пи=0 това е първия параметър
            TempParam [i2] = '\0';
            Counts = atoi(TempParam);
            i2 = 0;
          }

          if (pi == 1) { //Ако пи=1 това е втория параметър
            TempParam [i2] = '\0';
            Pitch = atoi(TempParam);
            i2 = 0;
          }

          if (pi == 2) { //Ако пи=2 това е третия параметър
            TempParam [i2] = '\0';
            Width = atoi(TempParam);
            i2 = 0;
          }

          if (pi == 3) { //Ако пи=3 това е четвъртия параметър

            if(TempParam [i2 - 1]=='0')StopInLyer = 0;
            else StopInLyer = 1;
            
          }


          pi++; //увеличи следващия параметър

        }


      }//end for


     //Тук поместваме кода за визуализация на дисплея
      lcd.clear();
      lcd.setCursor(0, 1);
      lcd.print("The parameters are");
      lcd.setCursor(0, 2);
      lcd.print("changed via BT");
      save_settings();
      delay(4000);
      lcd.clear();
         
      main_display();

     
    }


    else if (recived_byte == '?') {

      Serial.print(Counts);
      Serial.print('|');
      Serial.print(Pitch);
      Serial.print('|');
      Serial.print(Width);
      Serial.print('|');
      Serial.print(StopInLyer);
      Serial.print('|');
      Serial.print('X');
      //Serial.println();
    }
    else {

      //Serial.print(recived_byte);
      msg [index_com] = recived_byte;
      index_com++;

    }


  }//Край на код за комуникация




  //Реакция при натискане на Старт бутона (забранява се пуск, ако
  //---------------------------------------------------------------------

  if (digitalRead(StartButton) == 0 && menu_index == 0 && emergStop != 2 && digitalRead(Left_ES) == 1 && digitalRead(Right_ES) == 1) {

    //променливи отговорни за плавното тръгване на мотора
    MaxSpeedFlagBit=0; //Нулиране на стартовия флаг за развита максимална скорост
    pulseWidth = 700; //Определя стартовата скорост на мотора
    startSteps = 0; // използва се отброяване на стъпките до пълното ускоряване на мотора, ползват се за спирането
    StopFlag = 0;
    
    if (emergStop == 0) { //Имало ли е авариино  спиране?

      digitalWrite(dirPin, HIGH); //Не нямало е авариино спиране нулирай броячите и почни да редиш проводника наляво
      CountSteps = (long)Counts * 3200; //Задава колко стъпки трябва да направи мотора за да направи всичките навивки
      CurrentCountsSteps = 0;
      CurrentCounts = 0;
      CurrentPitchRatio = 0;
      LyerCounts = 0;
      inxTemp = 0;

      

      digitalWrite(ePinM, LOW);
      delay(80);
    }

    else {
      emergStop = 0;   // Имало е авариино спиране нулирай флага, който го регистрира
      startSteps = 0;
    }

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Runing...");
    lcd.setCursor(0, 1);
    lcd.print("Target: ");
    lcd.print(Counts);
    lcd.setCursor(0, 2);
    lcd.print("Complete: ");
    lcd.print(CurrentCounts);




    digitalWrite(ePinM, LOW);
    delay(300);





    PitchRatio = 3200 / Pitch; //Изчислява на колко стъпки на основния мотор да премести слайдера

    for (long inx = inxTemp; inx <= CountSteps; inx++) {

      CurrentCountsSteps++;
      CurrentPitchRatio++;

      digitalWrite(stepPinM, LOW);
      delayMicroseconds(pulseWidth);
      digitalWrite(stepPinM, HIGH);
      delayMicroseconds(pulseWidth);

      if (PitchRatio <= CurrentPitchRatio) {

        CurrentPitchRatio = 0;

        digitalWrite(stepPin, HIGH);
        delayMicroseconds(60);
        digitalWrite(stepPin, LOW);
        delayMicroseconds(60);

        if (digitalRead(dirPin)) { //Отчитане на преместването от стартовата точка
          ZeroOffset++;
        }
        else {
          ZeroOffset--;
        }

      }




      if (CurrentCountsSteps == 3200) { // Увеличи текущите навивки ако е направил един оборот мотора

        CurrentCountsSteps = 0;
        CurrentCounts++;
        LyerCounts++;



       lcd.setCursor(10, 2);
       lcd.print(CurrentCounts);



        if (LyerCounts == CountPerLyer) { // Обръща слайдера, когатро стигне края на бобината
          digitalWrite(dirPin, !digitalRead(dirPin)); // Reverse wire slider
          LyerCounts = 0;

          if (StopInLyer == 1 && Counts > CurrentCounts) {
            emergStop = 1;
            inxTemp = inx;
            break;
          }

        }


      }

                       //МАКСИМАЛМА СКОРОСТ
        //Алгоритъм за плавно пускане
        if (pulseWidth > MaxSpeed && (!MaxSpeedFlagBit) ){ //достигнал ли е дължината на импулса такъв за максимална скорост и ускорил ли е напълно мотора
          pulseWidth --; // не са изпълнени условията увеличи скоростта като съкратиш дължината на импулса
          startSteps ++; //отброи колко пъти става това до пълното ускорен

            if (pulseWidth <= MaxSpeed )MaxSpeedFlagBit=1;
            
        }

        //Алгоритъм за плавно спиране при край на програмата
        if (inx >= (CountSteps - startSteps)){ //наближава ли края в който трябва да започне спирането на мотора
        pulseWidth ++;// да увеличи дължината на импулса
        
        }

        //Алгоритъм за плавно спиране в края на слоя, когато е въведена функция стоп във всеки слой
        if (StopInLyer == 1 && (LyerCounts + 1) == CountPerLyer && CurrentCountsSteps >= (3200 -startSteps)){//наближава ли края на слоя

         pulseWidth ++;// да увеличи дължината на импулса
         
        }

        //Алгоритъм за плавно спиране при натискане на стоп бутона
        if (StopFlag == 1){//наближава ли края на слоя

         MaxSpeedFlagBit=1;
         pulseWidth ++;// да увеличи дължината на импулса
        
          if(pulseWidth>=500){
            emergStop = 1;
            inxTemp = inx;
            break;
            }
          }
       


      // Тук помести алгоритъма за авариино спиране чрез стоп бутона
      if (digitalRead(StopButton) == 0) { //Прекъсване на навиването, ако се натисне стоп бутона
        StopFlag = 1;
        //emergStop = 1;
        //inxTemp = inx;
       // break;
       }

      // Тук помести алгоритъма за авариино спиране от крайни изключватели
      if (digitalRead(Left_ES) == 0 || digitalRead(Right_ES) == 0) { //Прекъсване на навиването, ако се натисне стоп бутона
        emergStop = 2;
        break;

      }
      

    }


    if (emergStop == 0)digitalWrite(ePinM, HIGH); //Спира тока през основния мотор



   
   
    if (emergStop == 0) {

      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Coil is ready!");
      lcd.setCursor(0, 1);
      lcd.print("Press Stop button");
      lcd.setCursor(0, 2);
      lcd.print("to home slider");
     
      while(digitalRead(StopButton) != 0);
      
      lcd.setCursor(0, 3);
      lcd.print("                     ");
      lcd.setCursor(0, 3);
      lcd.print("Home Slider...");
      go_home();
      lcd.clear();
         
      main_display();
    }
    else if (emergStop == 2) {
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Stoped by end swich");
      lcd.setCursor(0, 1);
      lcd.print("Press Stop to return");
    }
    else {



      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Pause");
      lcd.setCursor(0, 1);
      lcd.print("Target: ");
      lcd.print(Counts);
      lcd.setCursor(0, 2);
      lcd.print("Complete: ");
      lcd.print(CurrentCounts);

      while (digitalRead(StopButton) == 0) { // Изчакване да отпуснем бутона
        // wait to relase the stop button
      }
      delay(30);// Изчакване да се установи бутона след отпускане
    }



  }//end StrtButton


  //Реакция при натискане на бутон < наляво
  //---------------------------------------------------------------------

  if (digitalRead(LeftButton) == 0 && emergStop == 0 ) { // Преместване на плъзгача наляво


    if (menu_index == 0 ) {

      if (digitalRead(Left_ES) == 0) {

        lcd.clear();
        lcd.setCursor(0, 1);
        lcd.print("Left End Switch");
        lcd.setCursor(0, 2);
        lcd.print("is pressed!");
        delay(2000);
        lcd.clear();
        main_display();

      }
      else {
        digitalWrite(dirPin, HIGH); // Enables the motor to move in a particular direction
        digitalWrite(stepPin, HIGH);
        delayMicroseconds(200);
        digitalWrite(stepPin, LOW);
        delayMicroseconds(200);

        ZeroOffset++;

        if (button_i < 250) delayMicroseconds(2100);
        button_i++;

      }

    }

    else if (menu_index == 1) {
      go_home();
      menu1_display();
    }
    else if (menu_index == 2) {

      if (button_i < 5) delay(200);
      button_i++;
      Counts--;
      menu2_display();
    }
    else if (menu_index == 3) {

      if (button_i < 5) delay(200);
      button_i++;
      if (Pitch > 0)  Pitch--;
      menu3_display();
    }
    else if (menu_index == 4) {

      if (button_i < 5) delay(200);
      button_i++;
      if (Width > 1)Width = Width - 10;
      menu4_display();
    }

    else if (menu_index == 5) {

      if (button_i < 5) delay(200);
      button_i++;
      if (StopInLyer == 1 )StopInLyer = 0;
      menu5_display();
    }

    else if (menu_index == 6) {
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Saving settings...");
      save_settings();
      delay(1000);
      lcd.clear();
      menu_index = 0;
      main_display();
    }





  }//end if LeftButton

  //Реакция при натискане на бутон > надясно
  //---------------------------------------------------------------------

  if (digitalRead(RightButton) == 0 && emergStop == 0 ) { //Преместване на плазгача надясно

    if (menu_index == 0) {

      if (digitalRead(Right_ES) == 0) {

        lcd.clear();
        lcd.setCursor(0, 1);
        lcd.print("Right End Switch");
        lcd.setCursor(0, 2);
        lcd.print("is pressed!");
        delay(2000);
        lcd.clear();
        main_display();

      }
      else {

        digitalWrite(dirPin, LOW); // Enables the motor to move in a particular direction
        digitalWrite(stepPin, HIGH);
        delayMicroseconds(200);
        digitalWrite(stepPin, LOW);
        delayMicroseconds(200);

        ZeroOffset--;

        if (button_i < 250) delayMicroseconds(2100);
        button_i++;
      }


    }

    else if (menu_index == 1) {
      ZeroOffset = 0;
      menu1_display();
    }
    else if (menu_index == 2) {

      if (button_i < 5) delay(200);
      button_i++;
      Counts++;
      menu2_display();
    }
    else if (menu_index == 3) {

      if (button_i < 5) delay(200);
      button_i++;
      if (Pitch < 300)Pitch++;
      menu3_display();
    }
    else if (menu_index == 4) {

      if (button_i < 5) delay(200);
      button_i++;
      if (Width < 30000)Width = Width + 10;
      menu4_display();

    }

    else if (menu_index == 5) {

      if (button_i < 5) delay(200);
      button_i++;
      if (StopInLyer == 0 )StopInLyer = 1;
      menu5_display();
    }

    else if (menu_index == 6) {
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Saving settings...");
      save_settings();
      delay(1000);
      lcd.clear();
      menu_index = 0;
      main_display();

    }




  }//end if RightButton


  //Реакция при натискане на бутон нагоре
  //---------------------------------------------------------------------

  if (digitalRead(Up_button) == 0 && emergStop == 0) { // Установяване на нулевата точка на плазгача


    lcd.clear();
    if (menu_index > 0) {
      menu_index--;
    }

    if (menu_index == 0) main_display();
    else if (menu_index == 1) menu1_display();
    else if (menu_index == 2) menu2_display();
    else if (menu_index == 3) menu3_display();
    else if (menu_index == 4) menu4_display();
    else if (menu_index == 5) menu5_display();
    else if (menu_index == 6) menu6_display();

    while (digitalRead(Up_button) == 0) { // Изчакване да отпуснем бутона
      // wait to relase the stop button
    }
    delay(30);// Изчакване да се установи бутона след отпускане


  }



  //Реакция при натискане на бутон надолу
  //---------------------------------------------------------------------

  if (digitalRead(Down_button) == 0 && emergStop == 0) { // Go to home

    // go_home();
    lcd.clear();
    if (menu_index < 6) {
      menu_index++;
    }
    else menu_index = 0;

    if (menu_index == 0) main_display();
    else if (menu_index == 1) menu1_display();
    else if (menu_index == 2) menu2_display();
    else if (menu_index == 3) menu3_display();
    else if (menu_index == 4) menu4_display();
    else if (menu_index == 5) menu5_display();
    else if (menu_index == 6) menu6_display();


    while (digitalRead(Down_button) == 0) { // Изчакване да отпуснем бутона
      // wait to relase the stop button
    }
    delay(30);// Изчакване да се установи бутона след отпускане


  }// край бутон надолу




  //Реакция при натискане на бутон стоп
  //---------------------------------------------------------------------

  if (digitalRead(StopButton) == 0 && menu_index == 0) { //Прекъсване на работата при повторно натискане на стоп бутона


    if (emergStop == 1 || emergStop == 2) {
      digitalWrite(ePinM, HIGH);//Спира тока през основния мотор
      lcd.clear();

      lcd.setCursor(0, 3);
      lcd.print("Job is cancelled!");
      delay(2000);
      emergStop = 0;
      CurrentCounts = 0;
      lcd.setCursor(0, 3);
      lcd.print("                     ");
      lcd.setCursor(0, 3);
      lcd.print("Home Slider...");
    }
    //  else if (ZeroOffset==0 && emergStop==0){

    //   }
    else {
      lcd.setCursor(0, 3);
      lcd.print("                     ");
      lcd.setCursor(0, 3);
      lcd.print("Home Slider...");
    }
    go_home();
    lcd.setCursor(0, 3);
    lcd.print("                     ");
    lcd.setCursor(0, 3);
    lcd.print("Is already home");
    delay(1000);
    lcd.clear();
    main_display();

  }//край бутон стоп



  //Условие с което се нулира отброяването на бавната реакция на бутоните "наляво, надясно"
  //----------------------------------------------------------------------------------------------
  if (/*button_i != 0 && */digitalRead(RightButton) == 1 && digitalRead(LeftButton) == 1) button_i = 0;

  //Натискане на старт бутона при натиснат краен изключвател
  //----------------------------------------------------------------------------------------------
  if (digitalRead(StartButton) == 0 && menu_index == 0  && digitalRead(Left_ES) == 0 || digitalRead(StartButton) == 0 && menu_index == 0 && digitalRead(Right_ES) == 0) {

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Some of End Switches");
    lcd.setCursor(0, 1);
    lcd.print("is pressed!");
    lcd.setCursor(0, 3);
    lcd.print("Can't start winding");
    delay(1700);
    lcd.clear();
    main_display();


  }

}// void loop !!!
