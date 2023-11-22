# RISCV-based-Project

## Implementing a Hand-dryer:



**Objective:**

    • To be able to detect the motion using a PIR sensor.
    • To turn on the FAN of the motor when the motion is detected in PIR Sensor.

**Requirements:**

    • PIR Sensor
    • MCU board
    • DC motor
    • Motor Driver
    • 9V Battery
    • Jumper Wires
    • USB cable


**Block Diagram of the Hand Dryer:**



![BlockDiagram](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/36ae2008-df72-4f5a-bcc2-68adc00aced0)

**Flowchart of the Implementation:**


![Flowchart](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/2f3ee60b-aae7-4782-ba6a-a9d8d4b5728f)

**C Code of the above Implementation:**



```bash

int main()  {
       
       int input_pirState;
       
       int output_motorDir1=0;                        //Using clockwise direction only                       
       int dummy;
       dummy = 0xFFFFFFFD;
       
     
	      asm volatile(
	          "and x30, x30, %1\n\t"
	          "or x30, x30, %0\n\t"
	          :
	          : "r"(output_motorDir1),"r"(dummy)  
	          : "x30"                      
	      );
       
        while(1) {
        // Read PIR sensor data into x30
	  asm volatile(
              "andi %0, x30, 1\n\t"
              : "=r"(input_pirState)
              : 
              : 
            );
       //printf("reading sensor values --- PIR State %d\n",input_pirState);
        

        //PIR sensor gives 1 if it detects motion
        if (input_pirState == 1) {
            
            output_motorDir1=2;
            dummy=0xFFFFFFFD;
            asm volatile(
	    "and x30, x30, %1\n\t"
            "or x30, x30, %0\n\t" 
            : 
            : "r"(output_motorDir1),"r" (dummy)
            : "x30"
          );
            
         //printf("PIR Sensor ON, FAN is ON %d\n",output_motorDir1);
         /*
		 for(i=0;i<1000;i++)
		 {
		 
		 }
		 
            motor_out = 0;
            dummy=0xFFFFFFFD;
	    asm volatile(
	    "and x30, x30, %0\n\t"
	    "ori x30, x30,0"
	    : 
            : "r" (dummy)
            : "x30"
            );
          
            asm volatile(
	    "addi %0,x30, 0\n\t"
            : "=r"(output_motorDir1)
            : 
            : "x30"
          );
          */
        //printf("Motor turns OFF after drying the hand %d\n",motor_out);
      }  
        else {
            //sensor not detecting motion
            output_motorDir1=0;
            dummy=0xFFFFFFFD;
            asm volatile(
	    "and x30, x30, %1\n\t"
            "or x30, x30, %0\n\t" 
            : 
            : "r"(output_motorDir1),"r" (dummy)
            : "x30"
          );
            //printf("PIR Sensor OFF, FAN is OFF %d\n",output_motorDir1);
        }

   
}

 return 0;
 
}           

       

```

**Testing the C Code, changing the input value:**

```bash
#include<stdio.h>
int main()  {

       int input_pirState;
       int output_motorDir1;                        //Using clockwise direction only
       int motor_out;                      
       int dummy=0xFFFFFFFD;
       int i, j;
       
       for( i = 0; i<10; i++)
       //while(1)
       {
        // Read PIR sensor data into x30
       
              if(i<5)
                        j=1;
                        else
                        j=0;
                 
             
                  asm volatile(
                  "or x30, x30, %1\n\t"
                  "andi %0, x30, 0x01\n\t"
                  : "=r" (input_pirState)
                  : "r" (j)
                  : "x30"
                  );
      
       
         
            printf("reading sensor values --- PIR State %d\n",input_pirState);
            
            

            //PIR sensor gives 1 if it detects motion
            if (j == 1)
            {
            
                  motor_out = 1;
                      dummy=0xFFFFFFFD;
                      asm volatile(
                      "and x30, x30, %0\n\t"    
                      "ori x30, x30,2"                 // output at 2nd bit (x30[1]), that switches on the motor
                      :
                      :"r"(dummy)
                      :"x30"         //Motor turns ON
                      );
                      asm volatile(
                        "addi %0,x30, 0\n\t"
                        :"=r"(output_motorDir1)
                        :
                        :"x30"
                        );
                   printf("PIR Sensor ON, FAN is ON %d\n",motor_out);
                  //for (i = 0; i < 1000; i++) {                //Delay
                      
                  //}
                  motor_out = 0;
                  dummy=0xFFFFFFFD;
                        asm volatile(
                            "and x30, x30, %0\n\t"    
                            "ori x30, x30,0"                 // output at 2nd bit (x30[1]), that switches OFF the motor
                            :
                            :"r"(dummy)
                            :"x30"         //Motor turns OFF after few seconds
                        );
                        asm volatile(
                              "addi %0,x30, 0\n\t"
                              :"=r"(output_motorDir1)
                              :
                              :"x30"
                              );
                  
                  printf("Motor turns OFF after drying the hand %d\n",motor_out);
                  }  
                  else {
            
                        motor_out=0;
                        //output_motorDir1=0;
                      //sensor not detecting motion
                      dummy=0xFFFFFFFD;
                      asm volatile(
                      "and x30, x30, %0\n\t"    
                      "ori x30, x30,0"            // output at 2nd bit (x30[1]) , that switches off the motor
                      :
                      :"r"(dummy)
                      :"x30"
                      );
                  
                  asm volatile(
                      "addi %0,x30, 0\n\t"            
                      :"=r"(output_motorDir1)
                      :
                      :"x30"
                    );
                  
                        printf("PIR Sensor OFF, FAN is OFF %d\n",motor_out);
                  }
                
      }
  return 0;
}
                
```
Commands used to do spike simulation:

```bash
riscv64-unknown-elf-gcc -march=rv64i -mabi=lp64 -ffreestanding -o output1 Project_1.c
spike pk output1
```
**Spike Output:**

![image](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/15b3215d-3c12-4eec-83b2-dcf5c3a363c4)



**Converting C Code to Assembly Language:**

I compiled the C code and converted into assembly language using the commands:

```bash
riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o Project_5 Project_1.c 
riscv64-unknown-elf-objdump -d -r Project_5 > asm.txt
```
**Function Simulation:**

![Screenshot from 2023-10-31 12-42-01](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/e355ad33-6ebd-4075-8c3f-e510d369f578)

According to my code, when the input is HIGH, the output should be HIGH. The above waveform matches the expected output. The toggling observed in the output line after some time is because it is executing the last instruction, '00FF6F33', and again, a new cycle is starting. While the input wire remains HIGH as written in the testbench after 4000 seconds without toggling.

![Screenshot from 2023-10-31 12-41-30](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/5a7a47c8-d135-47f3-a6e6-65bb2f6b6012)

Magnified Waveform:

![Screenshot from 2023-10-31 12-44-18](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/af0ede81-6629-4cf3-bfec-f9ae99f94b3b)



Assembly language instructions for the above code:

```bash

Project_5:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fe010113          	addi	sp,sp,-32
   10058:	00812e23          	sw	s0,28(sp)
   1005c:	02010413          	addi	s0,sp,32
   10060:	fe042623          	sw	zero,-20(s0)
   10064:	ffd00793          	li	a5,-3
   10068:	fef42423          	sw	a5,-24(s0)
   1006c:	fec42783          	lw	a5,-20(s0)
   10070:	fe842703          	lw	a4,-24(s0)
   10074:	00ef7f33          	and	t5,t5,a4
   10078:	00ff6f33          	or	t5,t5,a5
   1007c:	001f7793          	andi	a5,t5,1
   10080:	fef42223          	sw	a5,-28(s0)
   10084:	fe442703          	lw	a4,-28(s0)
   10088:	00100793          	li	a5,1
   1008c:	02f71463          	bne	a4,a5,100b4 <main+0x60>
   10090:	00200793          	li	a5,2
   10094:	fef42623          	sw	a5,-20(s0)
   10098:	ffd00793          	li	a5,-3
   1009c:	fef42423          	sw	a5,-24(s0)
   100a0:	fec42783          	lw	a5,-20(s0)
   100a4:	fe842703          	lw	a4,-24(s0)
   100a8:	00ef7f33          	and	t5,t5,a4
   100ac:	00ff6f33          	or	t5,t5,a5
   100b0:	fcdff06f          	j	1007c <main+0x28>
   100b4:	fe042623          	sw	zero,-20(s0)
   100b8:	ffd00793          	li	a5,-3
   100bc:	fef42423          	sw	a5,-24(s0)
   100c0:	fec42783          	lw	a5,-20(s0)
   100c4:	fe842703          	lw	a4,-24(s0)
   100c8:	00ef7f33          	and	t5,t5,a4
   100cc:	00ff6f33          	or	t5,t5,a5
   100d0:	fadff06f          	j	1007c <main+0x28>
```
**Extracting Unique Instructions:**

I used this code in python to obtain the unique instructions:

```bash
with open('hand-dryer.txt', 'r') as file:
    assembly_code = file.read()
lines = assembly_code.split('\n')
unique_instructions = set()
for line in lines[5:]:
    line = line.strip()
    if line and not line.startswith('#'):
        parts = line.split()
        if len(parts) >= 3:
            instruction = parts[2]
            unique_instructions.add(instruction)
print(f"Number of different instructions: {len(unique_instructions)}")
print("List of unique instructions:")
for instruction in unique_instructions:
    print(instruction)
```
The output of the code:

```bash
Number of different instructions: 9
List of unique instructions:
andi
or
lw
addi
and
bne
j
li
sw
```
**Gate Level Synthesis**

For Gate Level Simulation, writing_inst_done = 1

```bash
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80_256.lib 
read_verilog processor-gls.v 
synth -top wrapper
dfflibmap -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib 
abc -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib
write_verilog synth_processor_test.v
```

```bash
iverilog -o test synth_processor_test-gls.v testbench.v sky130_sram_1kbyte_1rw1r_32x256_8.v sky130_fd_sc_hd.v primitives.v
./test
gtkwave waveform.vcd

```

![Screenshot from 2023-11-01 01-29-21](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/482b97b1-e47e-487d-9f92-22082f65a033)

**Wrapper Module after Netlist generation**

![Screenshot from 2023-11-05 21-55-09](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/e3f15ee7-8e7b-4d4f-a95b-c1a38a37b80b)

## OpenLane

Below are the commands to be executed:
```bash
make mount
./flow.tcl -interactive
% package require openlane 0.9
% prep -design project
```

![Screenshot from 2023-11-22 19-01-50](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/53c1fdf1-cff0-4443-b497-72f6354ed4e3)

```bash
run_synthesis
```
![Screenshot from 2023-11-22 19-02-07](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/363da4d6-1f80-4872-a30e-72eb443da031)

```bash
run_floorplan
```

![Screenshot from 2023-11-22 19-02-32](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/9820acab-47b0-41f5-9c9e-b99d698ada1b)

```bash
run_placement
```


![Screenshot from 2023-11-22 19-02-48](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/7e2ebf4c-05e1-4241-ac2b-a6bcad6b55d5)

```bash
run_cts
run_routing
```

![Screenshot from 2023-11-22 19-03-03](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/b43f990f-dedd-44f0-b042-f90077b24ffb)



![Screenshot from 2023-11-22 19-03-22](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/c43c4056-f7dd-47f4-80bf-bc9a341a93d4)

Slack observed:

![WhatsApp Image 2023-11-22 at 18 41 05](https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/bce565b7-0b1b-4c50-9496-e4e48fbc0f82)

## Acknowledgement

I would like to express my gratitude to 
```bash
	• Prof. Kunal Ghosh
        • Dantu Nandini Devi, Senior, MS Student
	• Kopparthi Dinesh Lingaiah
	• Mayank Kabra, TA
```
for helping me to complete this project smoothly.
