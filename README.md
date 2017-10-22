# robotics_uart_homework
//bluetooth control servo to move by left to right by 'q' and 'e'

#include "main.h"
#include "uart.h"
#include "button.h"

u32 ticks = 0;
u16 isMovingTo = 1;
u8 isToggled = 0;


u16 endPosition = 1050;
u16 startPosition =450;
int tempPosition = 450;
u8 buttonInput = 1;


void UARTListener(const uint8_t byte)
{
	uart_tx(COM3,"\nYou entered : %c\n",byte);
	switch(byte)
	{
		case 'q' :
			isMovingTo=1;
		
		break;
		
		case'e' :
			isMovingTo=2;
		break;
		
		case ' ' :
			isMovingTo=0;
		break;
		}
	}
		

int main(){
  //initialization
  ticks_init();
  ticks_reset();
	uart_init(COM3,115200);
	uart_interrupt_init(COM3,UARTListener);
	servo_init(143,10000,750);
	button_init();
	
  while(1)
	{
    if(ticks != get_ticks())
		{
      ticks = get_ticks();
			
				if(ticks%20==5){
					buttonInput=read_button(BUTTON1);
				}
      if(ticks%20 == 0)
			{
				if(isMovingTo==1)
				{
					if(tempPosition<endPosition)
					{
						tempPosition+=12;
						servo_control(SERVO1,tempPosition);
					}
						
				}
				if(isMovingTo==2)
				{	
					if(tempPosition>startPosition)
					{
						tempPosition-=12;
						servo_control(SERVO1,tempPosition);
						
					}
					
				}
				
				}
				
				if(ticks%20==10)
				{
					if((buttonInput==0) && (isToggled==0))
					{
						float dutyCycle = (double)(tempPosition)/10000*100;
						uart_tx(COM3,"The duty cycle is %f%%",&dutyCycle);
						isToggled=1;
					}
					if(buttonInput==1 && isToggled==1)
					{
						isToggled=0;
					}
				}
					
				
				
				

					
					
      }
    }
  }
