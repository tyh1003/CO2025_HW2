# Computer Organization  計算機組織 2025 Programming Assignment II

## 課程資訊
- **課程**: 成功大學資訊系 大二必修
- **學年**: 113學年度下學期
- **教授**: 涂嘉恒 (Tu, Chia-Heng)
- **作者**: MQ (GM 115)

## 作業說明
### 作業概述  
RISC-V processors should support a core ISA for integer operations, including RV32I,
RV32E, RV64I, or RV128I. Additional functionality could be adopted to augment the
capability of target RISC-V processors. RISC-V has a series of standard extensions to
provide additional support beyond the core ISA, such as floating point and bit manipulation
RISC-V ISA List. On the other hand, there is also a series of non-standard extensions,
which might be specialized for certain purposes and might conflict with other extensions.
If you are interested in the related contents, please refer to the document Extending RISCV.
Particularly, the RISC-V M extension defines multiplication and division operations for
integers. The RISC-V F/D extensions are the computation operations for single/double
precision floating point numbers. The RISC-V Vector extension is a promising extension for
the AI computing as it enables the parallel processing of mathematic operations on a
RISC-V processor. The V extension involves adding a vector computation engine on the
RISC-V processor, compared with the serial computing on a typical processor.
In this assignment, you will be asked to convert the given C code segments into the
corresponding assembly versions, based on the skills you learned from the previous
programming assignment. You will use the RISC-V extensions to implement your programs.
More importantly, you will be asked to collect the performance data for your written code
(3. Performance Data Collection), and you need to use the performance data to derive the
execution time of your program on the RISC-V processor based on a basic performance
model (1. Performance Modeling). Besides, with the collected performance data, you are
able to further characterize the performance of the running programs, where a common
performance characterization method to analyze if a given program is bounded by CPU or
Memory (I/O) is provided in 2. Performance Characterization.  
RISC-V 處理器應支援整數運算的核心指令集（ISA），包括 RV32I、RV32E、RV64I 或 RV128I。為了增強目標 RISC-V 處理器的能力，也可以採用其他擴充功能。RISC-V 提供了一系列標準擴充指令集，以支援核心指令集以外的功能，例如浮點運算與位元操作（bit manipulation），詳見 RISC-V ISA 列表。  
另一方面，也有一些非標準擴充指令集，這些擴充可能是針對特定用途設計，並且可能會與其他擴充有衝突。如果你對這部分有興趣，請參閱文件《Extending RISC-V》。  
特別地，RISC-V 的 M 擴充 定義了整數的乘法與除法操作；F/D 擴充 則是針對單/雙精度浮點數的計算；V 向量擴充 是一種非常有潛力的擴充，適合 AI 計算，因為它讓 RISC-V 處理器能進行數學運算的並行處理。V 擴充會在處理器上增加一個向量計算引擎，相較於典型處理器的序列計算，提供更高效能。  
在本次作業中，你將把指定的 C 程式片段轉換為對應的組合語版本，並使用你從前次程式作業中學到的技能。你將使用 RISC-V 的擴充指令來完成程式。  
更重要的是，你將需要收集你撰寫的程式的效能資料（參見 3. 效能資料收集），並根據基本效能模型（1. 效能建模）推導出程式在 RISC-V 處理器上的執行時間。此外，透過所收集的效能資料，你還能進一步對程式進行效能分析，例如分析程式是屬於 CPU Bound 還是 Memory (I/O) Bound（詳見 2. 效能特性分析）。
### 1.Performance Modeling
**We can use clock cycles per instruction (CPI),instruction counts, and clock cycle time to derive the CPU execution time.**  
- In this project,use **fixed CPI numbers** , and it can impact the **CPU pipeline** and the **memory subsystem.**  
- You must collect the **performance** data of
your program to derive the CPU execution time in this project.
- The following table lists the variables used in this assignments to count the number of
Integer,Memory Access, Floating Point operations. 
- **The cycle_time** represents the clock cycle time for the target RISC-V
processor. It is a constant data and its content should not be altered.

下表已定義於 header 檔中，**不可修改**。
|指令|CPI常數|相關指令|  
|---------|---------|---------| 
| add_cnt | add_CPI |add{i}、vadd.vv、vadd.vx、vadd.vi|
| sub_cnt | sub_CPI |sub{i}、vsub.vv、vsub.vx|  
| mul_cnt | mul_CPI |mul、vmul.vv、vmul.vx|  
| div_cnt | div_CPI |div、vdiv.vv、vdiv.vx|  
| lw_cnt  | lw_CPI |lw、lh、lb、lbu、lhu、vle8.v、vle16.v、vle32.v、vle64.v|  
| sw_cnt  | sw_CPI |sw、sh、sb、vse8.v、vse16.v、vse32.v、vse64.v|  
| others_cnt | others_CPI |others|
|fadd_cnt|?|fadd.s、vfadd.vf、vfadd.vv 
|fsub_cnt|?| fsub.s、vfsub.vf、vfsub.vv
|fmul_cnt|?| fmul.s、vfmul.vf、vfmul.vv
|fdiv_cnt|?|fdiv.s、vfdiv.vf、vfdiv.vv
|flw_cnt|?|flw
| fsw_cnt|?      | fsw|
| dadd_cnt|?     | fadd.d| 
| dsub_cnt|?     | fsub.d | 
| dmul_cnt|?     | fmul.d| 
| ddiv_cnt|?     |fdiv.d| 
| dlw_cnt |?     |fld| 
| dsw_cnt |?     |fsd|

### 2.Performance Characterization
There are **CPU-bound** and **Memory-bound** may be the bottleneck in a program’s execution.
- **CPU-bound(受限CPU)**  
任務或程式執行高度依賴CPU，處理器的使用率>>>其他元件，會需要CPU長時間的運算。ex. HRC(高效能運算)、圖形運算。*處理器運算能力*  
- **Memory-bound(受限記憶體)**
因記憶體相關操作，而導致效能下降的任務，嘗試處理更多請求時，記憶體會先達到極限。*記憶體子系統*
- 此作業，著重判斷CPU-bound或Memory-bound
  - 計算 非load/store指令所耗的時鐘週期
  - 再除以所有指令的時鐘週期
  - 比值>0.5-->CPU；反之，則Memory

最後的成果，請參考 **marco_defined.h**

### 3.Performance Data Collection
You need to use source-level code instrumentation to collect performance data. And you need to calculate all program's **cycle count** and **CPU execution time**.  
- **Cycle count** can  computed with counter values and the given CPI  (defined in
the header files).  
- **CPU execution time** can compute based on the cycle time of the target processor (*fft.h*)
- calculate the **ratio** of the time spent on CPU/Memory

### 4.Assignment
**notice in assignment**
- **Write the code on your own.**
- **Must write your code inside asm volatile()**
- **Any modifications outside of asm
volatile() are not allowed**
- **There will be hidden test cases**
- **Pseudo instructions are not allowed**
- **Don't write any comments in the C files of inline assembly codes**
- **Labels and instructions should be written on separate lines**

#### Q1 (40%)
- 說明
    - 使用RISC-V RV64G實作FFT（快速傅立葉轉換）
    - 使用 Cooley-Tukey Radix-2 DIF（頻域抽取）FFT 演算法
    - FFT 演算法簡述（Radix-2 DIF）
        - 執行 butterfly 運算
        旋轉因子、運算順序被打散
        - 進行 bit-reverse 排序
        結果重新回到原本 x[0] ~ x[7] 的順序
- 輸入
*fft_input.txt* ，8 組複數數字，每組為 (實部, 虛部)，皆為 float，範圍為 -10.0 ~ 10.0
- 輸出
系統會自動印出 FFT 結果陣列 X[0]~X[7]，你不需自行印出
---
- complex_add.c
```c
//in c
result.Re = a.Re + b.Re;
result.Im = a.Im + b.Im;
//in assembly
//載入檔案
//相加
//存回
```
- complex_sub.c
```c
//in c
result.Re = a.Re - b.Re;
result.Im = a.Im - b.Im;
//in assembly
//載入檔案
//相減
//存回
```
- complex_mul.c
**有順序問題，無法先加減再乘除，目前不知道如何解決。**
```c
//in c
//in assembly
//載入檔案
//先乘完每一部分
//再相加減
//存回
```
- pi.c
*使用公式*
```c
//公式提示
    /* description: Gregory-Leibniz series
    * 
    *    we use Leibniz formula to approximate Pi
    * 
    *    pi/4 = (1 - 1/3 + 1/5 - 1/7 + 1/9 - ...)
    *    pi   = 4(1 - 1/3 + 1/5 - 1/7 + 1/9 - ...)
    * 
    */
//in assembly
//載入檔案
//設定初始值
//迴圈
//將值變成倒數、負數
//判斷正負
//計算成果
//存回
```
- log2.c
*左右移動，得二進位log值*
```c
//in c
//in assembly
//載入檔案
//設定初始值
//迴圈
//右移
//值加進去
//存回
```
- bit_reverse.c
*很像交換數值的寫法、記得左右移動*
```c
//in c
//in assembly
//載入檔案
//設定初始值
//迴圈
//左移下一位
//值判斷01
//右移存入
```
---
#### Q2 (40%)
- **Q2_1 (18%)**
- 說明  
需要使用 RV64G 指令集架構（ISA），對兩個陣列進行元素對元素的相乘運算，再收集效能資料並計算相關效能統計。
    - 單精度浮點數陣列相乘（不使用 V 擴充）
    - inline assembly 效能資料。  
    - 用到的暫存器:
    - 修改的檔案:
    - example  若有兩個陣列：
h[4] = {0.1, 0.2, 0.3, 0.4} 和 x[4] = {0.2, 0.3, 0.4, 0.5}，
則乘法結果為：
y[4] = {0.02, 0.06, 0.12, 0.20}。  
- 輸入:
*arraymul_input.txt* ，每個值為float，精確到小數點後六位。數值範圍：*0.0~100.0*，共會讀入 兩組陣列 h[] 與 x[]，每組長度為 arr_size，arr_size 的取值為：*2~128*，且必須是 2 的次方。
- 輸出:
相乘成果
```C
//C
for (int i = 0; i < arr_size; i++){
p_y[i] = p_h[i] * p_x[i] + id;
}
//in assembly
//迴圈
//讀值
//計算
//移動
//判斷迴圈
//存回
```
- **Q2_2**(22%)  
- 說明
將先前用 RISC-V RV64G ISA 所寫的程式，改寫為使用 RISC-V V Extension 向量指令（即 SIMD 向量平行運算）。
    - 單精度浮點數陣列相乘（使用 V 擴充）
    - inline assembly 效能資料。 
    - 用到的暫存器:
    - 修改的檔案: 
    - example  若有兩個陣列：
h[4] = {0.1, 0.2, 0.3, 0.4} 和 x[4] = {0.2, 0.3, 0.4, 0.5}，
則乘法結果為：
y[4] = {0.02, 0.06, 0.12, 0.20}。
- 輸入  
*arraymul_input.txt* ，每個值為float，精確到小數點後六位。數值範圍：*0.0~100.0*，共會讀入 兩組陣列 h[] 與 x[]，每組長度為 arr_size，arr_size 的取值為：*2~128*，且必須是 2 的次方。
- 輸出:
相乘成果
**要用陣列方式寫，詳見:[RISC-V Vector Extension v1.1 說明文件](https://eupilot.eu/wp-content/uploads/2022/11/RISC-V-VectorExtension-1-1.pdf)**

```C
//C
for (int i = 0; i < arr_size; i++) {
    y[i] = h[i] * x[i] + id;
}
//in assembly
//初始化 使用向量
//讀值
//計算
//移動
//判斷迴圈
//存回
```
**補充:額外閱讀 RISC-V Vector Extension**
##### 🚀 基本運作狀態 (Operational State)
RVV 操作向量時會用到兩個暫存器：
- `vtype`：向量型態（Vector Type）
- `vl`：實際操作的元素數（Vector Length）  
  ⚠ 不同於硬體支援的最大長度 VLEN！
##### 📐 vtype 組成
| 欄位 | 說明 |
|------|------|
| `sew` | Standard Element Width，每個元素的位元數，8 ≤ sew ≤ ELEN |
| `lmul` | Length Multiplier，控制向量暫存器群的使用方式（如下表） |
###### lmul 可設定值：
| lmul | 對應倍數 | 說明 |
|------|-----------|------|
| mf8  | 1/8        | 使用暫存器的一小部分 |
| mf4  | 1/4        |   |
| mf2  | 1/2        |   |
| m1   | 1          | 使用單一暫存器 |
| m2   | 2          | 使用 2 組暫存器（v0, v1） |
| m4   | 4          | 使用 4 組暫存器（v0~v3） |
| m8   | 8          | 使用 8 組暫存器（v0~v7） |
##### 📏 vl（Vector Length）
- 表示要操作的元素數量，從第 0 個元素開始
- 最大值為：vlmax = (VLEN / sew) × lmul
- sew 小 → 可容納多元素；sew 大 → 元素變少
- 若演算法需處理相同元素數，需調整：
- 小 sew：**不使用整個暫存器**
- 大 sew：**用多組暫存器組成 vector group**
##### 🔁 Vector Operation 執行
- 所有向量指令會根據 `vl` 與 `vtype` 隱式控制執行
- 若 `vl < vlmax`，其餘未處理者稱為 **tail elements**
- tail element 策略：
- `tail undisturbed`：保留原值
- `tail agnostic`：保留或設為全 1（不確定）
##### 🎭 Masking（遮罩）
- 使用 `v0` 作為遮罩向量（每元素為 1-bit）
- 控制哪些元素要執行、哪些略過
- 未執行元素的行為：
- `mask undisturbed`：不變
- `mask agnostic`：不變或設為全 1（不保證）
##### ⚙ 設定 `vl` 和 `vtype`
可透過以下指令設定：
```asm
vsetvli   rd, rs, eN, mX, tP, mP
vsetivli  rd, imm, eN, mX, tP, mP
```
##### 範例試題
```c
void add_ref(int N, double *c, double *a, double *b) {
    for (int i = 0; i < N; i++)
        c[i] = a[i] + b[i];
}
```
組合語言解釋:
- `vsetvli`：設定 vector 長度與元素格式（SEW = 64bit）
- `vle64.v`：載入 64-bit 浮點數向量
- `vfadd.vv`：向量加法
- `vse64.v`：向量儲存
```asm
.LBB0_4: # %vector.body
    slli a7, a4, 3
    add a6, a2, a7
    sub a5, a0, a4
    vsetvli t0, a5, e64, m1, ta, mu
    vle64.v v8, (a6)
    add a5, a3, a7
    vle64.v v9, (a5)
    vfadd.vv v8, v8, v9
    add a4, a4, t0
    vse64.v v8, (a5)
    bne a4, a0, .LBB0_4
```
#### Q3
- **Q3_1 (16%)**
- 說明
使用 RISC-V RV64G ISA（無 V-extension）撰寫，完成兩個 float 陣列的相乘與總乘積的計算。
    - 對兩個陣列進行逐元素相乘
    - 所有乘積連乘成一個最終結果
    - 用到的暫存器:
    - 修改的檔案: 
    - example  若有兩個陣列：
h[4] = {0.1, 0.2, 0.3, 0.4} 和 x[4] = {0.2, 0.3, 0.4, 0.5}，
則結果為：
result = 0.1 * 0.2 * 0.2 * 0.3 * 0.3 * 0.4 * 0.4 * 0.5 = 0.0000288
- 輸入  
*arraymul_input2.txt* ，每個值為float，精確到小數點後六位。數值範圍：*0.0~10.0*，共會讀入 兩組陣列 h[] 與 x[]，每組長度為 arr_size，arr_size 的取值為：*2~16*，且必須是 2 的次方。
- 輸出:
相乘成果
**資料為single floatingpoint (float)**
```C
//C
for (int i = 0; i < arr_size; i++){
single_floating_result = single_floating_result * p_h[i] * p_x[i];
}
//in assembly
//初始化
//迴圈
//讀值
//計算
//移動
//判斷迴圈
//存回
```
- **Q3_2**(24%)
重新撰寫 Exercise 3-1 的組合語程式碼，但這次使用 double 浮點數（double） 資料型態。
    - 對兩個陣列進行逐元素相乘
    - 所有乘積連乘成一個最終結果
    - 用到的暫存器:
    - 修改的檔案: 
    - example  若有兩個陣列：
h[4] = {7.22, 1.0, 5.08, 7.3} 和 x[4] = {8.48, 7.58, 3.61, 6.74}，
則結果為：
result = 418751.066664454585407
- 輸入  
*arraymul_input2.txt* ，每個值為float，精確到小數點後六位。數值範圍：*0.0~10.0*，共會讀入 兩組陣列 h[] 與 x[]，每組長度為 arr_size，arr_size 的取值為：*2~16*，且必須是 2 的次方。
- 輸出:
相乘成果
**資料為 double floating-point (double)**
```C
//C
for (int i = 0; i < arr_size; i++){
double_floating_result = double_floating_result * p_h[i] * p_x[i];
}
//in assembly
//初始化
//迴圈
//讀值
//計算
//移動
//判斷迴圈
//存回
```
## 結論及心得
大家好，我是MQ。2025/5/10寫完了計算機組織HW2，真爽。  
繼上次期中考被電慘，只考25.5分後，整個信心大減，完全就是很不想碰計組，也一度想退選。而這次的作業也不意外的有夠難。  
這次沒什麼心得，慢慢寫、問GPT、問同學，整體來說搞懂後就寫滿快的，也越來越懂如何跟GPT配合寫作業。上次拿了滿分，希望這次也是吧。  
關於資工，我還在摸索，但我相信不久之後就會有答案了。  

"在名為年輕的那個平面，他還在尋找他的極限。" 5/11 by MQ
