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
    
        while(1) {
        // Read PIR sensor data into x30
	asm volatile(
            "andi %0, x30, 1"
            : "=r"(input_pirState)
            :
            :
        );

       
        

        //PIR sensor gives 1 if it detects motion
        if (input_pirState == 1) {
            dummy=0xFFFFFFFD;
            asm volatile(
            "and x30, x30, %0\n\t"     // Load immediate 1 into x30
            "or x30, x30,2"                 // output at 2nd bit , that switches on the motor
            :
            :"r"(dummy)
            :"x30"         //Motor turns ON
        );
        
        for (i = 0; i < 10000; i++) {
            for (j = 0; j < 1000000; j++) {
            }
        }
      }  
        else {
            //sensor not detecting motion
            dummy=0xFFFFFFFD;
            asm volatile(
            "and x30, x30, %0\n\t"     // Load immediate 1 into x30
            "or x30, x30,0"            //// output at 2nd bit , that switches off the motor
            :
            :"r"(dummy)
            :"x30"
        );
        }

    return 0;
}

}           

```


**Converting C Code to Assembly Language:**

I compiled the C code and converted into assembly language using the commands:

```bash
riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o ./out hand-dryer1.c 
riscv64-unknown-elf-objdump -d -r out > asm.txt
```

Assembly language instructions for the above code:

```bash

out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fe010113          	addi	sp,sp,-32
   10058:	00812e23          	sw	s0,28(sp)
   1005c:	02010413          	addi	s0,sp,32
   10060:	001f7793          	andi	a5,t5,1
   10064:	fef42223          	sw	a5,-28(s0)
   10068:	fe442703          	lw	a4,-28(s0)
   1006c:	00100793          	li	a5,1
   10070:	06f71263          	bne	a4,a5,100d4 <main+0x80>
   10074:	ffd00793          	li	a5,-3
   10078:	fef42023          	sw	a5,-32(s0)
   1007c:	fe042783          	lw	a5,-32(s0)
   10080:	00ff7f33          	and	t5,t5,a5
   10084:	002f6f13          	ori	t5,t5,2
   10088:	fe042623          	sw	zero,-20(s0)
   1008c:	0340006f          	j	100c0 <main+0x6c>
   10090:	fe042423          	sw	zero,-24(s0)
   10094:	0100006f          	j	100a4 <main+0x50>
   10098:	fe842783          	lw	a5,-24(s0)
   1009c:	00178793          	addi	a5,a5,1
   100a0:	fef42423          	sw	a5,-24(s0)
   100a4:	fe842703          	lw	a4,-24(s0)
   100a8:	000f47b7          	lui	a5,0xf4
   100ac:	23f78793          	addi	a5,a5,575 # f423f <__global_pointer$+0xe2943>
   100b0:	fee7d4e3          	bge	a5,a4,10098 <main+0x44>
   100b4:	fec42783          	lw	a5,-20(s0)
   100b8:	00178793          	addi	a5,a5,1
   100bc:	fef42623          	sw	a5,-20(s0)
   100c0:	fec42703          	lw	a4,-20(s0)
   100c4:	000027b7          	lui	a5,0x2
   100c8:	70f78793          	addi	a5,a5,1807 # 270f <main-0xd945>
   100cc:	fce7d2e3          	bge	a5,a4,10090 <main+0x3c>
   100d0:	0180006f          	j	100e8 <main+0x94>
   100d4:	ffd00793          	li	a5,-3
   100d8:	fef42023          	sw	a5,-32(s0)
   100dc:	fe042783          	lw	a5,-32(s0)
   100e0:	00ff7f33          	and	t5,t5,a5
   100e4:	000f6f13          	ori	t5,t5,0
   100e8:	00000793          	li	a5,0
   100ec:	00078513          	mv	a0,a5
   100f0:	01c12403          	lw	s0,28(sp)
   100f4:	02010113          	addi	sp,sp,32
   100f8:	00008067          	ret

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
Number of different instructions: 10
List of unique instructions:
j
and
or
sw
bne
lw
mv
add
li
ret
```
