### 实验报告：

章节：ch.3
时间：2024.10.17

代码总结：
1. 在TaskControlBlock中新增sys_call_times
2. TaskManager 中实现 sys_call_count与get_syscall_times方法，分别获得调用次数与执行时间。
再实现两个方法的对外接口
3. syscall/mod.rs的syscall方法中调用sys_call_count用于记录调用次数
4. syscal/process的sys_task_info中实现TaskInfo结构体

#### 简答作业
环境版本:
[rustsbi] RustSBI version 0.3.0-alpha.2, adapting to RISC-V SBI v1.0.0
##### 简答1
正确进入 U 态后，程序的特征还应有：使用 S 态特权指令，访问 S 态寄存器后会报错。 
请同学们可以自行测试这些内容（运行 [三个 bad 测例 (ch2b_bad_*.rs)](https://github.com/LearningOS/rCore-Tutorial-Test-2024A/tree/master/src/bin) ）, 描述程序出错行为，同时注意注明你使用的 sbi 及其版本。
[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x804003a4, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.

答:
bad_address -> 访问0x0位置会导致错误,所以kernel killed
bad_instructions -> 从panic与main函数可能看出,于用户态执行了内核态的 sret 指令,导致kernel killed
bad_register -> 同 bad_instructions,于用户态使用了非法指令.

##### 简答2.1
a0 是一个通用寄存器，通常用于函数调用时传递第一个参数.
__restore 的两种使用情景可能包括：
1. 从中断处理返回到被中断的代码.
2. 从异常处理返回到导致异常的代码.
##### 简答2.2
`ld t0, 32*8(sp)`：这条指令从栈指针（sp）指向的地址加载数据到临时寄存器t0。
`ld t1, 33*8(sp)`：这条指令从栈指针（sp）指向的地址加载数据到临时寄存器t1。
`ld t2, 2*8(sp)`：这条指令从栈指针（sp）指向的地址加载数据到临时寄存器t2。
`csrw sstatus, t0`：这条指令将t0寄存器的值写入控制状态寄存器sstatus。
`csrw sepc, t1`：这条指令将t1寄存器的值写入程序计数器寄存器sepc。
`csrw sscratch, t2`：这条指令将t2寄存器的值写入scratch寄存器sscratch。
##### 简答2.3
问:L50-L56：为何跳过了 `x2` 和 `x4`
.rept 从n=5开始,执行27次,每次+1,跳过了偶数寄存器,可能被保留用于特定目的
##### 简答2.4
问:L60：该指令之后，`sp` 和 `sscratch` 中的值分别有什么意义
寄存器中内容可能发生了交换
##### 简答2.5
问:`_restore`：中发生状态切换在哪一条指令？为何该指令执行之后会进入用户态？
最后的 sret 指令,执行sret后,从S模式返回到陷入S模式之前的状态继续执行.
##### 简答2.6
问:L13：该指令之后，`sp` 和 `sscratch` 中的值分别有什么意义？
寄存器中内容发生了交换,可能为了在内核态和用户态之间正确切换栈环境。
##### 简答2.7
问:从 U 态进入 S 态是哪一条指令发生的
ecall 指令

### 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 以下各位 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：
《你交流的对象说明》

2. 此外，我也参考了以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：
《你参考的资料说明》

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。
4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。