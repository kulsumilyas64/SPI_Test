## Code for SPI_Test

```
#include "main.h"
#include<stdio.h>   // Header added for sprintf

/* Constants created representing available instructions that can be send to the peripheral connected */
const uint8_t pphl_READ  =  0b00000011;
const uint8_t pphl_WRITE =  0b00001011;
const uint8_t pphl_WREN  =  0b00001010;
const uint8_t pphl_RDSR  =  0b00001101;
const uint8_t pphl_WRSR  =  0b00001111;

int main(void)
{
char uart_buf[50];  // uart buffer stores the strings to print out to the serial terminal
int uart_buf_len;
char spi_buf[20];   // spi buffer to send and receive spi data

/* CS pin should default high */
  HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_SET);

  uart_buf_len = sprintf(uart_buf, "SPI Test Successful\r\n");
  HAL_UART_Transmit(&huart1, (uint8_t *)uart_buf, uart_buf_len, 100 );

  // Write Operations
  HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_RESET);
  HAL_SPI_Transmit(&hspi1, (uint8_t *)&pphl_WREN, 1, 100);  //cast the data that is to be send on unsigned 8 bits
  HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_SET);

  //Read Status Register
  HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_RESET);
  HAL_SPI_Transmit(&hspi1, (uint8_t *)&pphl_RDSR, 1, 100);
  HAL_SPI_Receive(&hspi1, (uint8_t *)spi_buf, 1, 100);  // pointer to a spi buffer filled with read bytes
  HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_SET);

  // Print out Status Register
  uart_buf_len = sprintf(uart_buf, "Status: 0x%02x\r\n",
		  (unsigned int)spi_buf[0]);
  HAL_UART_Transmit(&huart1,(uint8_t *)uart_buf, uart_buf_len, 100);
  }
