# ASSIGNMENT 3

---

## A3-Q1: 5G NR Numerology

> **Q.** Explain the concept of 5G NR numerology and derive the relationship between subcarrier spacing and numerology index: Δf = 2^μ × 15 kHz. For μ = 0, 1, 2, 3, 4, calculate the corresponding subcarrier spacing and useful OFDM symbol duration. Explain why higher SCS is preferred for FR2 mmWave deployments. **(5 marks)**

**📌 The examiner wants four things:** (1) concept, (2) derivation, (3) a table for μ = 0…4, (4) the FR2 reasoning.

### 1. Concept of numerology [P2-S3 to S9]

- **Numerology in 5G NR refers to the Sub-Carrier Spacing (SCS)**, together with the OFDM symbol duration and cyclic prefix (CP) that go with it.
- **LTE** used a single numerology: **15 kHz SCS** with a **CP ≈ 4.7 μs**. This was a good balance for what LTE was designed for: outdoor cellular deployments up to about 3 GHz.
- **NR** must support a much wider range of deployments, from **large cells below 1 GHz** up to **mm-wave cells with very wide bandwidths**. A **single numerology for all of these scenarios is neither efficient nor even possible.**
  - **Low carrier frequencies** (below 1 GHz up to a few GHz): cells can be large, so the delay spread is a couple of microseconds. The CP must be long enough to absorb it, which needs an SCS of **15–30 kHz** (LTE-like).
  - **High carrier frequencies** (towards mm-wave): **phase noise** becomes critical, which calls for a **higher SCS**. Cells are smaller because propagation is harder, and heavy **beamforming** reduces the delay spread, so a **shorter CP** is acceptable.
- Conclusion: NR needs a **scalable (flexible) numerology**, obtained by scaling a **baseline SCS of 15 kHz**.

**Why 15 kHz as the baseline? [P2-S5 to S7]**
- It allows **coexistence with LTE and LTE-based NB-IoT / eMTC on the same carrier.**
- MTC (machine-type) devices have long replacement cycles of 10 years or more. Without coexistence, an operator could not migrate a carrier to NR until every MTC device had been replaced.
- **Gradual migration:** with limited spectrum, an operator may have to share a single carrier between LTE and NR in the time domain.

### 2. Derivation of Δf = 2^μ × 15 kHz

**Step 1: OFDM orthogonality.** Subcarriers spaced Δf apart are orthogonal over the useful symbol time T_u when

$$T_u = \frac{1}{\Delta f}$$

You can check this against the notes' table [P2-S7]: 1/15 kHz = 66.7 μs ✓.
The same relation follows from the sampling numbers in [P2-S15]. With FFT size N = 4096 and sampling rate f_s = 122.88 MHz:

$$\Delta f = \frac{f_s}{N} = \frac{122.88\ \text{MHz}}{4096} = 30\ \text{kHz}, \qquad T_u = \frac{N}{f_s} = \frac{4096}{122.88\ \text{MHz}} = 33.33\ \mu s$$

**Step 2: Fix a baseline.** Δf₀ = 15 kHz, for LTE / NB-IoT coexistence.

**Step 3: Scale the baseline.** Write Δf = k × 15 kHz. Two facts from the frame structure [P2-S12, S13] fix k:
- The **1 ms subframe is a numerology-independent time reference**: it must stay 1 ms for every SCS.
- A **slot always contains 14 OFDM symbols.**

Since T_u ∝ 1/Δf, scaling the SCS by k shrinks every symbol, and therefore every slot, by k:

$$T_{slot} = \frac{1\ \text{ms}}{k}$$

For slots of every numerology to fit **exactly** into the 1 ms subframe and **nest inside each other** (one 30 kHz slot fills exactly half of a 15 kHz slot, and so on), k must be a **power of 2**: k = 1, 2, 4, 8, 16…

Setting k = 2^μ with μ = 0, 1, 2, 3, 4 gives:

$$\boxed{\ \Delta f = 2^{\mu}\times 15\ \text{kHz}\ } \qquad T_u = \frac{1}{2^{\mu}\cdot 15\ \text{kHz}} = \frac{66.67}{2^{\mu}}\ \mu s \qquad T_{CP}\approx\frac{4.7}{2^{\mu}}\ \mu s \qquad T_{slot}=\frac{1}{2^{\mu}}\ \text{ms}$$

So **every +1 in μ doubles the SCS and halves the symbol duration, the CP and the slot.**

### 3. Calculation for μ = 0 … 4

| μ   | Δf = 2^μ × 15 kHz     | Useful symbol T_u = 1/Δf  | CP T_CP [P2-S7] |  
| --- | --------------------- | ------------------------- | --------------- | 
| 0   | 2⁰ × 15 = **15 kHz**  | 1/(15×10³) = **66.67 μs** | 4.7 μs          |   
| 1   | 2¹ × 15 = **30 kHz**  | 1/(30×10³) = **33.33 μs** | 2.3 μs          |   
| 2   | 2² × 15 = **60 kHz**  | 1/(60×10³) = **16.67 μs** | 1.2 μs          |   
| 3   | 2³ × 15 = **120 kHz** | 1/(120×10³) = **8.33 μs** | 0.59 μs         |    
| 4   | 2⁴ × 15 = **240 kHz** | 1/(240×10³) = **4.17 μs** | 0.29 μs         |             |

**Cross-check:** at μ = 0, 14 × 71.4 μs ≈ 1 ms, which is one slot ✓. At μ = 1, CP = 288 samples / 122.88 MHz = 2.34 μs ≈ 2.3 μs ✓ [P2-S15].

### 4. Why a higher SCS is preferred for FR2 (mm-wave) [P2-S4, S5, S7, S9]

|             | **FR1**          | **FR2**                 |
| ----------- | ---------------- | ----------------------- |
| Carrier     | Sub-6 GHz        | mm-wave, 24.25–52.6 GHz |
| Bandwidth   | up to 100 MHz    | up to 400 MHz           |
| Cell size   | Relatively large | Small (hostile channel) |
| CP needed   | A couple of μs   | Short                   |
| Typical SCS | 15–30 kHz        | Higher (e.g., 120 kHz)  |

Reasons:
1. **Phase noise:** at mm-wave frequencies, oscillator **phase noise is much higher**. A larger SCS spaces the subcarriers further apart, which makes the signal more robust to it. The notes say it directly: *"FR2 requires higher SCS due to higher phase noise."*
2. **Small cells mean small delay spread.** FR2 propagation is hostile, so cells are small and multipath delays are short. The short CP that comes with a high SCS (0.59 μs at 120 kHz) is still enough.
3. **Beamforming** is used extensively at high frequencies, and it **further reduces the delay spread**, so the long CP of a low SCS is unnecessary overhead.
4. **Very wide bandwidth (400 MHz)** (derived from [P2-S15, S16]). A 100 MHz FR1 carrier with 30 kHz SCS uses 3300 subcarriers (275 PRBs). A 400 MHz FR2 carrier with **120 kHz** SCS also needs only about 3300 subcarriers (3300 × 120 kHz ≈ 396 MHz), so the FFT size stays practical. With 15 kHz it would need about 26 400 subcarriers.
5. **Shorter slots:** with 120 kHz the slot is 0.125 ms instead of 1 ms, so scheduling is faster and latency lower (derived from [P2-S11 to S14]).

Conversely, **FR1 keeps 15–30 kHz** because its large cells need a CP of a few μs. A high SCS would make the CP too short and cause inter-symbol interference.

> ✅ **Final answer:** Δf = 2^μ × 15 kHz, T_u = 1/Δf. μ = 0, 1, 2, 3, 4 gives 15, 30, 60, 120, 240 kHz and T_u = 66.67, 33.33, 16.67, 8.33, 4.17 μs. FR2 uses a higher SCS because of higher phase noise, small cells with small delay spread (a short CP is enough), beamforming, and very wide bandwidths.

**✍️ Exam tips:** Write T_u = 1/Δf before the table. Draw the table neatly with a CP column; that column comes straight from the notes, and including it earns marks. For FR2, give at least **3 numbered reasons**, and put "phase noise" first.

---

## A3-Q2: 5G NR Frame Structure

> **Q.** Explain the 5G NR frame structure with a neat diagram. A radio frame has a duration of 10 ms and consists of 10 subframes. For SCS values of 15 kHz, 30 kHz, 60 kHz and 120 kHz, determine the slot duration and explain how the number of slots per 1-ms subframe changes with numerology. **(5 marks)**

**📌 The examiner wants:** the hierarchy (frame → subframe → slot → symbol), a diagram, slot durations for 4 SCS values, and the "slots double with μ" explanation.

### 1. Time-domain hierarchy [P2-S11 to S14]

| Unit            | Duration                | Key fact                                                                                                        |
| --------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Radio frame** | **10 ms**               | NR transmissions are organized into frames                                                                      |
| **Subframe**    | **1 ms** (10 per frame) | **Numerology-independent time reference**: always 1 ms                                                          |
| **Slot**        | **1/2^μ ms**            | Always **14 OFDM symbols**; duration depends on numerology; **the slot is the typical dynamic scheduling unit** |
| **OFDM symbol** | T_u + T_CP              | T_u = 1/Δf                                                                                                      |

### 2. Neat diagram

```
                         ONE RADIO FRAME  (T_frame = 10 ms)
  +-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+
  | #0  | #1  | #2  | #3  | #4  | #5  | #6  | #7  | #8  | #9  |   10 subframes
  +-----+-----+-----+-----+-----+-----+-----+-----+-----+-----+
  |<--->|
  one subframe = 1 ms   (zoomed below)

                 0 ms                                                          1 ms
                 |<----- one subframe = 1 ms (fixed for all numerologies) ----->|
 mu=0   15 kHz : [=============================1 ms=============================]   1 slot  x 1 ms
 mu=1   30 kHz : [============0.5 ms============][============0.5 ms============]   2 slots x 0.5 ms
 mu=2   60 kHz : [====0.25ms====][====0.25ms====][====0.25ms====][====0.25ms====]   4 slots x 0.25 ms
 mu=3  120 kHz : [=.125=][=.125=][=.125=][=.125=][=.125=][=.125=][=.125=][=.125=]   8 slots x 0.125 ms
 mu=4  240 kHz : [==][==][==][==][==][==][==][==][==][==][==][==][==][==][==][==]  16 slots x 0.0625 ms

 Every slot [====] above = 14 OFDM symbols:
 +--+------+--+------+--+------+--     --+--+------+--+------+
 |CP| sym0 |CP| sym1 |CP| sym2 |   ...   |CP| sym12|CP| sym13|
 +--+------+--+------+--+------+--     --+--+------+--+------+
```

![alt text](image.png)

### 3. Formulas

$$\mu=\log_2\!\left(\frac{\Delta f}{15\ \text{kHz}}\right),\qquad \text{slots per subframe}=2^{\mu},\qquad T_{slot}=\frac{1\ \text{ms}}{2^{\mu}},\qquad \text{slots per frame}=10\times 2^{\mu}$$

$$\text{OFDM symbols per subframe} = 14\times 2^{\mu}$$

### 4. Calculation

| SCS       | μ   | Slot duration = 1 ms / 2^μ | Slots per 1 ms subframe | Slots per 10 ms frame | Symbols per subframe |
| --------- | --- | -------------------------- | ----------------------- | --------------------- | -------------------- |
| 15 kHz    | 0   | 1/1 = **1 ms**             | **1**                   | 10                    | 14                   |
| 30 kHz    | 1   | 1/2 = **0.5 ms**           | **2**                   | 20                    | 28                   |
| 60 kHz    | 2   | 1/4 = **0.25 ms**          | **4**                   | 40                    | 56                   |
| 120 kHz   | 3   | 1/8 = **0.125 ms**         | **8**                   | 80                    | 112                  |
| (240 kHz) | 4   | 1/16 = 0.0625 ms           | 16                      | 160                   | 224                  |

### 5. How the number of slots per subframe changes

- When μ increases by 1, the **SCS doubles**, so the **OFDM symbol duration halves** (T_u = 1/Δf).
- A slot always holds **14 symbols**, so the **slot duration also halves**.
- The **subframe stays fixed at 1 ms**, so the **number of slots per subframe doubles**: 1 → 2 → 4 → 8 → 16 (= 2^μ).
- Consequence: higher numerologies give **shorter scheduling units**, so scheduling decisions are faster and latency is lower. This suits FR2 / low-latency use.
- Because the subframe and frame boundaries are the same for all numerologies, different numerologies stay **time-aligned**.


> ✅ **Final answer:** Frame = 10 ms = 10 subframes of 1 ms. Slot = 14 OFDM symbols. Slot duration is 1 ms, 0.5 ms, 0.25 ms and 0.125 ms for 15, 30, 60 and 120 kHz. Slots per subframe = 2^μ = 1, 2, 4, 8, doubling with every step of μ while the subframe stays 1 ms.

---

## A3-Q3: Max-Rate Scheduler Numerical + Comparison

> **Q.** Three users U1, U2, U3 compete for the same PRB with instantaneous data rates in a particular slot R1 = 8 Mbps, R2 = 12 Mbps, R3 = 10 Mbps. Determine which user will be selected by the Max-Rate scheduler. Explain why Max-Rate scheduling can be unfair. Compare Max-Rate, Round-Robin and PF scheduling in terms of throughput and fairness. **(5 marks)**

### 1. Background [P2-S17, S18]

- Cellular systems have a **centralized architecture**: the **BS makes all the decisions** and must serve **multiple users**.
- Each user's channel fades **independently** over time. In each slot m, user i can support an **instantaneous rate R_i[m]** that depends on its channel at that moment.

### 2. Solution: who does Max-Rate pick?

Max-Rate rule [P2-S18]:

$$k=\arg\max_{i}\ R_i[m]$$

Substituting the values:

$$k = \arg\max\{R_1, R_2, R_3\} = \arg\max\{8,\ 12,\ 10\}\ \text{Mbps} = 2$$

> ✅ **The Max-Rate scheduler selects user U2** (12 Mbps), the highest instantaneous rate. The PRB carries **12 Mbps** in this slot, the maximum possible.

### 3. Why Max-Rate can be unfair [P2-S19]

- It is **greedy**: it **always chooses the user with the highest rate** and ignores how much service the other users have already received.
- A user with a **persistently good channel** (for example, close to the BS) wins almost every slot.
- A user with a **persistently weak channel** (for example, at the cell edge) may **never be scheduled**. This is called **starvation**: zero throughput for that user, even though the cell's total throughput is maximized.
- In our slot, U1 and U3 get nothing. If U2's channel stays best in later slots, U1 and U3 **keep getting nothing**.

```
 Channel quality
     ^       U2 (near BS) stays on top ---> always scheduled
     |   ..--''--..       ..--''--..
     |  /          \.._../          \..
     |      U3 (sometimes close)
     |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     |      U1 (cell edge, weak)
     |   _____________________________   ---> rarely / never scheduled = STARVATION
     +-------------------------------------------> time (slots)
```
![alt text](image-1.png)

### 4. What the other two schedulers would do in the same slot

- **Round-Robin [P2-S20]:** picks whoever's **turn** it is, ignoring the rates. If it is U1's turn, U1 is served at only 8 Mbps even though 12 Mbps was available.
- **Proportional Fair [P2-S21]:** picks the user with the largest **R_i[m] / R̄_i[m]**, where R̄_i is the user's average rate. The question does not give averages, so here is an *illustration with assumed values*. If R̄1 = 2, R̄2 = 10, R̄3 = 8 Mbps:

$$\frac{R_1}{\bar R_1}=\frac{8}{2}=4,\quad \frac{R_2}{\bar R_2}=\frac{12}{10}=1.2,\quad \frac{R_3}{\bar R_3}=\frac{10}{8}=1.25\ \Rightarrow\ \text{PF picks } U_1$$

  U1 is chosen even though its absolute rate is the lowest, because it is at a **relative peak** (4× its usual rate). This is "relatively best channel conditions" [P2-S21].

### 5. Comparison: throughput and fairness

| Criterion           | **Max-Rate**                                  | **Round-Robin**                                                       | **Proportional Fair**                                     |
| ------------------- | --------------------------------------------- | --------------------------------------------------------------------- | --------------------------------------------------------- |
| Selection rule      | k = arg max R_i[m]                            | Users served in turn                                                  | k = arg max R_i[m] / R̄_i[m]                               |
| Uses channel state? | Yes (absolute rate)                           | **No**                                                                | Yes (rate relative to own average)                        |
| **Cell throughput** | **Highest** (always best user)                | **Lowest** (often serves users in a fade, "degrades the performance") | **High**, close to Max-Rate                               |
| **Fairness**        | **Poorest**: greedy, starvation of weak users | **Fair in time**: every user gets equal turns                         | **Good**: every user gets served, at its own good moments |
| Complexity          | Low                                           | **Simplest** to design                                                | Higher (needs rates + running averages)                   |
| Starvation?         | Yes                                           | No                                                                    | No                                                        |

**One-line summary:** Max-Rate maximizes throughput but sacrifices fairness. Round-Robin is fair but wastes good channels. **PF balances both**, and that balance is why practical systems use PF-type schedulers. *A detailed multi-slot numerical comparison is in A3-Q6.*

**✍️ Exam tips:** Write the formula, then substitute to get k = 2, and box "U2". Draw a small channel-quality sketch for the unfairness part. Draw the comparison table with at least 4 rows (rule, channel-aware, throughput, fairness).

---

## A3-Q4: CRC Generation and Validation

> **Q.** Consider a 6-bit transport block 110101 and generator polynomial G(D) = D² + 1. Represent G(D) in binary form. Generate the CRC by modulo-2 division. Append the CRC to the original transport block. Show how the receiver validates the received block using the same generator polynomial. **(5 marks)**

### 1. Theory [P2-S31, S32]

- The PHY layer attaches a **24-bit (or 16-bit) CRC** to every transport block.
- **CRC only detects errors; it does not correct them.** That detection is exactly what **HARQ** needs: if the CRC fails, a retransmission is requested.
- An **n-bit CRC** typically detects **any single error burst of length ≤ n bits.**
- **Algorithm:**
  1. Treat the TB as a binary number and **append r zeros**, where r = degree of G(D) = CRC length.
  2. **Divide modulo-2** by the generator polynomial. Modulo-2 means XOR: no carries, no borrows.
  3. The **remainder is the CRC checksum**; append it to the TB.
  4. The **receiver divides the received block (TB + CRC) by the same polynomial.** A remainder of 0 means **no error**; a non-zero remainder means **an error occurred.**

Modulo-2 rules: 0⊕0 = 0, 0⊕1 = 1, 1⊕0 = 1, 1⊕1 = 0. Subtraction is the same as addition.

### 2. Step 1: generator polynomial in binary

$$G(D) = 1\cdot D^2 + 0\cdot D^1 + 1\cdot D^0 \;\Longrightarrow\; \boxed{1\ 0\ 1}$$

Degree = 2, so the **CRC length is 2 bits**.

### 3. Step 2: append 2 zeros

$$110101 \;\xrightarrow{\ \times D^2\ }\; 110101\,\underline{00} = 11010100$$

### 4. Step 3: modulo-2 long division (transmitter)

```
                1 1 1 0 1 1     <- quotient (not needed for CRC)
            ________________
   1 0 1 )  1 1 0 1 0 1 0 0
            1 0 1               XOR 101  (leading bit 1 -> quotient bit 1)
            -----
              1 1 1             <- bring down next bit
              1 0 1             XOR 101  (leading bit 1 -> quotient bit 1)
              -----
                1 0 0           <- bring down next bit
                1 0 1           XOR 101  (leading bit 1 -> quotient bit 1)
                -----
                  0 1 1         <- bring down next bit
                  0 0 0         XOR 000  (leading bit 0 -> quotient bit 0)
                  -----
                    1 1 0       <- bring down next bit
                    1 0 1       XOR 101  (leading bit 1 -> quotient bit 1)
                    -----
                      1 1 0     <- bring down next bit
                      1 0 1     XOR 101  (leading bit 1 -> quotient bit 1)
                      -----
                        1 1     <- REMAINDER = CRC = 1 1
```

The same steps as a table:

| Step | Current 3 bits | Leading bit | XOR with | Result | Keep last 2 bits + bring down |
| ---- | -------------- | ----------- | -------- | ------ | ----------------------------- |
| 1    | 110            | 1           | 101      | 011    | 11 + **1** → 111              |
| 2    | 111            | 1           | 101      | 010    | 10 + **0** → 100              |
| 3    | 100            | 1           | 101      | 001    | 01 + **1** → 011              |
| 4    | 011            | 0           | 000      | 011    | 11 + **0** → 110              |
| 5    | 110            | 1           | 101      | 011    | 11 + **0** → 110              |
| 6    | 110            | 1           | 101      | 011    | **remainder = 11**            |

> **CRC = 1 1** ✅ (same as [P2-S33]). The notes skip step 4 by bringing down two bits at once; the remainder is identical, and the quotient has no role in CRC.

### 5. Step 4: append the CRC

$$\text{Transmitted block} = \underbrace{1\,1\,0\,1\,0\,1}_{\text{TB}}\ \underbrace{1\,1}_{\text{CRC}} = 11010111$$

### 6. Step 5: validation at the receiver [P2-S34]

The receiver divides the **whole received block 11010111** by the **same 101**:

```
                1 1 1 0 1 1     <- quotient (not needed for CRC)
            ________________
   1 0 1 )  1 1 0 1 0 1 1 1
            1 0 1               XOR 101  (leading bit 1 -> quotient bit 1)
            -----
              1 1 1             <- bring down next bit
              1 0 1             XOR 101  (leading bit 1 -> quotient bit 1)
              -----
                1 0 0           <- bring down next bit
                1 0 1           XOR 101  (leading bit 1 -> quotient bit 1)
                -----
                  0 1 1         <- bring down next bit
                  0 0 0         XOR 000  (leading bit 0 -> quotient bit 0)
                  -----
                    1 1 1       <- bring down next bit
                    1 0 1       XOR 101  (leading bit 1 -> quotient bit 1)
                    -----
                      1 0 1     <- bring down next bit
                      1 0 1     XOR 101  (leading bit 1 -> quotient bit 1)
                      -----
                        0 0     <- REMAINDER = 0 0  -> NO ERROR
```

**Remainder = 00, so the checksum is zero and the transmission is successful.** ✅

**What if there is an error?** Suppose the channel flips the 5th bit, so the receiver gets 1101**1**111:

```
                1 1 1 0 0 1     <- quotient (not needed for CRC)
            ________________
   1 0 1 )  1 1 0 1 1 1 1 1
            1 0 1               XOR 101  (leading bit 1 -> quotient bit 1)
            -----
              1 1 1             <- bring down next bit
              1 0 1             XOR 101  (leading bit 1 -> quotient bit 1)
              -----
                1 0 1           <- bring down next bit
                1 0 1           XOR 101  (leading bit 1 -> quotient bit 1)
                -----
                  0 0 1         <- bring down next bit
                  0 0 0         XOR 000  (leading bit 0 -> quotient bit 0)
                  -----
                    0 1 1       <- bring down next bit
                    0 0 0       XOR 000  (leading bit 0 -> quotient bit 0)
                    -----
                      1 1 1     <- bring down next bit
                      1 0 1     XOR 101  (leading bit 1 -> quotient bit 1)
                      -----
                        1 0     <- REMAINDER = 1 0 (non-zero) -> ERROR
```

**Remainder = 10 ≠ 0, so an error is detected.** The receiver cannot correct it; it asks for retransmission (HARQ).

> ✅ **Final answer:** G(D) = D² + 1 = **101**. CRC = **11**. Transmitted block = **11010111**. The receiver divides 11010111 by 101 and gets remainder **00**, so there is no error. A non-zero remainder would mean an error, followed by a HARQ retransmission.

**✍️ Exam tips:** Show the long division neatly, with XOR at each step. Write "remainder = CRC" and "degree of G = number of zeros appended". Always show the receiver division too, which carries half the marks. Add the error example and the polynomial form if time allows.

---

## A3-Q5: Transport Block Segmentation (B = 12 000)

> **Q.** A transport block has length B = 12 000 bits after adding the 24-bit TB-CRC. Assume the maximum LDPC code-block size K_cb = 8448. Determine whether segmentation is required. If required, calculate the minimum number of code blocks needed and explain why multiple LDPC code blocks are used instead of processing the entire TB as one block. **(5 marks)**

### 1. Theory [P2-S38, S39], [P3-S40, S42]

- Segmentation is **done after TB-CRC insertion.**
- **K_cb** is the maximum input block size the 5G LDPC encoder can process: **8448** (BG1) or **3840** (BG2).
- A TB can be much larger than K_cb. The maximum TB is **319 784 bits** (MCS 27, 275 RBs).
- **Rule:** segment if

$$B = \text{TB length} + 24\text{-bit TB-CRC} > K_{cb}$$

- **Number of code blocks** [P3-S42]:

$$\text{if } B > K_{cb}:\quad L = 24,\quad C = \left\lceil \frac{B}{K_{cb}-L} \right\rceil,\quad B' = B + C\cdot L$$
$$\text{if } B \le K_{cb}:\quad L = 0,\quad C = 1,\quad B' = B$$

- **Why K_cb − L?** When C > 1, **each** code block carries its own **24-bit CB-CRC**, so only **K_cb − 24** bits of each block are left for TB bits.
- B′ is the **effective payload length** (TB + all CB-CRCs). Each code block then gets

$$K' = \left\lceil \frac{B'}{C}\right\rceil$$

### 2. Calculation

**Step 1: is segmentation required?**

$$B = 12\,000 > K_{cb} = 8448 \;\Rightarrow\; \textbf{YES, segmentation is required.}$$

**Step 2: minimum number of code blocks**

$$C = \left\lceil \frac{12\,000}{8448-24} \right\rceil = \left\lceil \frac{12\,000}{8424} \right\rceil = \lceil 1.4245 \rceil = \boxed{2}$$

Check that 2 is the minimum. With C = 1, one block holds at most 8424 TB bits, and 8424 < 12 000 ✗. With C = 2, the blocks hold 2 × 8424 = 16 848 ≥ 12 000 ✓.

**Step 3: size of each code block**

$$B' = B + C\cdot L = 12\,000 + 2\times 24 = 12\,048\ \text{bits}$$
$$K' = \frac{B'}{C} = \frac{12\,048}{2} = 6024\ \text{bits per code block}$$


> ✅ **Final answer:** Since B = 12 000 > 8448, segmentation **is required**. The **minimum number of code blocks is C = 2**, each of K′ = 6024 bits (including its 24-bit CB-CRC). Going further: Z_c = 288, K = 6336, F = 312 filler bits.

### 3. Why multiple LDPC code blocks instead of one huge block? [P2-S38], [P3-S40, S41]

Why not simply design the LDPC encoder for the largest TB (319 784 bits)?
1. **Decoding complexity grows with code-block length.** A single huge LDPC decoder would be very complex.
2. **Parallel processing gives lower latency.** With C smaller blocks, **multiple LDPC encoders/decoders run in parallel**, which reduces encoding and decoding time. This matters for 5G low latency.
3. **Finer error detection.** Each code block carries its own **CB-CRC**, so errors are detected **per code block** and only that block's retransmission can be requested, not the whole TB.
4. **Multiple levels of error check.** The TB-CRC is still kept, using a different polynomial from the CB-CRCs, so it catches any **residual error** missed at CB level.
5. **Equal-sized blocks give consistent performance.** The segmentation block divides the TB into **equal-size CBs**. Unequal blocks (the wrong way: 8448 + 3552) would hurt, because a **larger block decodes at a lower SNR and performance is limited by the smaller block** [P3-S41].

**✍️ Exam tips:** Write the condition B > K_cb, then the C formula, and **explain why it is K_cb − 24**. Show the C = 1 ✗ / C = 2 ✓ check, since it proves "minimum". Give at least 3 reasons for multiple CBs, with complexity and parallelism first.

---

## A3-Q6: Multi-user Scheduling Algorithms

> **Q.** Explain the different types of multi-user scheduling algorithms used in 5G NR. Discuss the working principle, advantages and limitations of Max-Rate, Round-Robin and Proportional Fair (PF) scheduling. **(5 marks)**

### 1. Why scheduling? [P2-S17, S18, S23]

- A cellular system has a **centralized architecture**: the **BS (gNB) makes all scheduling decisions** and must share radio resources among **multiple users**.
- In the downlink, several users compete for the **same PRB** in every slot. Each user's channel quality varies **with time and with frequency** (fading).
- **5G NR multiplexes users in both frequency and time** [P2-S23]. Each time-frequency chunk (PRB × slot) can be given to a different user.

```
 frequency (PRBs)
     ^
     |  +----+----+----+----+----+----+
     |  | U2 | U2 | U1 | U3 | U1 | U2 |   each box = 1 PRB in 1 slot,
     |  +----+----+----+----+----+----+   given to the user chosen by
     |  | U1 | U3 | U1 | U2 | U2 | U3 |   the scheduler for that box
     |  +----+----+----+----+----+----+
     |  | U3 | U1 | U2 | U2 | U3 | U1 |
     |  +----+----+----+----+----+----+
     +-----------------------------------> time (slots)
```

- **Channel-dependent scheduling:** the BS looks at each user's instantaneous achievable rate R_i[m] in slot m. Users fade independently, so at any moment *some* user is likely to have a good channel. The figure in [P2-S18] shows the **"effective channel variation seen by the BS"**: the upper envelope of all users' channels.

**Types (classification):**

| Type                                       | Uses channel state? | Algorithm             |
| ------------------------------------------ | ------------------- | --------------------- |
| Channel-**independent**                    | No                  | **Round-Robin**       |
| Channel-**dependent**, throughput-oriented | Yes (absolute rate) | **Max-Rate**          |
| Channel-**dependent**, fairness-aware      | Yes (relative rate) | **Proportional Fair** |

### 2. Max-Rate scheduler [P2-S18, S19]

**Working principle:** in each slot m, give the PRB to the user with the **highest instantaneous rate**:

$$k=\arg\max_{i}\ R_i[m]$$

```
 rate ^   U1 ___          ___
      |      /   \  U2  /     \           Max-Rate always follows the TOP curve:
      |  ___/     \____/       \___       slot:  1   2   3   4   5   6
      |  U2   \   /    \   /              pick : U1  U1  U2  U2  U1  U1 ...
      |        \_/      \_/               a user that is never on top is never served
      +-------------------------------> time
```

| Advantages                                                                                  | Limitations                                                                 |
| ------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Maximum cell throughput** / spectral efficiency: every PRB carries the best possible rate | **Greedy and unfair**: always chooses the user with the highest rate        |
| Exploits channel variations fully (rides the peaks)                                         | **Starvation**: users with poor channels (cell edge) may never be scheduled |
| Simple decision rule                                                                        | No QoS / latency guarantee for weak users                                   |

### 3. Round-Robin scheduler [P2-S20]

**Working principle:** serve the users **in turn** (U1 → U2 → U3 → U1 …), **without considering channel conditions.** In slot m with K users, the user is chosen by rotation (e.g., user (m mod K) + 1).

```
 rate ^   U1 ___          ___
      |      /   \  U2  /     \           Round-Robin ignores the curves:
      |  ___/     \____/       \___       slot:  1   2   3   4   5   6
      |  U2   \   /    \   /              pick : U1  U2  U1  U2  U1  U2
      |        \_/      \_/               (even if the chosen user is in a deep fade)
      +-------------------------------> time
```

| Advantages                                                | Limitations                                                             |
| --------------------------------------------------------- | ----------------------------------------------------------------------- |
| **Simplest to design**; no channel information needed     | **Degrades performance**: often serves a user in a deep fade            |
| **Fair in time**: every user gets an equal share of slots | **Lowest throughput**; wastes good channel opportunities                |
| No starvation                                             | "Equal time" is not "equal rate": a weak user still gets low throughput |

### 4. Proportional Fair (PF) scheduler [P2-S21, S22]

**Working principle:** give the resource to the user with the **relatively best channel**, meaning best **compared with its own average**:

$$k = \arg\max_{i}\ \frac{R_i[m]}{\bar R_i[m]}$$

The averages are updated after every slot over a **fairness window t_c**:

$$\bar R_i[m+1] = \begin{cases}\left(1-\dfrac{1}{t_c}\right)\bar R_i[m] + \dfrac{1}{t_c}R_i[m], & \text{if } k = i \ (\text{user was served})\\[2mm] \left(1-\dfrac{1}{t_c}\right)\bar R_i[m], & \text{if } k \ne i \ (\text{not served})\end{cases}$$

- **R_i[m]** is the instantaneous rate of user i in slot m.
- **R̄_i[m]** is the average rate of user i over the fairness window t_c.

**Why this is fair (intuition):**
- A user who is **not served** has its average **decay** by (1 − 1/t_c) every slot. Its ratio R_i / R̄_i therefore **keeps growing** until it wins a slot. **No user starves.**
- A user who has just been served sees its average rise, so its ratio falls and it gives way to others.
- Each user tends to be served when **its own channel is at a peak** relative to its normal level. Throughput stays high while fairness is maintained.
- t_c controls the trade-off. A small t_c means averages react quickly (fairness over short intervals). A large t_c means averages change slowly (fairness over longer intervals).

| Advantages                                  | Limitations                                                         |
| ------------------------------------------- | ------------------------------------------------------------------- |
| **Good balance of throughput and fairness** | More **complex**: needs the rate of every user and running averages |
| Exploits channel peaks of **every** user    | Throughput **slightly below Max-Rate**                              |
| No starvation; cell-edge users get served   | Does not guarantee equal *rates*, only proportional fairness        |
| Tunable via t_c                             | Performance depends on choosing t_c                                 |


### 6. Summary comparison

|               | Max-Rate                | Round-Robin       | Proportional Fair    |
| ------------- | ----------------------- | ----------------- | -------------------- |
| Rule          | arg max R_i             | in turn           | arg max R_i / R̄_i    |
| Channel-aware | ✔                       | ✘                 | ✔                    |
| Throughput    | Highest                 | Lowest            | High                 |
| Fairness      | Poor                    | Time-fair         | Good                 |
| Complexity    | Low                     | Lowest            | Moderate             |
| Typical use   | Theoretical upper bound | Simple / baseline | **Practical choice** |

**✍️ Exam tips:** For each algorithm write *principle, formula, sketch, advantages, limitations*. PF is the most important: write **both** update equations and **define t_c**. Finish with the comparison table.

---

# ASSIGNMENT 4

> **Running example used by the notes (and in A4-Q1, Q2, Q4):** a user is allocated **70 PRBs** over a slot of **14 OFDM symbols**, **MCS-16 (16-QAM)** with code rate **R = 658/1024 = 0.642**, and the MAC sends a **TB of 28 168 bits** [P3-S41, S53, S55].

---

## A4-Q1: Complete 5G NR Transmitter & Receiver Chain

> **Q.** Explain the complete 5G NR transmitter and receiver processing chain with a neat block diagram, from the transport block at the transmitter to the recovered transport block at the receiver. **(5 marks)**

### 1. MAC–PHY interface [P2-S27 to S29]

- The **MAC** passes two things to the **PHY**:
  - the **data payload = transport block (TB)**, carried on the **PDSCH** (Physical Downlink Shared Channel);
  - the **control information** (e.g., **MCS index, number of PRBs**), carried on the **PDCCH** (Physical Downlink Control Channel).
- The PHY must **code/decode, modulate/demodulate** and apply **multi-antenna processing** to the TB.

![alt text](image-2.png)

**Basic idea of the chain [P2-S30]:**

```
 Data --> [CRC calc] -1-> [LDPC coding] -2-> [Modulation] --> [AWGN channel y = x + n]
      --> [Demodulation] --> [LDPC decoding] -3-> [CRC validation] -4-> Decoded data

  1: data + CRC     2: data + CRC + parity     3: decoded data + CRC     4: decoded data
```

### 2. Complete block diagram [P3-S58, S60]

```
       TRANSMITTER (gNB)                       RECEIVER (UE)
          TB from MAC                            TB to MAC
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T1. TB-CRC attachment  |            | R9. TB-CRC validation  |  --> fail: HARQ
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T2. CB segmentation    |            | R8. CB concatenation   |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T3. CB-CRC attachment  |            | R7. CB-CRC validation  |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T4. LDPC encoding      |            | R6. LDPC decoding      |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T5. Rate matching      |            | R5. Rate recovery      |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T6. Interleaving       |            | R4. De-interleaving    |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T7. CB concatenation   |            | R3. CB segmentation    |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T8. Scrambling         |            | R2. Descrambling       |
  +------------------------+            +------------------------+
               |                                     ^
               v                                     |
  +------------------------+            +------------------------+
  | T9. Modulation         |--channel-->| R1. Demodulation       |
  |                        | y = x + n  |                        |
  +------------------------+            +------------------------+

  T3-T6 and R4-R7 run once PER CODE BLOCK: C parallel branches
  (drawn as stacked boxes in the notes).
  Each receiver block undoes the transmitter block on the same row.
```

Compact two-row version, the way the notes draw it (good to reproduce in the exam):

![alt text](image-3.png)

### 3. Transmitter: block by block (with the running example)

| #   | Block                                | What it does                                                                                               | Why                                                            | Running example                                                  |
| --- | ------------------------------------ | ---------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------------------- |
| T1  | **TB-CRC attachment** [P2-S31]       | Append a 24-bit CRC to the TB (modulo-2 division by a generator polynomial)                                | Error detection for HARQ                                       | 28 168 + 24 = **B = 28 192**                                     |
| T2  | **Code-block segmentation** [P2-S38] | If B > K_cb = 8448, split into C **equal** code blocks; add **filler bits** to match the LDPC lifting size | LDPC encoder size limit; lower complexity; parallel processing | C = ⌈28192/8424⌉ = **4** blocks                                  |
| T3  | **CB-CRC attachment** [P2-S39]       | If C > 1, append a 24-bit CB-CRC to every code block                                                       | Per-block error detection; early stop; retransmission          | K′ = 7072, K = **7744** (F = 672 filler)                         |
| T4  | **LDPC encoding** [P3-S50]           | Encode each block using BG1/BG2 and lifting size Z_c; codeword v with **Hv = 0**                           | Channel coding: adds parity for error *correction*             | Z_c = 352, N = 66 × 352 = **23 232** (rate 1/3)                  |
| T5  | **Rate matching** [P3-S54, S80]      | **Puncture / repeat** bits so each block fits its share of resources, E = G/C                              | Converts mother rate 1/3 to the desired rate                   | G = 70 × 162 × 4 = 45 360, **E = 11 340**                        |
| T6  | **Interleaving** [P3-S61]            | **Row-column interleaver**: write row-wise into M × E/M (M = modulation order), read column-wise           | Avoid burst errors                                             | 4 rows × 2835 columns per block                                  |
| T7  | **CB concatenation** [P3-S62]        | Join the C rate-matched blocks sequentially (when C > 1)                                                   | Form one bit stream for the slot                               | 4 × 11 340 = **45 360** bits                                     |
| T8  | **Scrambling** [P3-S63]              | b̃(i) = b(i) ⊕ c(i), with c(i) a pseudo-random sequence                                                     | Randomize bits so all QAM points (inner and outer) are used    | 45 360 bits                                                      |
| T9  | **Modulation** [P3-S67 to S69]       | Map bits to QPSK / 16 / 64 / 256-QAM symbols                                                               | Bits to complex symbols for transmission                       | 45 360 / 4 = **11 340** 16-QAM symbols = 70 PRB × 162 data REs ✓ |


### 4. Channel

The AWGN channel adds noise: **y = x + n** (also written r = s + n) [P2-S30, P3-S83]. Thermal noise is modeled as **Gaussian** (by the central limit theorem).

### 5. Receiver: block by block (exact reverse)

| #   | Block                                     | What it does                                                                                                                                                                                                                                                                                                                                                                                                                                |
| --- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| R1  | **Demodulation** [P3-S73, S74]            | Detect bits from the 4/16/64/256-QAM symbols. **Hard decoding:** apply the **nearest-distance rule**: threshold the equalized symbol to the nearest constellation point, then demap to bits. **Soft decoding:** instead of thresholding, **compute the probabilities** (MAP ratio) and pass them on [P3-S92]. The LDPC decoder works on **bits, not symbols**, because it is not practical to design a decoder for every modulation scheme. |
| R2  | **Descrambling** [P3-S75, S76]            | b(i) = b̃(i) ⊕ c(i) with the **same** c(i). XOR again restores the original bits.                                                                                                                                                                                                                                                                                                                                                            |
| R3  | **Code-block segmentation** [P3-S77, S78] | Split the 45 360 bits back into **C = 4 blocks of 11 340**                                                                                                                                                                                                                                                                                                                                                                                  |
| R4  | **De-interleaving** [P3-S79]              | Undo the row-column interleaver: write column-wise, read row-wise                                                                                                                                                                                                                                                                                                                                                                           |
| R5  | **Rate recovery** [P3-S80]                | Undo puncturing/repetition. **The LDPC decoder works only at code rate 1/3, so the rate must be reverted to 1/3** (11 340 → 23 232 positions)                                                                                                                                                                                                                                                                                               |
| R6  | **LDPC decoding** [P3-S81]                | Feed each rate-recovered block to the LDPC decoder to correct errors (C decoders in parallel)                                                                                                                                                                                                                                                                                                                                               |
| R7  | **CB-CRC validation** [P3-S81]            | **Validate the CRC of each code block**                                                                                                                                                                                                                                                                                                                                                                                                     |
| R8  | **Concatenation**                         | Remove CB-CRCs and filler bits, and join the blocks back into the TB + TB-CRC (28 192 bits)                                                                                                                                                                                                                                                                                                                                                 |
| R9  | **TB-CRC validation** [P3-S81]            | **If every CB-CRC passes, validate the TB-CRC. If the TB-CRC passes, the PHY layer's job is done** and the 28 168-bit TB goes to the MAC. **Otherwise HARQ is triggered** (retransmission).                                                                                                                                                                                                                                                 |

---

## A4-Q2: Segmentation Numerical (70 PRB, 16-QAM, TB = 28 168)

> **Q.** A user is allocated 70 RBs over a slot of 14 OFDM symbols. Modulation 16-QAM (MCS-16) with code rate R = 658/1024. The MAC provides a TB of 28 168 bits. Determine whether TB segmentation is required. If so, calculate the number of code blocks and the size of each code block. Also explain the need for filler bits. **(5 marks)**

### Given

- N_PRB = 70, symbols = 14, 16-QAM gives Q_m = 4 bits/symbol, R = 658/1024 = 0.642
- TB = 28 168 bits; K_cb = 8448 (BG1); L = 24; K_b = 22

### Step 1: add the TB-CRC [P3-S41]

$$B = 28\,168 + 24 = \mathbf{28\,192\ bits}$$

### Step 2: is segmentation required?

$$B = 28\,192 > K_{cb} = 8448 \;\Rightarrow\; \textbf{YES, segmentation is required}$$

### Step 3: number of code blocks [P3-S42]

$$C = \left\lceil \frac{B}{K_{cb}-L}\right\rceil = \left\lceil \frac{28\,192}{8448-24}\right\rceil = \left\lceil \frac{28\,192}{8424}\right\rceil = \lceil 3.347\rceil = \boxed{4}$$

### Step 4: effective payload and code-block size

$$B' = B + C\cdot L = 28\,192 + 4\times 24 = \mathbf{28\,288}$$
$$K' = \left\lceil\frac{B'}{C}\right\rceil = \frac{28\,288}{4} = \mathbf{7072\ bits}\quad (\text{TB bits + 24-bit CB-CRC, per block})$$

### Need for filler bits [P3-S42, S46, S47, S49, S50]

1. **The LDPC encoder accepts only specific sizes.** K must equal K_b × Z_c, where Z_c comes from a fixed table of lifting sizes. The segmented size K′ = 7072 is **not** one of those values (22 × Z_c ∈ {…, 7040, 7744, …}).
2. **Filler bits fill the gap.** Pick the next valid size K = 7744 and pad each block with F = K − K′ = 672 filler bits, so the block exactly matches the parity-check matrix H built with Z_c = 352.
3. **Where:** they are added at the **end of each code block** (after the CB-CRC), after segmentation and CB-CRC insertion and **before LDPC encoding**. They are usually denoted **−1** (NULL).
4. **How they are handled:** they are **replaced with zeros while encoding**. They carry **no information**, so they are **not transmitted**: the actual code rate is computed with them removed (7072/11 340 = 0.623). The receiver knows their positions.
5. "K′ = B′/C = 7072. **Not done like this ☺. Need additional filler bits.**" [P3-S42]: without filler bits, the block simply could not be encoded.

**✍️ Exam tips:** Number the steps (B → check → C → B′ → K′ → Z_c → K → F) and box each result. Draw the 4-block structure diagram. For "need for filler bits", write the K = 22·Z_c argument, give the numbers 7072 → 7744, and say "not transmitted".

---

## A4-Q3: Interleaving and Scrambling

> **Q.** Explain the interleaving and scrambling processes in the 5G NR PHY layer. Describe the working of the row-column interleaver and explain how the interleaved code blocks are subsequently scrambled using a pseudo-random sequence. Also explain the purpose of interleaving and scrambling in the overall transmission. Why is XOR used in scrambling, and how does it produce the scrambled output bits? **(5 marks)**

### 1. Position in the chain [P3-S60]

```
 ... -> [Rate Matching] -> [INTERLEAVER] -> [CB Concatenation] -> [SCRAMBLER] -> [Modulation] -> channel
            (x C)            (x C)              (C -> 1)
 ... <- [Rate Recovery] <- [DE-INTERLEAVER] <- [CB Segmentation] <- [DESCRAMBLER] <- [Demodulation] <-
```

Interleaving works on **each rate-matched code block separately**. Scrambling works on the **whole concatenated block of G bits**.

### 2. Row-column interleaver [P3-S61]

**Rule:** each rate-matched code block of length **E** is
1. **written row by row** into a matrix of **M rows × E/M columns**, where **M = modulation order** (bits per symbol): **M = 2, 4, 6, 8** for QPSK, 16-QAM, 64-QAM, 256-QAM;
2. **read column by column.**

**Example from the notes (QPSK, M = 2, E = 16):**

Rate-matching output: `0100110001010011`

**Write row-wise** (2 rows × 8 columns):
```
          col: 1 2 3 4 5 6 7 8
  row 1  -->   0 1 0 0 1 1 0 0
  row 2  -->   0 1 0 1 0 0 1 1
```
**Read column-wise** (top to bottom, column 1 to 8):
```
  col1  col2  col3  col4  col5  col6  col7  col8
   0     1     0     0     1     1     0     0      <- row 1
   0     1     0     1     0     0     1     1      <- row 2
  ---   ---   ---   ---   ---   ---   ---   ---
  "00"  "11"  "00"  "01"  "10"  "10"  "01"  "01"
```
**Interleaver output:** `0011000110100101` ✅ (matches [P3-S61])

**Why the number of rows = modulation order (the idea behind "avoid burst errors"):**
Each **column** (M bits) becomes **one modulation symbol**. With M = 2, column 1 = (bit 1, bit 9) forms one QPSK symbol. So the bits of one symbol come from **far-apart positions** of the codeword (E/M apart). If a deep fade or noise burst destroys a few consecutive symbols, the corrupted bits are **spread out** across the codeword instead of forming a **burst**, and the LDPC decoder corrects scattered errors much more easily.

**Same 16 bits with 16-QAM (M = 4, a 4 × 4 matrix)**, as practice:
```
  write row-wise:   0 1 0 0         read column-wise:
                    1 1 0 0         col1 = 0 1 0 0
                    0 1 0 1         col2 = 1 1 1 0
                    0 0 1 1         col3 = 0 0 0 1
                                    col4 = 0 0 1 1
  output = 0100 1110 0001 0011  (each 4-bit group = one 16-QAM symbol)
```

In the running example (16-QAM, E = 11 340) each block is written into **4 rows × 2835 columns**.

**De-interleaver (receiver) [P3-S79]:** "De-interleave them by reading them appropriately". Write the received bits **column-wise** into the same M × E/M matrix and **read row-wise**. Check: `0011000110100101` → write in columns (00, 11, 00, 01, 10, 10, 01, 01) → read rows → `01001100` `01010011` = original ✅.

### 3. Code-block concatenation (between the two) [P3-S62]

- When C > 1, the interleaved rate-matched blocks are **concatenated sequentially**: f_rj (r = 0…C−1, j = 0…E_r − 1) becomes g_k (k = 0…G−1).
- Running example: 4 blocks × 11 340 = **45 360 = G** = 70 × 162 × 4 ✓.

```
 |<-- 11340 -->|<-- 11340 -->|<-- 11340 -->|<-- 11340 -->|
 +-------------+-------------+-------------+-------------+
 | rate-matched| rate-matched| rate-matched| rate-matched|
 |   block 1   |   block 2   |   block 3   |   block 4   |
 +-------------+-------------+-------------+-------------+
 |<------------------ G = 45 360 bits ------------------>|
```

### 4. Scrambling [P3-S63]

The G concatenated bits b(0), …, b(G−1) are scrambled bit by bit with a **pseudo-random sequence c(i)**:

$$\boxed{\ \tilde b(i) = b(i)\oplus c(i),\qquad i = 0,1,\dots,G-1\ }$$

**Example from the notes:**

| i                    | 0     | 1     | 2     | 3     | 4     | 5     | 6     | 7     |
| -------------------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| b(i) (coded bits)    | 0     | 1     | 0     | 0     | 1     | 0     | 1     | 0     |
| c(i) (pseudo-random) | 0     | 1     | 1     | 0     | 1     | 0     | 0     | 1     |
| **b̃(i) = b ⊕ c**     | **0** | **0** | **1** | **0** | **0** | **0** | **1** | **1** |

**Scrambled output b̃ = [0 0 1 0 0 0 1 1]** ✅

**Descrambling at the receiver [P3-S75, S76]:** XOR with the **same** sequence:

$$b(i) = \tilde b(i)\oplus c(i)$$

| i         | 0     | 1     | 2     | 3     | 4     | 5     | 6     | 7     |
| --------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| b̃(i)      | 0     | 0     | 1     | 0     | 0     | 0     | 1     | 1     |
| c(i)      | 0     | 1     | 1     | 0     | 1     | 0     | 0     | 1     |
| **b̃ ⊕ c** | **0** | **1** | **0** | **0** | **1** | **0** | **1** | **0** |

which equals the original b = [0 1 0 0 1 0 1 0] ✅.

### 5. Why XOR, and how it produces the output

**XOR truth table:**

| b   | c   | b ⊕ c | Meaning                  |
| --- | --- | ----- | ------------------------ |
| 0   | 0   | 0     | c = 0: bit **unchanged** |
| 1   | 0   | 1     | c = 0: bit **unchanged** |
| 0   | 1   | 1     | c = 1: bit **flipped**   |
| 1   | 1   | 0     | c = 1: bit **flipped**   |

**How it produces the output:** wherever the pseudo-random bit c(i) = 1 the data bit is inverted, and wherever c(i) = 0 it passes unchanged. Since c(i) looks random, the output looks random **whatever the input pattern** (even all-zeros or all-ones).

**Why XOR is used:**
1. **Self-inverse, so recovery is trivial:** (b ⊕ c) ⊕ c = b ⊕ (c ⊕ c) = b ⊕ 0 = b. The receiver uses the **same operation and the same sequence**; no separate inverse circuit is needed.
2. **Randomizes the data:** XOR with a random sequence breaks long runs of identical bits.
3. **No change in length or rate:** one output bit per input bit, so G stays G and no extra bandwidth is used.
4. **Simplest hardware:** a single XOR gate per bit, done at bit level.
5. **No error multiplication** (follows from the formula): if the channel flips bit i (error e(i)), then descrambling gives (b ⊕ c ⊕ e) ⊕ c = b ⊕ e, so a single channel error stays a single bit error.

### 6. Purpose of interleaving and scrambling

**Interleaving:**
- **Avoid burst errors** [P3-S61]: fading or interference hits **consecutive** symbols. The interleaver spreads those errors across the codeword so the LDPC decoder sees **random, isolated errors**, which it can correct.
- Row size = modulation order, so the bits sharing one QAM symbol come from different parts of the code block.

**Scrambling:**
- **Randomizes the output of code-block concatenation** [P3-S70].
- **Uses the whole QAM constellation** [P3-S71]. If the interleaver output contains **repetitive bits**, then for 16/64/256-QAM **only the low-energy or only the high-energy points would be used**. Scrambling makes sure **both inner and outer points** are used.


> ✅ **Summary:** The interleaver writes each rate-matched block row-wise into an M × E/M matrix (M = 2/4/6/8) and reads it column-wise, which spreads burst errors. The blocks are concatenated (G bits) and scrambled as b̃(i) = b(i) ⊕ c(i) with a pseudo-random c(i), which randomizes the bits so inner and outer QAM points are used. XOR is self-inverse, so the receiver descrambles with the same c(i).

**✍️ Exam tips:** Draw the interleaver matrix example (write rows, read columns) and the scrambling table. State the purposes in two clear bullet lists. For "why XOR", the **self-inverse proof** (b ⊕ c ⊕ c = b) is the key line.

---

## A4-Q4: Lifting Size Zc and Filler Bits

> **Q.** Explain the concept of lifting size Zc in 5G NR LDPC coding. Discuss why different lifting sizes are specified, and explain how an appropriate lifting size is selected for a segmented code block. Also explain the need for filler bits, when they are added, and how they help match the segmented code-block size to the selected lifting size. **(5 marks)**

### 1. Background: LDPC and the base graph [P3-S43]

- An LDPC code is defined by a **parity-check matrix H**. A codeword **v** is chosen such that

$$\mathbf{H}\,\mathbf{v} = \mathbf{0}\quad(\text{mod } 2)$$

- 5G NR does **not** store a huge H directly. It stores a small **base graph matrix u** and **expands ("lifts")** it into H using a **lifting factor Z_c**.

### 2. Concept of lifting [P3-S43, S44]

**Lifting:** each (integer) entry of the base graph **u** is **replaced by a Z_c × Z_c permuted (circularly shifted) identity matrix.** The entry value says **how far to shift** the identity.

**Example from the notes:** a 2 × 2 base graph, Z_c = 3.

$$\mathbf{u}=\begin{bmatrix}2 & 3\\ 0 & 1\end{bmatrix},\qquad \mathbf{I}=\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix}$$

Shift each row of I to the right by the entry value (mod Z_c = 3):

| Entry | Shift       | Resulting 3 × 3 block       |
| ----- | ----------- | --------------------------- |
| 2     | 2           | [0 0 1; 1 0 0; 0 1 0]       |
| 3     | 3 mod 3 = 0 | [1 0 0; 0 1 0; 0 0 1] (= I) |
| 0     | 0           | [1 0 0; 0 1 0; 0 0 1] (= I) |
| 1     | 1           | [0 1 0; 0 0 1; 1 0 0]       |

$$\mathbf{H}=\left[\begin{array}{ccc|ccc}0&0&1&1&0&0\\1&0&0&0&1&0\\0&1&0&0&0&1\\\hline 1&0&0&0&1&0\\0&1&0&0&0&1\\0&0&1&1&0&0\end{array}\right]$$

```
   base graph u (2 x 2)              lifted H (6 x 6) = (2*Zc) x (2*Zc)
   +---+---+                         +-------+-------+
   | 2 | 3 |    each entry  --->     | I(2)  | I(3)  |   I(s) = 3x3 identity
   +---+---+    becomes a            +-------+-------+          circularly
   | 0 | 1 |    Zc x Zc block        | I(0)  | I(1)  |          shifted by s
   +---+---+    (Zc = 3)             +-------+-------+
```

**Key point:** the **same base graph** produces H of size (rows × Z_c) × (cols × Z_c). By changing **Z_c alone**, one base graph generates **many different code sizes**.

### 3. Why different lifting sizes are specified [P3-S45, S46]

- TB sizes in 5G range from **24 bits** (MCS-0, 1 RB) to **319 784 bits** (MCS-27, 275 RBs) [P2-S31], so code-block sizes after segmentation vary enormously.
- The LDPC input size is **K = K_b × Z_c**, where **K_b = 22** systematic (message) columns are fixed by the base graph. **The only way to support many block sizes with one base graph is to support many values of Z_c.**
- The standard therefore gives **Table 5.3.2-1 of TS 38.212**: 8 sets of lifting sizes.

| Set index i_LS | Set of lifting sizes Z           | Pattern  |
| -------------- | -------------------------------- | -------- |
| 0              | {2, 4, 8, 16, 32, 64, 128, 256}  | 2 × 2^j  |
| 1              | {3, 6, 12, 24, 48, 96, 192, 384} | 3 × 2^j  |
| 2              | {5, 10, 20, 40, 80, 160, 320}    | 5 × 2^j  |
| 3              | {7, 14, 28, 56, 112, 224}        | 7 × 2^j  |
| 4              | {9, 18, 36, 72, 144, 288}        | 9 × 2^j  |
| 5              | {11, 22, 44, 88, 176, 352}       | 11 × 2^j |
| 6              | {13, 26, 52, 104, 208}           | 13 × 2^j |
| 7              | {15, 30, 60, 120, 240}           | 15 × 2^j |

- Z_c runs from 2 to 384 in **fine steps**, so K = 22 × Z_c can be chosen **close to** any K′. Few filler bits are wasted and efficiency stays high.
- Maximum: 22 × 384 = **8448 = K_cb** (BG1). This is where the K_cb value used in segmentation comes from.
- Hardware benefit: one base-graph structure plus circular shifts is simple to implement for all sizes.

### 4. How to select Z_c for a segmented code block [P3-S46]

$$\boxed{\ Z_c = \min\{\,Z \in \text{Table 5.3.2-1}\;:\; K_b\times Z \ge K'\,\}\ }\qquad K = K_b\times Z_c$$

Steps:
1. Find K′ (code-block size including the CB-CRC) from segmentation.
2. Compute K′/K_b = K′/22.
3. From the table, take the **smallest Z ≥ K′/22**.
4. K = 22 × Z_c.

**Example 1 (running example, K′ = 7072):**
K′/22 = 321.45. Sorted Z values near this: …, 288, 320, **352**, 384.
- 22 × 320 = 7040 < 7072 ✗
- **22 × 352 = 7744 ≥ 7072 ✓** → **Z_c = 352** (set 5), **K = 7744**

**Example 2 (A3-Q5, K′ = 6024):**
K′/22 = 273.8. Z values: …, 256, **288**, 320, … → 22 × 256 = 5632 ✗, **22 × 288 = 6336 ✓** → **Z_c = 288** (set 4), **K = 6336**

### 5. Filler bits: why, when, how [P3-S46, S47, S49, S50]

**Why needed:** K′ (from segmentation) almost never equals an allowed K = 22 × Z_c exactly. *"When the segmented block size is not matched to a suitable lifting size, filler bits are added."*

**How many:**

$$F = K - K'$$

- Example 1: F = 7744 − 7072 = **672** per code block
- Example 2: F = 6336 − 6024 = **312** per code block

**When they are added:** after **TB-CRC → segmentation → CB-CRC insertion**, and **before LDPC encoding** (the "Insertion of filler bits in all code blocks" step in [P2-S39, P3-S51]). They are appended at the **end of each code block** and are usually denoted **−1** (NULL).

```
   TB + TB-CRC
        |
   segmentation  -->  CB #1 ... CB #C
        |
   + CB-CRC      -->  [ data | CB-CRC ]                       = K' bits
        |
   + filler bits -->  [ data | CB-CRC | F filler (-1) ]       = K = 22 x Zc bits
        |
   LDPC encoder  (filler bits treated as 0 while encoding)
        |
   rate matching (filler bits NOT transmitted)
```

**How they help match the size:**
- The LDPC encoder is built from H with exactly **22·Z_c** systematic columns, so it **needs exactly K = 22·Z_c input bits**.
- Filler bits **pad K′ up to K**, which makes the block fit the selected lifting size.
- They are **replaced with zeros while encoding** [P3-S50]. They carry no data, so they are **not transmitted** (the effective rate uses K′: 7072/11 340 = 0.623), and the receiver knows they are zeros.
- Because Z_c is the **minimum** valid value, F is kept as small as possible.

The bit sequences, as the notes denote them [P3-S49, S50]: the segmentation input is b₀, b₁, …, b_{B−1}; its output for block r is c_{r0}, c_{r1}, …, c_{r(K_r−1)}, with filler bits at the end. The LDPC input is c₀ … c_{K−1} (subscript r dropped), and its output is d₀ … d_{N−1}.

> ✅ **Summary:** Z_c is the size of the circularly shifted identity blocks that expand a small base graph into the full H. Many Z_c values (Table 5.3.2-1, 2…384) let one base graph serve all block sizes, K = 22·Z_c. Choose the minimum Z_c with 22·Z_c ≥ K′. Add F = K − K′ filler bits (−1/zeros, not transmitted) at the end of each CB after CB-CRC and before encoding, so the block exactly fits. Example: K′ = 7072 gives Z_c = 352, K = 7744, F = 672.

**✍️ Exam tips:** Show the u → H lifting example (one shifted 3 × 3 block is enough), the table (at least 3–4 rows), the selection inequality and the 320 ✗ / 352 ✓ check, and the F formula with numbers.

---

## A4-Q5: LDPC Base Graphs BG1/BG2 and LDPC Encoding

> **Q.** Explain the two LDPC base graphs (BG1 and BG2) used in 5G NR and compare their applications. Also explain the LDPC encoding process with a neat block diagram. **(5 marks)**

### 1. LDPC basics [P3-S43]

- **LDPC (Low-Density Parity-Check) code:** defined by a sparse parity-check matrix **H** (mostly 0s). A valid codeword satisfies **H v = 0**.
- 5G NR (PDSCH) uses a small **base graph** and **lifts** it with Z_c to obtain H (see A4-Q4).
- The two things to remember [P3-S45]: (1) **the notion of base graph and lifting size**; (2) **the number of systematic (message) columns of the encoder, K_b = 22.**

### 2. The two base graphs [P3-S45, S50]

*"5G PDSCH supports two base graphs to ensure good performance."*

| Feature                  | **Base Graph 1 (BG1)**                                            | **Base Graph 2 (BG2)**                                                      |
| ------------------------ | ----------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Optimized for            | **Large information block sizes** and **high code rates**         | **Small information block sizes** and **low code rates**                    |
| Code-rate range          | **Max 8/9, min 1/3**                                              | **Lowest 1/5**                                                              |
| Max code-block size K_cb | **8448**                                                          | **3840**                                                                    |
| Encoder output length    | **N = 66 Z_c**                                                    | **N = 50 Z_c**                                                              |
| Decoding complexity      | Higher                                                            | **Much lower** than BG1                                                     |
| Typical application      | Large TBs and high-throughput data (eMBB, good channel, high MCS) | Small TBs / low-rate transmissions (poor channel, cell edge, small packets) |
| Running example          | ✔ used: K = 7744, N = 66 × 352 = 23 232, rate 1/3                 | not used                                                                    |

*(Beyond notes, TS 38.212 rule: BG2 is used if TB size A ≤ 292, or A ≤ 3824 with R ≤ 0.67, or R ≤ 0.25; otherwise BG1. For the running example A = 28 168 and R = 0.642, so BG1 ✓.)*

**Comparison in words:**
- **BG1** is the "big-block, high-rate" graph. It handles up to 8448 bits per code block and rates up to 8/9, so it suits large, high-throughput transmissions.
- **BG2** is the "small-block, robust" graph. It goes down to rate 1/5 (more redundancy, so more robust at low SNR), handles up to 3840 bits, and is **much cheaper to decode**. It suits small packets and weak channels.
- With two graphs, 5G gets good performance across the whole range of block sizes and rates. One graph alone would be inefficient at one of the two extremes.

### 3. LDPC encoding process: block diagram [P3-S43 to S50]

```
   from segmentation: c_0, c_1, ..., c_(K-1)      (K = K' info+CRC bits + F filler bits)
                         |
                         v
   +--------------------------------------------+
   | 1. Replace filler bits (-1) by 0           |  filler bits = 0 during encoding
   +--------------------------------------------+
                         |
                         v
   +--------------------------------------------+      +---------------------------+
   | 2. Select base graph: BG1 or BG2           |<-----| block size and code rate  |
   |    (BG1: large K, high R; BG2: small, low) |      | (from MCS / TB size)      |
   +--------------------------------------------+      +---------------------------+
                         |
                         v
   +--------------------------------------------+      +---------------------------+
   | 3. Select lifting size Zc                  |<-----| Table 5.3.2-1 (38.212)    |
   |    min Zc with  Kb * Zc >= K'  (Kb = 22)   |      | sets i_LS = 0..7          |
   +--------------------------------------------+      +---------------------------+
                         |
                         v
   +--------------------------------------------+
   | 4. Lift base graph u -> parity-check H     |  each entry -> Zc x Zc circularly
   |                                            |  shifted identity matrix
   +--------------------------------------------+
                         |
                         v
   +--------------------------------------------+
   | 5. Compute parity bits p such that         |  codeword v = [ c | p ]
   |    H v = 0  (mod 2)                        |  systematic part + parity part
   +--------------------------------------------+
                         |
                         v
   +--------------------------------------------+
   | 6. Output d_0, d_1, ..., d_(N-1)           |  N = 66 Zc (BG1) / 50 Zc (BG2)
   |    filler positions marked again (-1)      |  mother code rate K/N = 1/3 (BG1)
   +--------------------------------------------+
                         |
                         v
                 to RATE MATCHING  (adapts rate 1/3 -> desired rate, e.g. 0.642)
```

**Step-by-step explanation:**
1. **Input:** K bits c₀ … c_{K−1} from segmentation: data + CB-CRC + filler bits [P3-S50]. The subscript r (code-block number) is dropped when feeding the encoder.
2. **Filler bits → 0** while encoding (they are added back as markers after encoding and never transmitted).
3. **Base graph choice:** BG1 for large blocks / high rates, BG2 for small blocks / low rates.
4. **Lifting size choice:** minimum Z_c from the table with 22 Z_c ≥ K′ (K′ = 7072 gives Z_c = 352).
5. **Build H** by lifting: every entry of u becomes a Z_c × Z_c circularly shifted identity.
6. **Encode:** find the parity bits so that the codeword satisfies **H v = 0**. The code is systematic: the message bits appear unchanged, followed by the parity bits.
7. **Output** N bits d₀ … d_{N−1}: N = 66 Z_c (BG1) or 50 Z_c (BG2).
   Running example: N = 66 × 352 = **23 232**, rate K/N = 7744/23 232 = **1/3** (the **mother code rate**).
8. **Rate matching** then punctures or repeats bits to fit the resources (E = 11 340), giving the actual rate 7072/11 340 = 0.623 ≈ 0.642.

### 4. Tiny worked encoding using the notes' H (to *see* H v = 0)

With the 6 × 6 H from [P3-S43], H v = 0 gives 3 independent equations (rows 4–6 repeat rows 2, 3, 1):

$$\text{row 1: } v_3 \oplus v_4 = 0,\qquad \text{row 2: } v_1 \oplus v_5 = 0,\qquad \text{row 3: } v_2 \oplus v_6 = 0$$

Take the message bits as (v₁, v₂, v₃) and the parity bits as (v₄, v₅, v₆) = (v₃, v₁, v₂).

**Encode message 1 0 1:** parity = (v₃, v₁, v₂) = (1, 1, 0), so **codeword v = 1 0 1 | 1 1 0**.
Check H v: row 1: v₃ ⊕ v₄ = 1 ⊕ 1 = 0 ✓; row 2: v₁ ⊕ v₅ = 1 ⊕ 1 = 0 ✓; row 3: v₂ ⊕ v₆ = 0 ⊕ 0 = 0 ✓. So **H v = 0** ✓.
(Real 5G does exactly this, but with 22 Z_c message bits and a much bigger lifted H.)

> ✅ **Summary:** BG1 is for large blocks and high rates (1/3 to 8/9, K_cb = 8448, N = 66 Z_c). BG2 is for small blocks and low rates (down to 1/5, K_cb = 3840, N = 50 Z_c, much lower decoding complexity). Encoding: input c (with filler bits set to 0) → choose BG → choose Z_c → lift u to H → compute parity so that Hv = 0 → output d of length N (mother rate 1/3) → rate matching.

**✍️ Exam tips:** Draw the comparison table (at least 5 rows) and the vertical encoding block diagram. Write the formulas **N = 66 Z_c / 50 Z_c**, **K = 22 Z_c**, **Hv = 0**, and the running-example numbers (7744 → 23 232, rate 1/3).

---

## Formula Sheet (one-page revision)

| Topic               | Formula / fact                                                                                             |
| ------------------- | ---------------------------------------------------------------------------------------------------------- |
| Numerology          | Δf = 2^μ × 15 kHz, μ = 0…4 → 15, 30, 60, 120, 240 kHz                                                      |
| Symbol time         | T_u = 1/Δf = 66.67/2^μ μs → 66.67, 33.33, 16.67, 8.33, 4.17 μs                                             |
| CP                  | 4.7, 2.3, 1.2, 0.59, 0.29 μs                                                                               |
| Sampling            | Δf = f_s/N, e.g., 122.88 MHz / 4096 = 30 kHz                                                               |
| Frame               | 10 ms = 10 subframes × 1 ms; slot = 14 OFDM symbols                                                        |
| Slot                | T_slot = 1/2^μ ms; slots/subframe = 2^μ; slots/frame = 10·2^μ                                              |
| 30 kHz slot samples | 14 × (288 + 4096) = 61 376; 0.5 ms × 122.88 MHz = 61 440; 64 extra go to the 1st CP                        |
| PRB                 | 1 PRB = 12 subcarriers; 100 MHz → 3300/12 = 275 PRB; 1 PRB × 1 slot = 168 RE (162 data + 6 pilots)         |
| Max-Rate            | k = arg max_i R_i[m]                                                                                       |
| PF                  | k = arg max_i R_i[m]/R̄_i[m]; R̄_i[m+1] = (1−1/t_c) R̄_i[m] + (1/t_c) R_i[m] if served, else (1−1/t_c) R̄_i[m] |
| CRC                 | append deg(G) zeros, mod-2 divide, remainder = CRC; Rx remainder 0 means no error                          |
| TB sizes            | min 24 (MCS-0, 1 RB); max 319 784 (MCS-27, 275 RB); CRC 24/16 bits                                         |
| Segmentation check  | B = TB + 24 > K_cb (8448 BG1 / 3840 BG2)                                                                   |
| No. of CBs          | C = ⌈B/(K_cb − 24)⌉; B′ = B + 24C; K′ = ⌈B′/C⌉                                                             |
| Lifting             | min Z_c with 22·Z_c ≥ K′; K = 22·Z_c                                                                       |
| Filler bits         | F = K − K′                                                                                                 |
| LDPC output         | N = 66 Z_c (BG1), 50 Z_c (BG2); mother rate 1/3                                                            |
| BG1 / BG2           | BG1: large K, R 1/3–8/9; BG2: small K, R down to 1/5, lower complexity                                     |
| Resources           | G = N_PRB × 162 × Q_m; E = G/C                                                                             |
| Interleaver         | write row-wise into M × E/M (M = 2/4/6/8), read column-wise                                                |
| Scrambling          | b̃(i) = b(i) ⊕ c(i); descramble b(i) = b̃(i) ⊕ c(i)                                                          |
| MAP                 | choose m₀ if p(ρ\|m₀)P[m₀] > p(ρ\|m₁)P[m₁]; equal priors → ML; BPSK threshold (s₀+s₁)/2 = 0                |

**Running example in one line:**
TB 28 168 → B 28 192 → C = 4 → B′ 28 288 → K′ 7072 → Z_c 352 → K 7744 (F 672) → N 23 232 (rate 1/3) → E 11 340 (G 45 360) → rate 0.623 → 11 340 16-QAM symbols on 70 PRB × 162 RE.

---

## Typos in the notes (don't get confused)

| Where         | Notes say                    | Correct                                                                             |
| ------------- | ---------------------------- | ----------------------------------------------------------------------------------- |
| P2-S38        | Max TB "319 874 (MCS-26)"    | **319 784 (MCS-27, 275 RBs)** as on P2-S31                                          |
| P3-S67        | "4/16/64/**356**-QAM"        | **256-QAM** (as on P3-S73)                                                          |
| P2-S33        | Quotient written as "111 01" | Full quotient is **111011**. It does not matter: only the remainder (11) is the CRC |
| P2-S35 (text) | "…+a₅D² + p₀D + p₁"          | **(a₀D⁵ + … + a₅)·D² + p₀D + p₁**: the whole TB polynomial is multiplied by D²      |
| P2-S20        | RR "serves two users"        | Works the same for K users (in turn)                                                |

---

## Question → Slide Map

| Assignment | Q   | Topic                                   | Notes slides                             |
| ---------- | --- | --------------------------------------- | ---------------------------------------- |
| A3         | Q1  | Numerology                              | P2-S3 to S9, S15                         |
| A3         | Q2  | Frame structure                         | P2-S11 to S16                            |
| A3         | Q3  | Max-Rate numerical + comparison         | P2-S17 to S23                            |
| A3         | Q4  | CRC                                     | P2-S31 to S35                            |
| A3         | Q5  | Segmentation (B = 12 000)               | P2-S38, S39; P3-S40 to S42, S46, S47     |
| A3         | Q6  | Scheduling algorithms                   | P2-S17 to S23                            |
| A4         | Q1  | Full TX/RX chain                        | P2-S25 to S30; P3-S51 to S92             |
| A4         | Q2  | Segmentation (TB = 28 168)              | P3-S41 to S50, S53, S55, S56             |
| A4         | Q3  | Interleaving, concatenation, scrambling | P3-S60 to S63, S68 to S71, S75, S76, S79 |
| A4         | Q4  | Lifting size, filler bits               | P3-S43, S44, S46, S47, S49, S50          |
| A4         | Q5  | BG1/BG2, LDPC encoding                  | P3-S43 to S45, S50, S53                  |

**Assignment 3 says "Solve any five".** Q3 and Q6 overlap heavily, so preparing Q6 well covers most of Q3. Q5 uses the same method as A4-Q2. Learn the *running example* once and you can answer A4-Q1, Q2, Q4 and Q5 with real numbers.

*All the best!*