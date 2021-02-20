
#include<pic.h>
__CONFIG(0X3F72);


static bit rs@((unsigned )&PORTD*8+1);
static bit rw@((unsigned )&PORTD*8+2);
static bit en@((unsigned )&PORTD*8+3);

static bit sw1@((unsigned )&PORTB*8+0);
static bit RELAY1@((unsigned )&PORTC*8+0);
static bit RELAY2@((unsigned )&PORTC*8+1);
unsigned char  VAL8=0,CNT,val1=0,val2,val3,val4,val5,val6=0,VV1=0;

unsigned int HB,VAL1=0,VAL2=0,VAL3=0;
unsigned int val11=0,val12,val13, val14,val15;
unsigned int val21=0,val22, val23,  val24,val25;  
//unsigned int VALAA=0,VALBB=0,VAL5=0, VAL7=0;

unsigned char i,j,k,m; 
unsigned int res,spo2, COUNT,adc_value,a,b,c,e,f,g,z,h;
unsigned int  adc_value2,l,n,o;
unsigned char s1,H1=0,H2=0,H3=0,T1=0,T2=0,T3=0,COUNT1,COUNT2;


unsigned char VIK=0,ser=0x37,st=0x01,data_cap=0x00; 
unsigned char VAL4=1,adc=0;
 unsigned char gpsdata[50];


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

delay(100);

lcd_data(e+0x30);
delay(100);


lcd_data(b+0x30);
delay(100);


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


lcd_data(l+0x30);
delay(100);

lcd_data(j+0x30);
delay(100);


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

void sms1()
	{
   	  txs('A');txs('T');txs('+');txs('C');txs('M');txs('G');
	  txs('S');txs('=');
	  txs('"');	  
	  txs('7');txs('5');txs('3');txs('9');txs('9');
      txs('7');txs('6');txs('2');txs('8');txs('5');	  
	  txs('"');	  
	  txs(13); delay(10000);txs(10);



if(T1==1)
{
//gsm_command("TEMP ",5);
TXREG='T';delay(100);
TXREG='E';delay(100);
TXREG='M';delay(100);
TXREG='P';delay(100);
TXREG=' ';delay(100);
}
if(T2==1)
{
//gsm_command("SUGAR  ",7);
TXREG='S';delay(100);
TXREG='U';delay(100);
TXREG='G';delay(100);
TXREG='A';delay(100);
TXREG='R';delay(100);
TXREG=' ';delay(100);
}
if(T3==1)
{
//gsm_command("HB ",3);
TXREG='H';delay(100);
TXREG='B';delay(100);
TXREG=' ';delay(100);
}
if(H1==1)
{
//gsm_command("O2S  ",5);
TXREG='O';delay(100);
TXREG='2';delay(100);
TXREG='S';delay(100);
TXREG=' ';delay(100);
}
if(H2==1)
{
//gsm_command("RESP ",5);
TXREG='R';delay(100);
TXREG='E';delay(100);
TXREG='S';delay(100);
TXREG='P';delay(100);
TXREG=' ';delay(100);
}
if((T1==0)&&(T2==0)&&(T3==0)&&(H1==0)&&(H2==0))
{
gsm_command("UR HEALTHLY!!",13);
}
delay(600);
delay(600);



	 delay(60000);
	  txs(13);delay(60000);
 txs(10);	 
delay(10000);
	  txs(26);
	}




//**************************************************************************
//IOT
//********************************

void IOT_SEND()
{

TXREG='T';
	delay(100);

TXREG=(g+0x30);
delay(600);

TXREG=(e+0x30);
delay(600);

TXREG=(b+0x30);
delay(600);


TXREG='S';
	delay(100);

TXREG=(n+0x30);
delay(600);

TXREG=(l+0x30);
delay(600);

TXREG=(j+0x30);
delay(600);


TXREG='H';
delay(100);

TXREG=(val5+0x30);
delay(600);
TXREG=(val4+0x30);
delay(600);
TXREG=(val2+0x30);
delay(600);

TXREG='P';
	delay(100);

TXREG=(val15+0x30);
delay(600);
TXREG=(val14+0x30);
delay(600);
TXREG=(val12+0x30);
delay(600);

TXREG='R';
	delay(100);

TXREG=(val25+0x30);
delay(600);
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
delay(600);

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
VAL8=0;
while(CNT<=25)
{
if((RB3==1)&&(VAL8==0))
{
VAL8=1;
COUNT=COUNT+1; 
//delay(10000);
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
if(COUNT<42)
{
COUNT=0;
}

if(COUNT>99)
{
T3=1;
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
VAL8=0;
while(CNT<=25)
{
if((RB4==1)&&(VAL8==0))
{
VAL8=1;
COUNT1=COUNT1+1; 
//delay(10000);
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
  if(COUNT1>0)
{
  COUNT1=COUNT1*2+12;
COUNT1=COUNT1*5;
if(COUNT1<102)
{
COUNT1=COUNT1-2;
}
if(COUNT1>102)
{
COUNT1=COUNT1-12;
}
spo2=COUNT1;

}

if((spo2>96)&&(spo2<99))
{
}
else
{
if(spo2>0)
{
H1=1;
}
}
  val12= COUNT1%10;//UNIT DIGIT
  val13= COUNT1/10;
  val14=val13%10;    // tens digit
 val15=val13/10;
  COUNT1=0;  
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
VAL8=0;
while(CNT<=25)
{
if((RB5==1)&&(VAL8==0))
{
VAL8=1;
COUNT2=COUNT2+1; 
//delay(10000);
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
  if(COUNT2>0)
{
  COUNT2=COUNT2/10;
COUNT=COUNT*4;
res=COUNT2;

}

if(res>20)
{
H2=1;
}

  val22= COUNT2%10;//UNIT DIGIT
  val23= COUNT2/10;
  val24=val23%10;    // tens digit
  val25=val23/10;
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


gps_send();
delay(50000);

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
adc_value=(adc_value*9);
adc_value=(adc_value/5);
adc_value=(adc_value+32);
VAL1=adc_value;

if(VAL1>98)
{
T1=1;
}

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

VAL2=adc_value/0X02;
 
if((VAL2>140)&&(VAL2<190))
{
T2=1;
}
delay(100);
lcd_command(0x87);
delay(100);
adcconvert2();
delay(10000);




heart_beat1();

lcd_command(0xC0);
delay(100);
lcd_data(val5+0x30);
delay(100);
lcd_data(val4+0x30);
delay(100);
lcd_data(val2+0x30);


RES_beat1();

lcd_command(0xC7);
delay(100);


lcd_data(val25+0x30);
delay(100);

lcd_data(val24+0x30);
delay(100);

lcd_data(val22+0x30);



SPO2_beat1();
lcd_command(0xCC);
delay(100);
lcd_data(val15+0x30);
delay(100);
lcd_data(val14+0x30);
delay(100);
lcd_data(val12+0x30);
delay(100);
lcd_data('%');

if((VAL1>100)&&(HB>100))
{
RELAY2=1;delay(50000);delay(50000);
RELAY2=0;
}

RELAY1=0;delay(50000);delay(30000);

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
VAL1=0;


//**************************************************************************
//  while
//**************************************************************************
while(1)
{
sensor();



if(sw1==0)
{
RELAY1=1;delay(50000);delay(30000);
sms1();delay(30000);
}



}
}





