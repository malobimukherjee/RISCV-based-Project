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
       //int output_fanspeed;
       int output_motorDir1;                        //Using clockwise direction only                       
       int dummy;
       int i, j;
       int motor_out;
        while(1) {
        // Read PIR sensor data into x30
	asm volatile(
	    "or x30, x30, %1\n\t"
            "andi %0, x30, 1"
            : "=r"(input_pirState)
            : "r" (j)
            : "x30"
          );
       //printf("reading sensor values --- PIR State %d\n",input_pirState);
        

        //PIR sensor gives 1 if it detects motion
        if (j == 1) {
            motor_out = 1;
            dummy=0xFFFFFFFD;
            asm volatile(
	    "and x30, x30, %0\n\t"
            "ori x30, x30,2" 
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
         //printf("PIR Sensor ON, FAN is ON %d\n",motor_out);
         
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
        //printf("Motor turns OFF after drying the hand %d\n",motor_out);
      }  
        else {
            //sensor not detecting motion
            dummy=0xFFFFFFFD;
            motor_out=0;
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
            //printf("PIR Sensor OFF, FAN is OFF %d\n",motor_out);
        }

    return 0;
}

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
riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o Project_1 Project_1.c 
riscv64-unknown-elf-objdump -d -r Project_1 > asm.txt
```

Assembly language instructions for the above code:

```bash

Project_1:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fd010113          	addi	sp,sp,-48
   10058:	02812623          	sw	s0,44(sp)
   1005c:	03010413          	addi	s0,sp,48
   10060:	fec42783          	lw	a5,-20(s0)
   10064:	00ff6f33          	or	t5,t5,a5
   10068:	001f7793          	andi	a5,t5,1
   1006c:	fef42423          	sw	a5,-24(s0)
   10070:	fec42703          	lw	a4,-20(s0)
   10074:	00100793          	li	a5,1
   10078:	04f71663          	bne	a4,a5,100c4 <main+0x70>
   1007c:	00100793          	li	a5,1
   10080:	fef42023          	sw	a5,-32(s0)
   10084:	ffd00793          	li	a5,-3
   10088:	fef42223          	sw	a5,-28(s0)
   1008c:	fe442783          	lw	a5,-28(s0)
   10090:	00ff7f33          	and	t5,t5,a5
   10094:	002f6f13          	ori	t5,t5,2
   10098:	000f0793          	mv	a5,t5
   1009c:	fcf42e23          	sw	a5,-36(s0)
   100a0:	fe042023          	sw	zero,-32(s0)
   100a4:	ffd00793          	li	a5,-3
   100a8:	fef42223          	sw	a5,-28(s0)
   100ac:	fe442783          	lw	a5,-28(s0)
   100b0:	00ff7f33          	and	t5,t5,a5
   100b4:	000f6f13          	ori	t5,t5,0
   100b8:	000f0793          	mv	a5,t5
   100bc:	fcf42e23          	sw	a5,-36(s0)
   100c0:	0240006f          	j	100e4 <main+0x90>
   100c4:	ffd00793          	li	a5,-3
   100c8:	fef42223          	sw	a5,-28(s0)
   100cc:	fe042023          	sw	zero,-32(s0)
   100d0:	fe442783          	lw	a5,-28(s0)
   100d4:	00ff7f33          	and	t5,t5,a5
   100d8:	000f6f13          	ori	t5,t5,0
   100dc:	000f0793          	mv	a5,t5
   100e0:	fcf42e23          	sw	a5,-36(s0)
   100e4:	00000793          	li	a5,0
   100e8:	00078513          	mv	a0,a5
   100ec:	02c12403          	lw	s0,44(sp)
   100f0:	03010113          	addi	sp,sp,48
   100f4:	00008067          	ret
	

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
Number of different instructions: 12
List of unique instructions:
mv
ori
andi
sw
lw
and
j
li
or
addi
bne
ret
```
