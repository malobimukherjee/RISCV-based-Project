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
riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o Project_1.o Project_1.c 
riscv64-unknown-elf-objdump -d -r Project_1.o > asm.txt
```

Assembly language instructions for the above code:

```bash

Project_1.o:     file format elf64-littleriscv


Disassembly of section .text:

0000000000010078 <main>:
   10078:	fd010113          	addi	sp,sp,-48
   1007c:	02813423          	sd	s0,40(sp)
   10080:	03010413          	addi	s0,sp,48
   10084:	fe042623          	sw	zero,-20(s0)
   10088:	0f40006f          	j	1017c <main+0x104>
   1008c:	fec42783          	lw	a5,-20(s0)
   10090:	0007871b          	sext.w	a4,a5
   10094:	00400793          	li	a5,4
   10098:	00e7c863          	blt	a5,a4,100a8 <main+0x30>
   1009c:	00100793          	li	a5,1
   100a0:	fef42423          	sw	a5,-24(s0)
   100a4:	0080006f          	j	100ac <main+0x34>
   100a8:	fe042423          	sw	zero,-24(s0)
   100ac:	fe842783          	lw	a5,-24(s0)
   100b0:	00ff6f33          	or	t5,t5,a5
   100b4:	001f7793          	andi	a5,t5,1
   100b8:	fef42223          	sw	a5,-28(s0)
   100bc:	fe442783          	lw	a5,-28(s0)
   100c0:	0007879b          	sext.w	a5,a5
   100c4:	08078263          	beqz	a5,10148 <main+0xd0>
   100c8:	100007b7          	lui	a5,0x10000
   100cc:	ffd78793          	addi	a5,a5,-3 # ffffffd <__global_pointer$+0xffee65d>
   100d0:	fef42023          	sw	a5,-32(s0)
   100d4:	fe042783          	lw	a5,-32(s0)
   100d8:	00ff7f33          	and	t5,t5,a5
   100dc:	002f6f13          	ori	t5,t5,2
   100e0:	fcf42e23          	sw	a5,-36(s0)
   100e4:	fe042623          	sw	zero,-20(s0)
   100e8:	0380006f          	j	10120 <main+0xa8>
   100ec:	fe042423          	sw	zero,-24(s0)
   100f0:	0100006f          	j	10100 <main+0x88>
   100f4:	fe842783          	lw	a5,-24(s0)
   100f8:	0017879b          	addiw	a5,a5,1
   100fc:	fef42423          	sw	a5,-24(s0)
   10100:	fe842783          	lw	a5,-24(s0)
   10104:	0007871b          	sext.w	a4,a5
   10108:	000f47b7          	lui	a5,0xf4
   1010c:	23f78793          	addi	a5,a5,575 # f423f <__global_pointer$+0xe289f>
   10110:	fee7d2e3          	bge	a5,a4,100f4 <main+0x7c>
   10114:	fec42783          	lw	a5,-20(s0)
   10118:	0017879b          	addiw	a5,a5,1
   1011c:	fef42623          	sw	a5,-20(s0)
   10120:	fec42783          	lw	a5,-20(s0)
   10124:	0007871b          	sext.w	a4,a5
   10128:	000027b7          	lui	a5,0x2
   1012c:	70f78793          	addi	a5,a5,1807 # 270f <main-0xd969>
   10130:	fae7dee3          	bge	a5,a4,100ec <main+0x74>
   10134:	fe042783          	lw	a5,-32(s0)
   10138:	00ff7f33          	and	t5,t5,a5
   1013c:	000f6f13          	ori	t5,t5,0
   10140:	fcf42e23          	sw	a5,-36(s0)
   10144:	0200006f          	j	10164 <main+0xec>
   10148:	100007b7          	lui	a5,0x10000
   1014c:	ffd78793          	addi	a5,a5,-3 # ffffffd <__global_pointer$+0xffee65d>
   10150:	fef42023          	sw	a5,-32(s0)
   10154:	fe042783          	lw	a5,-32(s0)
   10158:	00ff7f33          	and	t5,t5,a5
   1015c:	000f6f13          	ori	t5,t5,0
   10160:	fcf42e23          	sw	a5,-36(s0)
   10164:	00100f1b          	addiw	t5,zero,1
   10168:	020f1f13          	slli	t5,t5,0x20
   1016c:	ffff0f13          	addi	t5,t5,-1
   10170:	fec42783          	lw	a5,-20(s0)
   10174:	0017879b          	addiw	a5,a5,1
   10178:	fef42623          	sw	a5,-20(s0)
   1017c:	fec42783          	lw	a5,-20(s0)
   10180:	0007871b          	sext.w	a4,a5
   10184:	00900793          	li	a5,9
   10188:	f0e7d2e3          	bge	a5,a4,1008c <main+0x14>
   1018c:	00000793          	li	a5,0
   10190:	00078513          	mv	a0,a5
   10194:	02813403          	ld	s0,40(sp)
   10198:	03010113          	addi	sp,sp,48
   1019c:	00008067          	ret
 

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
Number of different instructions: 20
List of unique instructions:
bge
slli
blt
j
li
ld
beqz
ori
mv
or
sext.w
and
andi
ret
addiw
lw
sd
lui
sw
addi
```
