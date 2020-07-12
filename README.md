# REGISTER_BUTTON_CONTROL
This project is button control and used Registers with Stm32f407-Discovery Kit.
//CODES

#include "stm32f4xx.h"
#include "stm32f4_discovery.h"

int count =0;

void delay(uint32_t time)
{
	while(time--);
}

void CLK_Config()
{
	//Clock ayarlarını Atollic programı içinde yapılmıştır
	RCC->CR |= 0x00030000; // HSEON and HSEONRDY enable
	RCC->CR |= 0x00080000; //CSS enable
	RCC->PLLCFGR |= 0x00040000; // PLL e HSE sectik
	RCC->PLLCFGR |= 0x00000004; // PLL M=4
	RCC->PLLCFGR |= 0x00005A00; // PLL N = 168
	RCC->PLLCFGR |= 0x00000000; // PLL P =2
	RCC->CFGR |= 0x00000000;    //AHB Prescaler =1
	RCC->CFGR |= 0x00080000;    //AHB Prescaler =2
	RCC->CFGR |= 0x00001400;    //AHB Prescaler =4
	RCC->CIR |= 0x00080000;     //HSERDY Flag clear
	RCC->CIR |= 0x00800000;     //CSS Flag clear
}

void GPIO_Config()
{
	RCC->AHB1ENR = 0x00000009;  //GPIOA ve GPIOD aktif

	GPIOD->MODER = 0x55000000;  //12, 13, 14, 15 pin digital OUTPUT //Bu program için A portu configurasyonlarını yazmaya gerek yok
	GPIOD->OTYPER =0x00000000;  //12, 13, 14, 15 pin push_pull
	GPIOD->OSPEEDR = 0xFF000000; //12, 13, 14, 15 pin 100MHz
	GPIOD->PUPDR = 0x00000000;  //12, 13, 14, 15 pin no pull
}

int main(void)
{
	CLK_Config();
    GPIO_Config();

  while (1)
  {
	  if(GPIOA->IDR & 0x00000001) //A portunun 0.portunda sinyal var mı?
	  {
		  while(GPIOA->IDR &0x00000001); //Butona basılıyorsa bekle
		  delay(168000); //Arkların gitmesi icin bekleniyor
		  count++;
	  }

	  if(count %2==0)
	  {
		  GPIOD->ODR= 0x00000000;
	  }
	  else
	  {
		  GPIOD->ODR=0x0000F000;
	  }

  }
}
