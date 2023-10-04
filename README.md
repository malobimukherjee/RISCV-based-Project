# RISCV-based-Project




**Implementing a Hand-dryer:**

Block Diagram of the Implementation:

<img width="1000" alt="image" src="https://github.com/malobimukherjee/RISCV-based-Project/assets/141206513/9a824507-6431-4f92-acc0-d9a41b901dd8">


C Code:

```bash
#include<stdio.h>

void hand_dryer(int input_pirState){

       int output_fanspeed;
       int output_motorDir1;
       int output_motorDir2;

     //sensors gives 1 if it detects hand 
    if(input_pirState == 1){
        output_motorDir1=1;
        output_fanspeed = 500;
        output_motorDir2=0;
        
         
       
        for (int i = 0; i < 10000000; i++) 
        {
        // Empty loop for delay
    	}
        
        output_motorDir1=0;
        output_fanspeed = 0;
        
        
    }
    //sensor not detecting any obstacle
    else if(input_pirState == 0){
        output_motorDir1=0;
        output_fanspeed = 0;
        output_motorDir2=0;
        
    }
    
}

void setup(){
    int input_pirState=0;
  
    
    // input_pirState = digital_read(0);
    
   
    hand_dryer(input_pirState); 
   
    // digitalWrite(output_motorDir1, 0);         // Pin set to LOW
    // digitalWrite(output_motorDir2, 0);         // Pin set to LOW
    // analogWrite(output_fanspeed, 0);           

}

int main(){
    
    // H-Bridge Motor Driver is used 
    while(1){
        setup();
    }
    return(0);
}
```

I compiled the C code using the commands:

```bash
gcc hand-dryer.c
./a.out
```

I used following commands to convert it into assembly language instructions:

```bash
riscv64-unknown-elf-gcc -c -mabi=lp64 -march=rv64i -o hand-dryer.o hand-dryer.c
riscv64-unknown-elf-objdump -d hand-dryer.o
```
Assembly language instructions for the above code:

```bash
0000000000000000 <hand_dryer>:
   0:	fd010113          	addi	sp,sp,-48
   4:	02813423          	sd	s0,40(sp)
   8:	03010413          	addi	s0,sp,48
   c:	00050793          	mv	a5,a0
  10:	fcf42e23          	sw	a5,-36(s0)
  14:	fdc42783          	lw	a5,-36(s0)
  18:	0007871b          	sext.w	a4,a5
  1c:	00100793          	li	a5,1
  20:	04f71663          	bne	a4,a5,6c <.L2>
  24:	00100793          	li	a5,1
  28:	fef42423          	sw	a5,-24(s0)
  2c:	1f400793          	li	a5,500
  30:	fef42223          	sw	a5,-28(s0)
  34:	fe042023          	sw	zero,-32(s0)
  38:	fe042623          	sw	zero,-20(s0)
  3c:	0100006f          	j	4c <.L3>

0000000000000040 <.L4>:
  40:	fec42783          	lw	a5,-20(s0)
  44:	0017879b          	addiw	a5,a5,1
  48:	fef42623          	sw	a5,-20(s0)

000000000000004c <.L3>:
  4c:	fec42783          	lw	a5,-20(s0)
  50:	0007871b          	sext.w	a4,a5
  54:	009897b7          	lui	a5,0x989
  58:	67f78793          	addi	a5,a5,1663 # 98967f <.L9+0x9895a3>
  5c:	fee7d2e3          	bge	a5,a4,40 <.L4>
  60:	fe042423          	sw	zero,-24(s0)
  64:	fe042223          	sw	zero,-28(s0)
  68:	01c0006f          	j	84 <.L6>

000000000000006c <.L2>:
  6c:	fdc42783          	lw	a5,-36(s0)
  70:	0007879b          	sext.w	a5,a5
  74:	00079863          	bnez	a5,84 <.L6>
  78:	fe042423          	sw	zero,-24(s0)
  7c:	fe042223          	sw	zero,-28(s0)
  80:	fe042023          	sw	zero,-32(s0)

0000000000000084 <.L6>:
  84:	00000013          	nop
  88:	02813403          	ld	s0,40(sp)
  8c:	03010113          	addi	sp,sp,48
  90:	00008067          	ret

0000000000000094 <setup>:
  94:	fe010113          	addi	sp,sp,-32
  98:	00113c23          	sd	ra,24(sp)
  9c:	00813823          	sd	s0,16(sp)
  a0:	02010413          	addi	s0,sp,32
  a4:	fe042623          	sw	zero,-20(s0)
  a8:	fec42783          	lw	a5,-20(s0)
  ac:	00078513          	mv	a0,a5
  b0:	00000097          	auipc	ra,0x0
  b4:	000080e7          	jalr	ra # b0 <setup+0x1c>
  b8:	00000013          	nop
  bc:	01813083          	ld	ra,24(sp)
  c0:	01013403          	ld	s0,16(sp)
  c4:	02010113          	addi	sp,sp,32
  c8:	00008067          	ret

00000000000000cc <main>:
  cc:	ff010113          	addi	sp,sp,-16
  d0:	00113423          	sd	ra,8(sp)
  d4:	00813023          	sd	s0,0(sp)
  d8:	01010413          	addi	s0,sp,16

00000000000000dc <.L9>:
  dc:	00000097          	auipc	ra,0x0
  e0:	000080e7          	jalr	ra # dc <.L9>
  e4:	ff9ff06f          	j	dc <.L9>
```

I used this code in python to obtain the unique instructions:

```bash
with open('hand-dryer_asm.txt', 'r') as file:
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
Number of different instructions: 18
List of unique instructions:
bnez
ret
sext.w
j
jalr
sw
addiw
lui
bge
bne
addi
auipc
lw
sd
ld
nop
li
mv
```
