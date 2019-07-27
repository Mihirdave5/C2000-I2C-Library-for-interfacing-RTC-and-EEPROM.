//AUTHOR:Mihir Dave
//Email:mihirdave36@gmail.com
//RTC & EEPROM Interfacing module data acquired using polling operation
#include "DSP28x_Project.h"
#define I2C_TIMEOUT ((Uint16) 1024) //Delay value
//**********************************************************
//**********PROTOTYPE OF FUNCTION IN I2C MODULE*************
void rtc_write(Uint16 location,Uint16 data); //write function
void eeprom_write(Uint16 high_add,Uint16 low_add,Uint16 data); //EEPROM write function
Uint16 rtc_read(Uint16 location);  //read function
Uint16 eeprom_read(Uint16 high_add,Uint16 low_add); //EEPROM read function
void rtc_Init(void); //RTC Initializing function
void i2c_Config(void); //GPIO configuration for I2C
void wait_delay(void); //wait function
//**********************************************************
int rtc_data; //data store variable


void i2c_Config(void){
    EALLOW;                //enable's registers data write
    GpioCtrlRegs.GPBQSEL1.bit.GPIO32 = 3; //selecting ASYNC mode
    GpioCtrlRegs.GPBQSEL1.bit.GPIO33 = 3; //selecting ASYNC mode
    GpioCtrlRegs.GPBMUX1.bit.GPIO32 = 1; //selecting pin as SCL
    GpioCtrlRegs.GPBMUX1.bit.GPIO33 = 1; //selecting pin as SDA
    SysCtrlRegs.PCLKCR0.bit.I2CAENCLK = 1; //start of i2c clock
    EDIS;                //disable's registers data write
}

void rtc_Init(void){
    I2caRegs.I2CSAR= 0x0068;     // 7-bit Slave address
    I2caRegs.I2CPSC.all = 19;    // I2C clock between 7Mhz-12Mhz
    I2caRegs.I2CCLKL = 45;      // Prescalers set for 100kHz bit rate
    I2caRegs.I2CCLKH = 45;      // At a 10Mhz I2C clock
    I2caRegs.I2CFFTX.all = 0x6000;    // Enable FIFO mode and TXFIFO
    I2caRegs.I2CFFRX.all = 0x2040;    // Enable RXFIFO, clear RXFFINT
    return;
}

void rtc_write(Uint16 location,Uint16 data){
    I2caRegs.I2CMDR.all = 0x2E20;    //START condition, STOP condition, Master, Transmitter, I2C enabled
    I2caRegs.I2CCNT = 0x02;          // bytes to send - 2 here
    I2caRegs.I2CDXR= location; // register location
    I2caRegs.I2CDXR= data;   // register location
    Uint16 timeout_copy;            // timeout period
    timeout_copy = I2C_TIMEOUT;     // get time out value
    while ((I2caRegs.I2CMDR.bit.STP == 1) && (timeout_copy > 0u)){//wait for i2c  bus to end any previous communication
        timeout_copy--;
    }
}

Uint16 rtc_read(Uint16 location){
        I2caRegs.I2CMDR.all = 0x2E20; //START condition, STOP condition, Master, Transmitter, I2C enabled
        I2caRegs.I2CCNT = 0x01;                 // bytes to send - always 1
        I2caRegs.I2CDXR= location; // register location
        Uint16 timeout_copy = I2C_TIMEOUT;            // timeout period
        while ((I2caRegs.I2CMDR.bit.STP == 1) && (timeout_copy > 0u)){
            timeout_copy--; //wait for i2c  bus to end any previous communication
            }
        I2caRegs.I2CMDR.all = 0x2C20; // Start condition, Stop condition, Master, I2C enabled
        timeout_copy = I2C_TIMEOUT;     // get time out value
        while ((I2caRegs.I2CSTR.bit.BB == 1) && (timeout_copy > 0u)){
            timeout_copy--;  //wait for to bus free
            }
        I2caRegs.I2CCNT = 0x01;         // bytes to receive - always 1
        return I2caRegs.I2CDRR;     // read and return data
}

void wait_delay(void){
    long i;
    for (i = 0; i < 10000; i++){

    }
}

//***************EEPROM SECTION******************************
void eeprom_write(Uint16 high_add,Uint16 low_add,Uint16 data){
    I2caRegs.I2CMDR.all = 0x2E20; //START condition, STOP condition, Master, Transmitter, I2C enabled
    I2caRegs.I2CCNT = 0x04;                 // bytes to send - 2 here
    I2caRegs.I2CDXR= high_add; // High order address
    I2caRegs.I2CDXR= low_add;   // Low order address
    I2caRegs.I2CDXR= data;  //current address data
    I2caRegs.I2CDXR= data;  //consecutive next address data
    Uint16 timeout_copy;            // timeout period
    timeout_copy = I2C_TIMEOUT;     // get time out value
    while ((I2caRegs.I2CMDR.bit.STP == 1) && (timeout_copy > 0u)){
        timeout_copy--;       //wait for i2c  bus to end any previous communication
        }
    //I2caRegs.I2CFFTX.all = 0x6040;
}

Uint16 eeprom_read(Uint16 high_add,Uint16 low_add){
    I2caRegs.I2CMDR.all = 0x2E20; //START condition, STOP condition, Master, Transmitter, I2C enabled
    I2caRegs.I2CCNT = 0x02;                 // bytes to send - always 1
    I2caRegs.I2CDXR= high_add; // high order address
    I2caRegs.I2CDXR= low_add;  // low order address
    Uint16 timeout_copy = I2C_TIMEOUT; // timeout period
    while ((I2caRegs.I2CMDR.bit.STP == 1) && (timeout_copy > 0u)){
        timeout_copy--; //wait for i2c  bus to end any previous communication
        }
    I2caRegs.I2CMDR.all = 0x2C20; // Start condition, Stop condition, Master, I2C enabled
    timeout_copy = I2C_TIMEOUT;     // get time out value
    while ((I2caRegs.I2CSTR.bit.BB == 1) && (timeout_copy > 0u)){
        timeout_copy--;  //wait for to bus free
        }
    I2caRegs.I2CCNT = 0x01;     // bytes to receive - always 1
    return I2caRegs.I2CDRR;     // read and return data
}
