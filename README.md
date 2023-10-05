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
**Converting C Code to Assembly Language:**

I used following commands to convert it into assembly language instructions:

```bash
riscv64-unknown-elf-gcc -c -mabi=lp64 -march=rv64i -o hand-dryer.o hand-dryer.c
riscv64-unknown-elf-objdump -d hand-dryer.o
```
Assembly language instructions for the above code:

```bash

hand-dryer:     file format elf32-littleriscv


Disassembly of section .text:

00010094 <exit>:
   10094:	ff010113          	add	sp,sp,-16
   10098:	00000593          	li	a1,0
   1009c:	00812423          	sw	s0,8(sp)
   100a0:	00112623          	sw	ra,12(sp)
   100a4:	00050413          	mv	s0,a0
   100a8:	328000ef          	jal	103d0 <__call_exitprocs>
   100ac:	c281a503          	lw	a0,-984(gp) # 11a68 <_global_impure_ptr>
   100b0:	03c52783          	lw	a5,60(a0)
   100b4:	00078463          	beqz	a5,100bc <exit+0x28>
   100b8:	000780e7          	jalr	a5
   100bc:	00040513          	mv	a0,s0
   100c0:	538000ef          	jal	105f8 <_exit>

000100c4 <register_fini>:
   100c4:	00000793          	li	a5,0
   100c8:	00078863          	beqz	a5,100d8 <register_fini+0x14>
   100cc:	00010537          	lui	a0,0x10
   100d0:	4f050513          	add	a0,a0,1264 # 104f0 <__libc_fini_array>
   100d4:	4780006f          	j	1054c <atexit>
   100d8:	00008067          	ret

000100dc <_start>:
   100dc:	00002197          	auipc	gp,0x2
   100e0:	d6418193          	add	gp,gp,-668 # 11e40 <__global_pointer$>
   100e4:	c3418513          	add	a0,gp,-972 # 11a74 <completed.1>
   100e8:	c5018613          	add	a2,gp,-944 # 11a90 <__BSS_END__>
   100ec:	40a60633          	sub	a2,a2,a0
   100f0:	00000593          	li	a1,0
   100f4:	200000ef          	jal	102f4 <memset>
   100f8:	00000517          	auipc	a0,0x0
   100fc:	45450513          	add	a0,a0,1108 # 1054c <atexit>
   10100:	00050863          	beqz	a0,10110 <_start+0x34>
   10104:	00000517          	auipc	a0,0x0
   10108:	3ec50513          	add	a0,a0,1004 # 104f0 <__libc_fini_array>
   1010c:	440000ef          	jal	1054c <atexit>
   10110:	148000ef          	jal	10258 <__libc_init_array>
   10114:	00012503          	lw	a0,0(sp)
   10118:	00410593          	add	a1,sp,4
   1011c:	00000613          	li	a2,0
   10120:	120000ef          	jal	10240 <main>
   10124:	f71ff06f          	j	10094 <exit>

00010128 <__do_global_dtors_aux>:
   10128:	ff010113          	add	sp,sp,-16
   1012c:	00812423          	sw	s0,8(sp)
   10130:	c341c783          	lbu	a5,-972(gp) # 11a74 <completed.1>
   10134:	00112623          	sw	ra,12(sp)
   10138:	02079263          	bnez	a5,1015c <__do_global_dtors_aux+0x34>
   1013c:	00000793          	li	a5,0
   10140:	00078a63          	beqz	a5,10154 <__do_global_dtors_aux+0x2c>
   10144:	00011537          	lui	a0,0x11
   10148:	63050513          	add	a0,a0,1584 # 11630 <__EH_FRAME_BEGIN__>
   1014c:	00000097          	auipc	ra,0x0
   10150:	000000e7          	jalr	zero # 0 <exit-0x10094>
   10154:	00100793          	li	a5,1
   10158:	c2f18a23          	sb	a5,-972(gp) # 11a74 <completed.1>
   1015c:	00c12083          	lw	ra,12(sp)
   10160:	00812403          	lw	s0,8(sp)
   10164:	01010113          	add	sp,sp,16
   10168:	00008067          	ret

0001016c <frame_dummy>:
   1016c:	00000793          	li	a5,0
   10170:	00078c63          	beqz	a5,10188 <frame_dummy+0x1c>
   10174:	00011537          	lui	a0,0x11
   10178:	c3818593          	add	a1,gp,-968 # 11a78 <object.0>
   1017c:	63050513          	add	a0,a0,1584 # 11630 <__EH_FRAME_BEGIN__>
   10180:	00000317          	auipc	t1,0x0
   10184:	00000067          	jr	zero # 0 <exit-0x10094>
   10188:	00008067          	ret

0001018c <hand_dryer>:
   1018c:	fd010113          	add	sp,sp,-48
   10190:	02812623          	sw	s0,44(sp)
   10194:	03010413          	add	s0,sp,48
   10198:	fca42e23          	sw	a0,-36(s0)
   1019c:	fdc42703          	lw	a4,-36(s0)
   101a0:	00100793          	li	a5,1
   101a4:	04f71463          	bne	a4,a5,101ec <hand_dryer+0x60>
   101a8:	00100793          	li	a5,1
   101ac:	fef42423          	sw	a5,-24(s0)
   101b0:	1f400793          	li	a5,500
   101b4:	fef42223          	sw	a5,-28(s0)
   101b8:	fe042023          	sw	zero,-32(s0)
   101bc:	fe042623          	sw	zero,-20(s0)
   101c0:	0100006f          	j	101d0 <hand_dryer+0x44>
   101c4:	fec42783          	lw	a5,-20(s0)
   101c8:	00178793          	add	a5,a5,1
   101cc:	fef42623          	sw	a5,-20(s0)
   101d0:	fec42703          	lw	a4,-20(s0)
   101d4:	009897b7          	lui	a5,0x989
   101d8:	67f78793          	add	a5,a5,1663 # 98967f <__global_pointer$+0x97783f>
   101dc:	fee7d4e3          	bge	a5,a4,101c4 <hand_dryer+0x38>
   101e0:	fe042423          	sw	zero,-24(s0)
   101e4:	fe042223          	sw	zero,-28(s0)
   101e8:	0180006f          	j	10200 <hand_dryer+0x74>
   101ec:	fdc42783          	lw	a5,-36(s0)
   101f0:	00079863          	bnez	a5,10200 <hand_dryer+0x74>
   101f4:	fe042423          	sw	zero,-24(s0)
   101f8:	fe042223          	sw	zero,-28(s0)
   101fc:	fe042023          	sw	zero,-32(s0)
   10200:	00000013          	nop
   10204:	02c12403          	lw	s0,44(sp)
   10208:	03010113          	add	sp,sp,48
   1020c:	00008067          	ret

00010210 <setup>:
   10210:	fe010113          	add	sp,sp,-32
   10214:	00112e23          	sw	ra,28(sp)
   10218:	00812c23          	sw	s0,24(sp)
   1021c:	02010413          	add	s0,sp,32
   10220:	fe042623          	sw	zero,-20(s0)
   10224:	fec42503          	lw	a0,-20(s0)
   10228:	f65ff0ef          	jal	1018c <hand_dryer>
   1022c:	00000013          	nop
   10230:	01c12083          	lw	ra,28(sp)
   10234:	01812403          	lw	s0,24(sp)
   10238:	02010113          	add	sp,sp,32
   1023c:	00008067          	ret

00010240 <main>:
   10240:	ff010113          	add	sp,sp,-16
   10244:	00112623          	sw	ra,12(sp)
   10248:	00812423          	sw	s0,8(sp)
   1024c:	01010413          	add	s0,sp,16
   10250:	fc1ff0ef          	jal	10210 <setup>
   10254:	ffdff06f          	j	10250 <main+0x10>

00010258 <__libc_init_array>:
   10258:	ff010113          	add	sp,sp,-16
   1025c:	00812423          	sw	s0,8(sp)
   10260:	000117b7          	lui	a5,0x11
   10264:	00011437          	lui	s0,0x11
   10268:	01212023          	sw	s2,0(sp)
   1026c:	63478793          	add	a5,a5,1588 # 11634 <__init_array_start>
   10270:	63440713          	add	a4,s0,1588 # 11634 <__init_array_start>
   10274:	00112623          	sw	ra,12(sp)
   10278:	00912223          	sw	s1,4(sp)
   1027c:	40e78933          	sub	s2,a5,a4
   10280:	02e78263          	beq	a5,a4,102a4 <__libc_init_array+0x4c>
   10284:	40295913          	sra	s2,s2,0x2
   10288:	63440413          	add	s0,s0,1588
   1028c:	00000493          	li	s1,0
   10290:	00042783          	lw	a5,0(s0)
   10294:	00148493          	add	s1,s1,1
   10298:	00440413          	add	s0,s0,4
   1029c:	000780e7          	jalr	a5
   102a0:	ff24e8e3          	bltu	s1,s2,10290 <__libc_init_array+0x38>
   102a4:	00011437          	lui	s0,0x11
   102a8:	000117b7          	lui	a5,0x11
   102ac:	63c78793          	add	a5,a5,1596 # 1163c <__do_global_dtors_aux_fini_array_entry>
   102b0:	63440713          	add	a4,s0,1588 # 11634 <__init_array_start>
   102b4:	40e78933          	sub	s2,a5,a4
   102b8:	40295913          	sra	s2,s2,0x2
   102bc:	02e78063          	beq	a5,a4,102dc <__libc_init_array+0x84>
   102c0:	63440413          	add	s0,s0,1588
   102c4:	00000493          	li	s1,0
   102c8:	00042783          	lw	a5,0(s0)
   102cc:	00148493          	add	s1,s1,1
   102d0:	00440413          	add	s0,s0,4
   102d4:	000780e7          	jalr	a5
   102d8:	ff24e8e3          	bltu	s1,s2,102c8 <__libc_init_array+0x70>
   102dc:	00c12083          	lw	ra,12(sp)
   102e0:	00812403          	lw	s0,8(sp)
   102e4:	00412483          	lw	s1,4(sp)
   102e8:	00012903          	lw	s2,0(sp)
   102ec:	01010113          	add	sp,sp,16
   102f0:	00008067          	ret

000102f4 <memset>:
   102f4:	00f00313          	li	t1,15
   102f8:	00050713          	mv	a4,a0
   102fc:	02c37e63          	bgeu	t1,a2,10338 <memset+0x44>
   10300:	00f77793          	and	a5,a4,15
   10304:	0a079063          	bnez	a5,103a4 <memset+0xb0>
   10308:	08059263          	bnez	a1,1038c <memset+0x98>
   1030c:	ff067693          	and	a3,a2,-16
   10310:	00f67613          	and	a2,a2,15
   10314:	00e686b3          	add	a3,a3,a4
   10318:	00b72023          	sw	a1,0(a4)
   1031c:	00b72223          	sw	a1,4(a4)
   10320:	00b72423          	sw	a1,8(a4)
   10324:	00b72623          	sw	a1,12(a4)
   10328:	01070713          	add	a4,a4,16
   1032c:	fed766e3          	bltu	a4,a3,10318 <memset+0x24>
   10330:	00061463          	bnez	a2,10338 <memset+0x44>
   10334:	00008067          	ret
   10338:	40c306b3          	sub	a3,t1,a2
   1033c:	00269693          	sll	a3,a3,0x2
   10340:	00000297          	auipc	t0,0x0
   10344:	005686b3          	add	a3,a3,t0
   10348:	00c68067          	jr	12(a3)
   1034c:	00b70723          	sb	a1,14(a4)
   10350:	00b706a3          	sb	a1,13(a4)
   10354:	00b70623          	sb	a1,12(a4)
   10358:	00b705a3          	sb	a1,11(a4)
   1035c:	00b70523          	sb	a1,10(a4)
   10360:	00b704a3          	sb	a1,9(a4)
   10364:	00b70423          	sb	a1,8(a4)
   10368:	00b703a3          	sb	a1,7(a4)
   1036c:	00b70323          	sb	a1,6(a4)
   10370:	00b702a3          	sb	a1,5(a4)
   10374:	00b70223          	sb	a1,4(a4)
   10378:	00b701a3          	sb	a1,3(a4)
   1037c:	00b70123          	sb	a1,2(a4)
   10380:	00b700a3          	sb	a1,1(a4)
   10384:	00b70023          	sb	a1,0(a4)
   10388:	00008067          	ret
   1038c:	0ff5f593          	zext.b	a1,a1
   10390:	00859693          	sll	a3,a1,0x8
   10394:	00d5e5b3          	or	a1,a1,a3
   10398:	01059693          	sll	a3,a1,0x10
   1039c:	00d5e5b3          	or	a1,a1,a3
   103a0:	f6dff06f          	j	1030c <memset+0x18>
   103a4:	00279693          	sll	a3,a5,0x2
   103a8:	00000297          	auipc	t0,0x0
   103ac:	005686b3          	add	a3,a3,t0
   103b0:	00008293          	mv	t0,ra
   103b4:	fa0680e7          	jalr	-96(a3)
   103b8:	00028093          	mv	ra,t0
   103bc:	ff078793          	add	a5,a5,-16
   103c0:	40f70733          	sub	a4,a4,a5
   103c4:	00f60633          	add	a2,a2,a5
   103c8:	f6c378e3          	bgeu	t1,a2,10338 <memset+0x44>
   103cc:	f3dff06f          	j	10308 <memset+0x14>

000103d0 <__call_exitprocs>:
   103d0:	fd010113          	add	sp,sp,-48
   103d4:	01412c23          	sw	s4,24(sp)
   103d8:	c281aa03          	lw	s4,-984(gp) # 11a68 <_global_impure_ptr>
   103dc:	03212023          	sw	s2,32(sp)
   103e0:	02112623          	sw	ra,44(sp)
   103e4:	148a2903          	lw	s2,328(s4)
   103e8:	02812423          	sw	s0,40(sp)
   103ec:	02912223          	sw	s1,36(sp)
   103f0:	01312e23          	sw	s3,28(sp)
   103f4:	01512a23          	sw	s5,20(sp)
   103f8:	01612823          	sw	s6,16(sp)
   103fc:	01712623          	sw	s7,12(sp)
   10400:	01812423          	sw	s8,8(sp)
   10404:	04090063          	beqz	s2,10444 <__call_exitprocs+0x74>
   10408:	00050b13          	mv	s6,a0
   1040c:	00058b93          	mv	s7,a1
   10410:	00100a93          	li	s5,1
   10414:	fff00993          	li	s3,-1
   10418:	00492483          	lw	s1,4(s2)
   1041c:	fff48413          	add	s0,s1,-1
   10420:	02044263          	bltz	s0,10444 <__call_exitprocs+0x74>
   10424:	00249493          	sll	s1,s1,0x2
   10428:	009904b3          	add	s1,s2,s1
   1042c:	040b8463          	beqz	s7,10474 <__call_exitprocs+0xa4>
   10430:	1044a783          	lw	a5,260(s1)
   10434:	05778063          	beq	a5,s7,10474 <__call_exitprocs+0xa4>
   10438:	fff40413          	add	s0,s0,-1
   1043c:	ffc48493          	add	s1,s1,-4
   10440:	ff3416e3          	bne	s0,s3,1042c <__call_exitprocs+0x5c>
   10444:	02c12083          	lw	ra,44(sp)
   10448:	02812403          	lw	s0,40(sp)
   1044c:	02412483          	lw	s1,36(sp)
   10450:	02012903          	lw	s2,32(sp)
   10454:	01c12983          	lw	s3,28(sp)
   10458:	01812a03          	lw	s4,24(sp)
   1045c:	01412a83          	lw	s5,20(sp)
   10460:	01012b03          	lw	s6,16(sp)
   10464:	00c12b83          	lw	s7,12(sp)
   10468:	00812c03          	lw	s8,8(sp)
   1046c:	03010113          	add	sp,sp,48
   10470:	00008067          	ret
   10474:	00492783          	lw	a5,4(s2)
   10478:	0044a683          	lw	a3,4(s1)
   1047c:	fff78793          	add	a5,a5,-1
   10480:	04878e63          	beq	a5,s0,104dc <__call_exitprocs+0x10c>
   10484:	0004a223          	sw	zero,4(s1)
   10488:	fa0688e3          	beqz	a3,10438 <__call_exitprocs+0x68>
   1048c:	18892783          	lw	a5,392(s2)
   10490:	008a9733          	sll	a4,s5,s0
   10494:	00492c03          	lw	s8,4(s2)
   10498:	00f777b3          	and	a5,a4,a5
   1049c:	02079263          	bnez	a5,104c0 <__call_exitprocs+0xf0>
   104a0:	000680e7          	jalr	a3
   104a4:	00492703          	lw	a4,4(s2)
   104a8:	148a2783          	lw	a5,328(s4)
   104ac:	01871463          	bne	a4,s8,104b4 <__call_exitprocs+0xe4>
   104b0:	f92784e3          	beq	a5,s2,10438 <__call_exitprocs+0x68>
   104b4:	f80788e3          	beqz	a5,10444 <__call_exitprocs+0x74>
   104b8:	00078913          	mv	s2,a5
   104bc:	f5dff06f          	j	10418 <__call_exitprocs+0x48>
   104c0:	18c92783          	lw	a5,396(s2)
   104c4:	0844a583          	lw	a1,132(s1)
   104c8:	00f77733          	and	a4,a4,a5
   104cc:	00071c63          	bnez	a4,104e4 <__call_exitprocs+0x114>
   104d0:	000b0513          	mv	a0,s6
   104d4:	000680e7          	jalr	a3
   104d8:	fcdff06f          	j	104a4 <__call_exitprocs+0xd4>
   104dc:	00892223          	sw	s0,4(s2)
   104e0:	fa9ff06f          	j	10488 <__call_exitprocs+0xb8>
   104e4:	00058513          	mv	a0,a1
   104e8:	000680e7          	jalr	a3
   104ec:	fb9ff06f          	j	104a4 <__call_exitprocs+0xd4>

000104f0 <__libc_fini_array>:
   104f0:	ff010113          	add	sp,sp,-16
   104f4:	00812423          	sw	s0,8(sp)
   104f8:	000117b7          	lui	a5,0x11
   104fc:	00011437          	lui	s0,0x11
   10500:	63c78793          	add	a5,a5,1596 # 1163c <__do_global_dtors_aux_fini_array_entry>
   10504:	64040413          	add	s0,s0,1600 # 11640 <impure_data>
   10508:	40f40433          	sub	s0,s0,a5
   1050c:	00912223          	sw	s1,4(sp)
   10510:	00112623          	sw	ra,12(sp)
   10514:	40245493          	sra	s1,s0,0x2
   10518:	02048063          	beqz	s1,10538 <__libc_fini_array+0x48>
   1051c:	ffc40413          	add	s0,s0,-4
   10520:	00f40433          	add	s0,s0,a5
   10524:	00042783          	lw	a5,0(s0)
   10528:	fff48493          	add	s1,s1,-1
   1052c:	ffc40413          	add	s0,s0,-4
   10530:	000780e7          	jalr	a5
   10534:	fe0498e3          	bnez	s1,10524 <__libc_fini_array+0x34>
   10538:	00c12083          	lw	ra,12(sp)
   1053c:	00812403          	lw	s0,8(sp)
   10540:	00412483          	lw	s1,4(sp)
   10544:	01010113          	add	sp,sp,16
   10548:	00008067          	ret

0001054c <atexit>:
   1054c:	00050593          	mv	a1,a0
   10550:	00000693          	li	a3,0
   10554:	00000613          	li	a2,0
   10558:	00000513          	li	a0,0
   1055c:	0040006f          	j	10560 <__register_exitproc>

00010560 <__register_exitproc>:
   10560:	c281a703          	lw	a4,-984(gp) # 11a68 <_global_impure_ptr>
   10564:	14872783          	lw	a5,328(a4)
   10568:	04078c63          	beqz	a5,105c0 <__register_exitproc+0x60>
   1056c:	0047a703          	lw	a4,4(a5)
   10570:	01f00813          	li	a6,31
   10574:	06e84e63          	blt	a6,a4,105f0 <__register_exitproc+0x90>
   10578:	00271813          	sll	a6,a4,0x2
   1057c:	02050663          	beqz	a0,105a8 <__register_exitproc+0x48>
   10580:	01078333          	add	t1,a5,a6
   10584:	08c32423          	sw	a2,136(t1) # 10208 <hand_dryer+0x7c>
   10588:	1887a883          	lw	a7,392(a5)
   1058c:	00100613          	li	a2,1
   10590:	00e61633          	sll	a2,a2,a4
   10594:	00c8e8b3          	or	a7,a7,a2
   10598:	1917a423          	sw	a7,392(a5)
   1059c:	10d32423          	sw	a3,264(t1)
   105a0:	00200693          	li	a3,2
   105a4:	02d50463          	beq	a0,a3,105cc <__register_exitproc+0x6c>
   105a8:	00170713          	add	a4,a4,1
   105ac:	00e7a223          	sw	a4,4(a5)
   105b0:	010787b3          	add	a5,a5,a6
   105b4:	00b7a423          	sw	a1,8(a5)
   105b8:	00000513          	li	a0,0
   105bc:	00008067          	ret
   105c0:	14c70793          	add	a5,a4,332
   105c4:	14f72423          	sw	a5,328(a4)
   105c8:	fa5ff06f          	j	1056c <__register_exitproc+0xc>
   105cc:	18c7a683          	lw	a3,396(a5)
   105d0:	00170713          	add	a4,a4,1
   105d4:	00e7a223          	sw	a4,4(a5)
   105d8:	00c6e6b3          	or	a3,a3,a2
   105dc:	18d7a623          	sw	a3,396(a5)
   105e0:	010787b3          	add	a5,a5,a6
   105e4:	00b7a423          	sw	a1,8(a5)
   105e8:	00000513          	li	a0,0
   105ec:	00008067          	ret
   105f0:	fff00513          	li	a0,-1
   105f4:	00008067          	ret

000105f8 <_exit>:
   105f8:	05d00893          	li	a7,93
   105fc:	00000073          	ecall
   10600:	00054463          	bltz	a0,10608 <_exit+0x10>
   10604:	0000006f          	j	10604 <_exit+0xc>
   10608:	ff010113          	add	sp,sp,-16
   1060c:	00812423          	sw	s0,8(sp)
   10610:	00050413          	mv	s0,a0
   10614:	00112623          	sw	ra,12(sp)
   10618:	40800433          	neg	s0,s0
   1061c:	00c000ef          	jal	10628 <__errno>
   10620:	00852023          	sw	s0,0(a0)
   10624:	0000006f          	j	10624 <_exit+0x2c>

00010628 <__errno>:
   10628:	c301a503          	lw	a0,-976(gp) # 11a70 <_impure_ptr>
   1062c:	00008067          	ret
```
**Extracting Unique Instructions:**

I used this code in python to obtain the unique instructions:

```bash
with open('assembly.txt', 'r') as file:
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
Number of different instructions: 32
List of unique instructions:
nop
beqz
zext.b
neg
bne
jalr
jal
jr
mv
add
bltz
bltu
lbu
bgeu
lui
lw
li
bge
beq
ret
sb
sll
sra
sub
or
blt
ecall
j
and
auipc
sw
bnez
```
