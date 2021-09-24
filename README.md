# M480BSP_ISP_UART_APROM
 M480BSP_ISP_UART_APROM

update @ 2021/09/23

1. under Application code project : AP

	- add binary to hex , by SRecord tool 
	
	- under generateChecksum.bat will execute generateChecksum.cmd , generateCRCbinary.cmd , generateCRChex.cmd
	
		- generateChecksum.cmd : calculate checksum by load the original binary file , and display on KEIL project
		
		- generateCRCbinary.cmd : calculate checksum by load the original binary file , and fill 0xFF , range up to 0x1D000
		
		- generateCRChex.cmd : conver binary file into hex file
		
	- at KEIL output file , file name is APROM_application , under \obj folder , 
	
	which mapping to generateChecksum.cmd , generateCRCbinary.cmd , generateCRChex.cmd
	
	modify the file name in KEIL project , also need to modify the file name in these 3 generate***.cmd

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/APROM_KEIL_output_file.jpg)

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/APROM_SRecord_cmd_file.jpg)


update @ 2021/09/22

1. Scenario notice:

	- Boot loader project : ISP_UART 
	
		- under sct file (uart_iap.sct) , will allocate flash size 
		
				LDROM_Bootloader.bin : 0x100000 ~ 0xFFF (default LDROM size : 4K)
			
				APROM_Bootloader.bin : 0x1E000 0x1000 (reserve 4K size , to store extra boot loader code 

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/LDROM_KEIL_sct.jpg)


	
		- when power on , will check power on source (ex : power on reset , nReset , from application code)
	
		- use CRC to calculate Application code checksum (length : 0x1D000)
		
		- load Application code checksum , from specific address (at 0x1D000 last 4 bytes)
		
		- if two checksum result are different , will stuck in Boot loader , and wait for ISP tool hand shaking
		
		- if two checksum result are the same , will jump to Application code

		- if reset from application code , will entry timeout counting , jump to application code if not receive ISP tool command
	
	- Application code project : AP
	
		- use SRecord , to calculate application code checksum 
		
		- SRecord file : srec_cat.exe , generateChecksum.bat (will execute generateChecksum.cmd , generateCRCbinary.cmd)
		
		- check sum calculate will start from 0 to 0x1D000 , and store in 0x1D000 last 4 bytes 
		
		- after project compile finish , binary size will be 120K (total application code size : 0x1E000)
		
		- under terminal , use keyboard , 'z' , 'Z' , will write specific value in RTC backup register , and return to boot loader
		
		- use ISP tool , to programming Application code project binary (120K) , when under Boot loader flow
		
	- reserve data flash address : 0x1F000
	
2. Flash allocation

	- LDROM_Bootloader.bin : 0x100000 ~ 0xFFF
	
	- APROM_Bootloader.bin : 0x1E000 0x1000
	
	- Application code : 0x00000
	
	- Data flash : 0x1F000
	
	- Chcecksum storage : 0x1DFFC

3. Function assignment

	- debug port : UART1 (PB , PB3) , in Boot loader an Application code project
	
	- ISP UART port : UART0 (PB12 , PB13) , in Boot loader project
	
	- enable RTC and CRC , Timer1 module
	
4. Need to use ICP tool , to programm boot loader project file (LDROM_Bootloader.bin , APROM_Bootloader.bin)

below is boot loader project , Config setting 

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/LDROM_ICP_config.jpg)

below is boot loader project , ICP programming setting 

- LDROM_Bootloader.bin : under LDROM

- APROM_Bootloader.bin : 0x1E000

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/LDROM_ICP_update.jpg)

5. under Application code KEIL project setting 

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/APROM_KEIL_checksum_calculate.jpg)

in Application project , press 'z' , 'Z' will reset to Boot loader 

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/APROM_press_Z.jpg)

below is log message , from boot loader , to application code

![image](https://github.com/released/M480BSP_ISP_UART_APROM/blob/main/boot_from_LDROM_to_APROM.jpg)



