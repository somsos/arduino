# Button Class With Checking Debouncing


IMPORTANT 1: Somethimes keep reading HIGH, to solve this pinbButton must have a
contact with ground
      
```c
class ButtonReader {
  public:
    int unsigned status= LOW;
    bool printLogger = false;
    
  private:
    int unsigned pinbButton = 0;
    bool inValueChange = false;
    long unsigned totalMillisPassed = 0;
    long unsigned lastChangeMillis = 0;
    long unsigned millisPassedSinceLastChange = 0;
    long unsigned millisDelayToDetectChange = 50;



  public:
  
    ButtonReader(int pin) {
       this->pinbButton = pin;
    }

    void setupButton() {
      pinMode(this->pinbButton, INPUT);
    }
  
  
    void readButtonStatus() {
      int reading = this->readButtonStatusChekingFalsePositive();
      if(this->buttonStateHasChanged(reading)) {
        this->status = reading;
      }
      this->printTime();
    }

    bool hasTheValueChanged() {
      if(this->inValueChange) {
        this->inValueChange = false;
        return true;
      }
      return false;
    }

  private:

    int readButtonStatusChekingFalsePositive() {
      if(this->isPosibleFalsePositive() == false) {
        Serial.println("too soon: Returning laststate");
        return this->status;
      }
    
      //IMPORTANT 1
      //Somethimes keep reading HIGH, to solve this pinbButton must have a contact with ground
      int reading = digitalRead(pinbButton);
      this->printReading(reading);
      return reading;
    }

    bool isPosibleFalsePositive() {
      bool changeDetectedInAnAcceptableTime = millisPassedSinceLastChange > millisDelayToDetectChange;
      if(changeDetectedInAnAcceptableTime == false) {
        return true;
      }
    }


    bool buttonStateHasChanged(int reading) {
      bool change = reading != this->status;
      if(change) {
        lastChangeMillis = totalMillisPassed;
        this->inValueChange = true;
      }
      return change;
    }

    void printReading(int reading) {
      if(!printLogger) {
        return;
      }
      
      Serial.print("Reading: ");
      Serial.println(reading);
    
    }


    void printTime() {
      if(!printLogger) {
        return;
      }
      
      Serial.print("millisPassedSinceLastChange: ");
      Serial.println(this->millisPassedSinceLastChange);
      //Serial.print(", ");
    }
};



//##########################################################################
//##########################################################################
//##########################################################################



int pinbButton1 = 4;
ButtonReader button1(pinbButton1);

void setup() {
  button1.setupButton();
  Serial.begin(9600);
}

void loop() {
  button1.readButtonStatus();

  
  if(button1.hasTheValueChanged()) {
    printInMainApp();
  }

}

void printInMainApp() {  
  if(button1.status == HIGH) {
    Serial.println("Button on");
  } else {
    Serial.println("Button off");
  }
}

```

