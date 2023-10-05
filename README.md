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
    
    
        while(1) {
        // Read PIR sensor data into x30
	asm (
            "and %0, x30, 1"
            : "=r"(input_pirState)
        );

       
        

        //PIR sensor gives 1 if it detects motion
        if (input_pirState == 1) {
            dummy=0xFFFFFFFD;
            asm(
            "and x30,x30, %0\n\t"     // Load immediate 1 into x30
            "or %1, x30,2"                 // output at 2nd bit , that switches on the motor
            :"=r"(dummy)
            :"r"(output_motorDir1)         //Motor turns ON
        );
        } 
        else {
            //sensor not detecting motion
            dummy=0xFFFFFFFD;
            asm(
            "and x30,x30, %0\n\t"     // Load immediate 1 into x30
            "or %1, x30,0"            //// output at 2nd bit , that switches off the motor
            :"=r"(dummy)
            :"r"(output_motorDir1)
        );
        }

    return 0;
}

}

```


**Converting C Code to Assembly Language:**

I compiled the C code and converted into assembly language using the commands:

```bash
riscv32-unknown-elf-gcc -c -mabi=ilp32 -march=rv32im -ffreestanding -nostdlib -o ./hand-dryer hand-dryer.c
riscv32-unknown-elf-objdump -d hand-dryer > hand-dryer.txt
```

Assembly language instructions for the above code:

```bash
hand-dryer:     file format elf32-littleriscv


Disassembly of section .text:

00000000 <main>:
   0:	fe010113          	add	sp,sp,-32
   4:	00812e23          	sw	s0,28(sp)
   8:	02010413          	add	s0,sp,32
   c:	001f7793          	and	a5,t5,1
  10:	fef42623          	sw	a5,-20(s0)
  14:	fec42703          	lw	a4,-20(s0)
  18:	00100793          	li	a5,1
  1c:	02f71063          	bne	a4,a5,3c <.L2>
  20:	ffd00793          	li	a5,-3
  24:	fef42423          	sw	a5,-24(s0)
  28:	fe442783          	lw	a5,-28(s0)
  2c:	00ff7f33          	and	t5,t5,a5
  30:	002f6793          	or	a5,t5,2
  34:	fef42423          	sw	a5,-24(s0)
  38:	01c0006f          	j	54 <.L3>

0000003c <.L2>:
  3c:	ffd00793          	li	a5,-3
  40:	fef42423          	sw	a5,-24(s0)
  44:	fe442783          	lw	a5,-28(s0)
  48:	00ff7f33          	and	t5,t5,a5
  4c:	000f6793          	or	a5,t5,0
  50:	fef42423          	sw	a5,-24(s0)

00000054 <.L3>:
  54:	00000793          	li	a5,0
  58:	00078513          	mv	a0,a5
  5c:	01c12403          	lw	s0,28(sp)
  60:	02010113          	add	sp,sp,32
  64:	00008067          	ret
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
sw
and
or
ret
lw
mv
add
li
bne
```
