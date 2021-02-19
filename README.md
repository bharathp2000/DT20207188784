#include<pic.h>
__CONFIG(0X3F72);


static bit rs@((unsigned )&PORTD*8+1);
static bit rw@((unsigned )&PORTD*8+2);
static bit en@((unsigned )&PORTD*8+3);


unsigned char  VAL8=0,CNT,val1=0,val2,val3,val4,val5,val6=0,VV1=0;

unsigned int HB,VAL1=0,VAL2=0,VAL3=0;
unsigned int val11=0,val12,val13, val14,val15;
unsigned int val21=0,val22, val23,  val24,val25;  
unsigned int VALAA=0,VALBB=0,VAL5=0, VAL7=0;

unsigned char i,j,k,m; 
unsigned int res,spo2, COUNT,adc_value,a,b,c,e,f,g,z,h;
unsigned int  adc_value2,l,n,o;
unsigned char s1,H1,H2,H3,T1,T2,T3;


unsigned char VIK=0,ser=0x37,st=0x01,data_cap=0x00; 
unsigned char VAL4=1,adc=0;
bank1 unsigned char gpsdata[50];


void delay(unsigned int y)//delay prg
{
while(y--);
}




//**************************************************************************
//  LCD
//**************************************************************************
void lcd_command(unsigned char com)
{
unsigned char temp;

PORTD=com&0xf0;
rs=0;
rw=0;
en=1;
delay(10);
en=0;
temp=com<<4;
PORTD=temp&0xf0;
rs=0;
rw=0;
en=1;
delay(10);
en=0;
}


void lcd_init()
{
lcd_command(0x02);
lcd_command(0x2c);
lcd_command(0x06);
lcd_command(0x0c);
lcd_command(0x01);
lcd_command(0x80);
}

void lcd_data(unsigned char data)
{
unsigned char val1;
PORTD=data&0xf0;
en=1;
rs=1;
rw=0;
delay(10);
en=0;
val1=data<<4;
PORTD=val1&0xf0;
en=1;
rs=1;
rw=0;
delay(10);
en=0;
}
void lcd_display(const unsigned char*word,unsigned int n)
{
unsigned char ll;
for(ll=0;ll<n;ll++)
{
lcd_data(word[ll]);
}
}




void adcconvert1()
{
b=VAL1%10;
c=VAL1/10;

e=c%10;
f=c/10;
g=f%10;
h=f/10;


lcd_data(g+0x30);
H1=(g+0x30);
delay(100);

lcd_data(e+0x30);
delay(100);
H2=(e+0x30);

lcd_data(b+0x30);
delay(100);
H3=(b+0x30);

}

void adcconvert2()
{
j=VAL2%10;
k=VAL2/10;

l=k%10;
m=k/10;
n=m%10;
o=m/10;



lcd_data(n+0x30);
delay(100);
T1=(n+0x30);

lcd_data(l+0x30);
delay(100);
T2=(l+0x30);

lcd_data(j+0x30);
delay(100);
T3=(j+0x30);

}

//**************************************************************************
//  SERIAL INTRRUPT
//**************************************************************************
void interrupt rcx(void)
{

if(RCIF==1)
{
//relay=1;
RCIF=0;
ser=RCREG;

if((ser==0x52)&&(st==0x01))
{
data_cap=0x01;
st=0x00;
}
if((data_cap==0x01)&&(i<45))
{
gpsdata[i]=ser;
i=i+1;
}
}
}

 void delay2()
	{
	long i;
	for(i=0;i<10000;i++);
    CLRWDT();
	}


//**************************************************************************
//   GPS AND GSM  INIT 
//**************************************************************************
void iot_init()
{
TXSTA=0X24;
RCSTA=0X90;
SPBRG=25;
BRGH=1;

}
void gps_init()
{
TXSTA=0X24;
RCSTA=0X90;
SPBRG=25;
BRGH=1;
GIE=1;
PEIE=1;
RCIE=1;
}
void gsm_command(const unsigned char *da,unsigned int y)
{
unsigned int s;
for(s=0;s<=y;s++)
{
while(!TXIF)
	{

	}
//delay2();
OERR=0;


TXREG=da[s];
//delay(2000);


}
}
void txs(unsigned char val)
{

while(!TXIF)
	{

	}
//delay2();
OERR=0;
TXREG=val;
}





//**************************************************************************
//IOT
//********************************

void IOT_SEND()
{

TXREG='T';
	delay(100);

TXREG=(H1);
delay(600);

TXREG=(H2);
delay(600);

TXREG=(H3);
delay(600);


TXREG='S';
	delay(100);

TXREG=(T1);
delay(600);

TXREG=(T2);
delay(600);

TXREG=(T3);
delay(600);


TXREG='H';
delay(100);


TXREG=(val4+0x30);
delay(600);
TXREG=(val2+0x30);
delay(600);

TXREG='R';
	delay(100);


TXREG=(val14+0x30);
delay(600);
TXREG=(val12+0x30);
delay(600);

TXREG='S';
	delay(100);


TXREG=(val24+0x30);
delay(600);
TXREG=(val22+0x30);
delay(600);




delay(600);delay(600);
txs('L');
delay(100);
for(j=16;j<27;j++)
{
ser=(gpsdata[j]);
TXREG=(ser);
delay(600);
}
delay(600);delay(600);

txs('C');
delay(100);
for(j=30;j<41;j++)
{
ser=(gpsdata[j]);
TXREG=(ser);	
delay(600);
}

}
//**********************************************************
//HB SENSOR
//**********************************************************

void heart_beat1()
{
TMR1ON=1;	
TMR1CS=0;     // TIMER MODE
T1CKPS1=1;    // SETTING  PRESCALAR VALUE  AS 8
T1CKPS0=1;

TMR1H=0xCF;   
TMR1L=0x2B;

while(CNT<=25)
{
if((RB3==1)&&(VAL8==0))
{
VAL8=1;
COUNT=COUNT+1; 
delay(10000);
}
if(RB3==0)
{
VAL8=0;
}


if(TMR1IF==1)
{
TMR1IF=0;
CNT=CNT+1;
TMR1ON=0;
TMR1ON=1;	

TMR1CS=0;     // TIMER MODE
T1CKPS1=1;    // SETTING  PRESCALAR VALUE  AS 8
T1CKPS0=1;
TMR1H=0xCF;   
TMR1L=0x2B;
}
}

CNT=0;
  if(COUNT>0)
{
  COUNT=COUNT*2+8;
COUNT=COUNT*4;
HB=COUNT;

}
  val2= COUNT%10;//UNIT DIGIT
  val3= COUNT/10;
  val4=val3%10;    // tens digit
  val5=val3/10;
  COUNT=0;  
  // hundred digit 
 }

//**************************************************************************
//**********************************************************
//SPO2 SENSOR
//**********************************************************


void SPO2_beat1()
{
TMR1ON=1;	
TMR1CS=0;     // TIMER MODE
T1CKPS1=1;    // SETTING  PRESCALAR VALUE  AS 8
T1CKPS0=1;

TMR1H=0xCF;   
TMR1L=0x2B;

while(CNT<=25)
{
if((RB4==1)&&(VAL8==0))
{
VAL8=1;
COUNT=COUNT+1; 
delay(10000);
}
if(RB4==0)
{
VAL8=0;
}


if(TMR1IF==1)
{
TMR1IF=0;
CNT=CNT+1;
TMR1ON=0;
TMR1ON=1;	

TMR1CS=0;     // TIMER MODE
T1CKPS1=1;    // SETTING  PRESCALAR VALUE  AS 8
T1CKPS0=1;
TMR1H=0xCF;   
TMR1L=0x2B;
}
}

CNT=0;
  if(COUNT>0)
{
  COUNT=COUNT*2+8;
COUNT=COUNT*4;
spo2=COUNT;

}
  val12= COUNT%10;//UNIT DIGIT
  val13= COUNT/10;
  val14=val13%10;    // tens digit
 val15=val13/10;
  COUNT=0;  
  // hundred digit 
 }

//**********************************************************
//**********************************************************
//RES  SENSOR
//**********************************************************
void RES_beat1()
{
TMR1ON=1;	
TMR1CS=0;     // TIMER MODE
T1CKPS1=1;    // SETTING  PRESCALAR VALUE  AS 8
T1CKPS0=1;

TMR1H=0xCF;   
TMR1L=0x2B;

while(CNT<=25)
{
if((RB5==1)&&(VAL8==0))
{
VAL8=1;
COUNT=COUNT+1; 
delay(10000);
}
if(RB5==0)
{
VAL8=0;
}


if(TMR1IF==1)
{
TMR1IF=0;
CNT=CNT+1;
TMR1ON=0;
TMR1ON=1;	

TMR1CS=0;     // TIMER MODE
T1CKPS1=1;    // SETTING  PRESCALAR VALUE  AS 8
T1CKPS0=1;
TMR1H=0xCF;   
TMR1L=0x2B;
}
}

CNT=0;
  if(COUNT>0)
{
  COUNT=COUNT*2+8;
COUNT=COUNT*4;
res=COUNT;

}
  val22= COUNT%10;//UNIT DIGIT
  val23= COUNT/10;
  val24=val13%10;    // tens digit
  val25=val13/10;
  COUNT=0;  
  // hundred digit 
 }
//**************************************************************
//GPS
//***********************************************************
void gps_send()
{

lcd_command(0xc7);
delay(100);
lcd_display("gps",4);

delay(50000);delay(50000);

if(i>43)
{	
lcd_command(0x80);
delay(1000);
		
for(j=16;j<28;j++)
{

ser=(gpsdata[j]);
lcd_data(ser);

delay(600);
}
lcd_command(0x0c0);

for(j=30;j<41;j++)
{
     
ser=(gpsdata[j]);
lcd_data(ser);	

delay(600);
}

delay(50000);delay(50000);


data_cap=0x00;
st=0x01;
i=0x00;

lcd_command(0x01);
delay(100);
lcd_command(0x80);
delay(100);
lcd_display("welcome",7);

}
}

//**************************************************************************
//SENSOR
//*********************************************************
void sensor()
{
lcd_command(0x01);
delay(100);
lcd_command(0x80);

delay(100);
CHS0=0;
CHS1=0;
CHS2=0;

ADON=1;
delay(200);
ADCON0=ADCON0|0X04;
delay(200);
adc_value=ADRESH;
adc_value=adc_value<<8;
adc_value=(adc_value+ADRESL)/0x02;
VAL1=adc_value;
//adc_value=adc_value/0x02;
delay(100);
lcd_command(0x82);
delay(100);
adcconvert1();
delay(10000);

CHS0=1;
CHS1=0;
CHS2=0;
ADON=1;
delay(200);
ADCON0=ADCON0|0X04;
delay(200);
adc_value=ADRESH;
adc_value=adc_value<<8;
adc_value=(adc_value+ADRESL)/0x02;
VAL2=adc_value;
delay(100);
lcd_command(0x87);
delay(100);
adcconvert2();
delay(10000);


heart_beat1();

lcd_command(0xC7);
delay(100);
lcd_data(val4+0x30);
delay(100);
lcd_data(val2+0x30);


RES_beat1();

lcd_command(0xCA);
delay(100);
lcd_data(val14+0x30);
delay(100);
lcd_data(val12+0x30);



SPO2_beat1();


lcd_command(0xCD);
delay(100);
lcd_data(val24+0x30);
delay(100);
lcd_data(val22+0x30);
delay(50000);delay(50000);
lcd_command(0xc7);
delay(100);
lcd_display("ttttttt",7);
delay(50000);

delay(50000);
gps_send();
delay(50000);

IOT_SEND();

}


//**************************************************************************
//  main
//**************************************************************************
void main()
{

ADCON1=0X80;
ADCON0=0x00;

TRISB=0XFF;
TRISD=0X00;
TRISC=0X80;
TRISA=0X0f;
TRISE=0X00;

PORTA=0X00;
PORTD=0X00;
PORTB=0XFF;
PORTC=0X80;
PORTE=0X00;

delay(35000);delay(35000);
delay(1000);
lcd_init();
delay(1000);
lcd_command(0x01);
delay(50000);	
lcd_command(0x80);
delay(100);
lcd_display("T:   X:        ",15);
lcd_command(0xC0);
delay(100);
lcd_display("H:       :     ",15);
delay(10000);
delay(35000);
gps_init();
delay(35000);
VAL5=0;VAL1=0;


//**************************************************************************
//  while
//**************************************************************************
while(1)
{


sensor();
delay(50000);

}
}






