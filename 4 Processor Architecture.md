# <center>Chapter 4  Processor Architecture

## 4.1 The Y86-64 Instruction Set Architecture

### 4.1.1 Programmer-Visible State

<font size =4.5 face="Consolas">

* RF:Program register
* CC:Condition codes  (ZF SF OF)
* PC:Program counter
* Memory
* Stat:program execution

![avatar](./Pictures/4/Y86-64%20programmer%20visible%20state.png)

</font>

### 4.1.2 Y86-64 Instructions

![avatar](./Pictures/4/Y86-64%20Instruction%20Set.png)

### 4.1.3 Instruction Encoding

<font size =4.5 face="Consolas">
Every instruction has an initial byte identifying the instruction type.

</br>
</br>
high order 4-bit part:  <b><i>code</b></i>

low order 4-bit part: <b><i>function</b></i>

![avatar](./Pictures/4/Function%20codes%20for%20ISA.png)

![avatar](./Pictures/4/registers%20identifiers.png)

</font>

## 4.2 Logic Design and the Hardware Control Language

<font size =4.5 face="Consolas">
This chapter is mainly about digital circuits. Nothing new.
</font>

## 4.3 Sequential Y86-64 Implementations

### 4.3.1 Organizing Processing into Stages

<font size =4.5 face="Consolas">

***Fetch***

***Decode***

***Execute***

***Memory***

***Write back***

***PC update***
</font>

### 4.3.2 SEQ Hardware Structure

![avatar](./Pictures/4/Abstract%20view%20of%20SEQ.png)
![avatar](./Pictures/4/Hardware%20Structure%20of%20SEQ.png)