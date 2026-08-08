---
layout: post
title:  "Derivation and Implementation of the Parallel Cyclic Redundancy Check on an FPGA"
date:   2026-08-05 20:45:00 -0300
categories: math, fpga
---

<script>
MathJax = {
  tex: {
    inlineMath: {'[+]': [['$', '$']]}
  },
  svg: {
    fontCache: 'global'
  }
};
</script>
<script defer src="https://cdn.jsdelivr.net/npm/mathjax@4/tex-svg.js"></script>

* TOC
{:toc}

# Introduction

Meaningful introduction here...

# Serial Cyclic Redundancy Check

The purpose of this section will be to develop an understanding of the two different serial CRCs...

# Problems with the Serial Cyclic Redundancy Check

The purpose of this section is to discuss the problems of with the serial CRC 

# Derivation of the Parallel Cylic Redundancy Check

To begin our derivation of the parallel CRC, we shall first consider the serial CRC circuit shown below in Figure X.

INSERT FIGURE OF CRC

The serial CRC circuit shown in Figure X has the generator polynomial

$$
g(x) = x^4 + x + 1
$$

Now, let us consider the state of the CRC circuit $$R[n]$$ at sample $$n$$. At the first sample $$n$$, the circuit takes on the state

$$
R[n] = \begin{pmatrix} f_3[n] \\\ f_2[n] \\\ f_1[n] \\\ f_0[n] \end{pmatrix}
$$

Now, let us consider what $$R[n]$$ is when a rising edge of the driving clock occurs (i.e., what is $$R[n + 1]$$). Well, this circuit is just a LFSR, so all of the values get shifted, with some modifications depending on the placement of the XORs (denoted by $$\oplus$$) and the input bit $$b[n]$$, right? So the state of the CRC circuit after the next rising edge of the clock will be

$$
R[n + 1] = \begin{pmatrix} f_2[n]  \\\ f_1[n] \\\ f_0[n] \oplus f_3[n] \oplus b[n] \\\ f_3[n] \oplus b[n] \end{pmatrix}
$$

So, after a rising edge of the clock occurs, this is the relationship that the next state of the CRC circuit has with its initial state. Let's break this matrix up into the sum of a matrix dependent on the states of the flip-flops and another dependent on the bit input into the circuit.

$$
R[n + 1] = \begin{pmatrix} f_2[n]  \\\ f_1[n] \\\ f_0[n] \oplus f_3[n] \\\ f_3[n] \end{pmatrix} \oplus 
\begin{pmatrix} 
  0 \\ 
  0 \\
  1 \\
  1 
\end{pmatrix} b[n]
$$

Next, let us factor the above equation so the matrix dependent on the state of the flip-flops into the form of some coefficient matrix $$A$$ multiplied by $$R[n]$$

$$
R[n + 1] = 
\begin{pmatrix}
  0 & 1 & 0 & 0 \\
  0 & 0 & 1 & 0 \\
  1 & 0 & 0 & 1 \\
  1 & 0 & 0 & 0
\end{pmatrix}
\begin{pmatrix}
  f_3[n] \\
  f_2[n] \\
  f_1[n] \\
  f_0[n]
\end{pmatrix} \oplus
\begin{pmatrix}
  0 \\
  0 \\
  1 \\
  1
\end{pmatrix} b[n]
$$

Notice anything about the first column of the matrix $$A$$ and the column matrix multiplied by $$b[n]$$? Well, for one, they are both the same. However, what is even more exciting is that they are the generator polynomial in matrix form! That is, our generator polynomial is given by

$$
g(x) = x^4 + x + 1
$$

Which, if we were to represent this as a matrix, it would be

$$
G' = 
\begin{pmatrix} 
  1 \\ 
  0 \\ 
  0 \\ 
  1 \\ 
  1 
\end{pmatrix}
$$

Or, in general, for a CRC generator polynomial CRC-N that has an order of N

$$
G' = 
\begin{pmatrix}
  g_{n} \\
  g_{n - 1} \\
  \vdots \\
  g_{1} \\
  g_{0}
\end{pmatrix}
$$

From here, you can see that $$R[n + 1]$$ is a function of $$G$$ where

$$
G = 
\begin{pmatrix}
  0 \\
  0 \\
  1 \\
  1
\end{pmatrix}
$$

Or, in general

$$
G = 
\begin{pmatrix}
  g_{n - 1} \\
  g_{n - 2} \\
  \vdots \\
  g_{1} \\
  g_{0}
\end{pmatrix}
$$

So, this means that the next state of the CRC circuit after the rising edge of the clock is

$$
R[n + 1] = A R[n] \oplus G b[n]
$$

where

$$
A = 
\begin{pmatrix}
  0 & 1 & 0 & 0 \\
  0 & 0 & 1 & 0 \\
  1 & 0 & 0 & 1 \\
  1 & 0 & 0 & 0
\end{pmatrix}, \ R[n] = 
\begin{pmatrix}
  f_3[n] \\
  f_2[n] \\
  f_1[n] \\
  f_0[n]
\end{pmatrix}, \ \text{and} \ G =
\begin{pmatrix}
  0 \\
  0 \\
  1 \\
  1
\end{pmatrix}
$$

Or, in general

$$
A = 
\begin{pmatrix}
  g_{n - 1} & 1         & 0         & \cdots    & 0 \\
  g_{n - 2} & 0         & 1         & 0         & \vdots \\
  \vdots    & \vdots    & 0         & \ddots    & 0 \\
  g_{1}     & 0         & \cdots    & 0         & 1 \\
  g_{0}     & 0         & \cdots    & \cdots    & 0
\end{pmatrix}, \ R[n] = 
\begin{pmatrix}
  f_{n - 1}[n] \\
  f_{n - 2}[n] \\
  \vdots \\
  f_1[n] \\
  f_0[n]
\end{pmatrix}, \ \text{and} \ G =
\begin{pmatrix}
  g_{n - 1} \\
  g_{n - 2} \\
  \vdots \\
  g_{1} \\
  g_{0}
\end{pmatrix}
$$

Okay, so now that we have a nice equation that relates state $$n + 1$$ with state $$n$$, let us consider how state $$n + 2$$ relates with state $$n$$

$$
R[n + 2] = A R[n + 1] \oplus G b[n + 1]
$$

Well, we know what $$R[n + 1]$$ is, so

$$
R[n + 2] = A (A R[n] \oplus G b[n]) \oplus G b[n + 1]
$$

$$
R[n + 2] = A^2 R[n] \oplus A G b[n] \oplus b[n + 1]
$$

For state $$n + 3$$

$$
R[n + 3] = A R[n + 2] \oplus G b[n + 2]
$$

$$
= A (A R[n + 1] \oplus G b[n + 1]) \oplus G b[n + 2]
$$

$$
= A^2 R[n + 1] \oplus A G b[n + 1] \oplus G b[n + 2]
$$

$$
= A^2 (A R[n] \oplus G b[n]) \oplus A G b[n + 1] \oplus G b[n + 2]
$$

$$
R[n + 3] = A^3 R[n] \oplus A^2 G b[n] \oplus A G b[n + 1] \oplus G b[n + 2]
$$

Notice the pattern yet? Well, if we were to relate state $$n + i$$ with state $$n$$, the relationship would be

$$
R[n + i] = A^i R[n] \oplus A^{i - 1} G b[n] \oplus A^{i - 2} \oplus \cdots \oplus A^2 G b[n + i - 3] \oplus A G b[n + i - 2] \oplus G b[n + i - 1]
$$

And that is it! The equation relating the output of a CRC circuit given $$i$$ input bits in parallel is given by the equation above.

# Implementation of the Parallel Cylic Redundancy Check on an FPGA

So, now you might be asking "How can I implement this on an FPGA?". Well, it is actually quite simple. All you have to do is unroll this large summation and multiplication of matrices. I will not go through all of the details on how to write this out in Verilog, but the method I chose was to define the equation in a function, allowing the synthesizer to infer combinatorial logic and then registered the output of the function when some valid input was present, and then fed that back into the function. My implementation of the parallel CRC can be found below. The module has a parameterizable generator polynomial and input bit width, along with some other added functionalities to support use with IEEE 802.3.

```verilog
module crc #(
    parameter c_DATA_WIDTH = 8,
    parameter c_GEN_POLY = 32'h04c11db7,
    parameter c_GEN_POLY_WIDTH = 32,
    parameter c_INITIAL_CRC_VALUE = {c_GEN_POLY_WIDTH{1'b1}},
    parameter c_REVERSE_INPUT_BIT_ORDER = 1,
    parameter c_REVERSE_OUTPUT_BIT_ORDER = 1,
    parameter c_COMPLEMENT_OUTPUT = 1
) (
    input wire i_rst,

    input wire i_clk,

    input wire [c_DATA_WIDTH - 1:0] i_data,
    input wire i_data_valid,
    output wire [c_GEN_POLY_WIDTH - 1:0] o_crc
);

reg [c_GEN_POLY_WIDTH - 1:0] r_crc = c_INITIAL_CRC_VALUE;
reg [c_GEN_POLY_WIDTH - 1:0] crc_next = {c_GEN_POLY_WIDTH{1'b0}};
reg [c_GEN_POLY_WIDTH - 1:0] t_crc;

assign o_crc = t_crc;

function [c_GEN_POLY_WIDTH - 1:0] crc (input [c_DATA_WIDTH - 1:0] i_data, input [c_GEN_POLY_WIDTH - 1:0] o_crc);
    reg [0:c_GEN_POLY_WIDTH - 1] A [0:c_GEN_POLY_WIDTH - 1];
    reg [0:c_GEN_POLY_WIDTH - 1] T0 [0:c_GEN_POLY_WIDTH - 1];
    reg [0:c_GEN_POLY_WIDTH - 1] T1 [0:c_GEN_POLY_WIDTH - 1];
    reg [c_GEN_POLY_WIDTH - 1:0] S;
    
    integer i;
    integer j;
    integer k;
    integer w;
    begin
        for (i = 0; i < c_GEN_POLY_WIDTH; i = i + 1) begin
            S[i] = 0;
            A[i] = 0;
            A[i][0] = c_GEN_POLY[c_GEN_POLY_WIDTH - 1 - i];
            T0[i] = 0;
            T0[i][i] = 1;
            T1[i] = 0;
            if (i > 0) begin
                A[i - 1][i] = 1;
            end
        end
                
        for (i = 0; i < c_DATA_WIDTH; i = i + 1) begin
            if (i == 0) begin
                if (c_REVERSE_INPUT_BIT_ORDER) begin
                    S = c_GEN_POLY & {c_GEN_POLY_WIDTH{i_data[c_DATA_WIDTH - 1'b1]}};
                end else begin
                    S = c_GEN_POLY & {c_GEN_POLY_WIDTH{i_data[0]}};
                end
            end
            
            if (i > 0) begin
                for (j = 0; j < c_GEN_POLY_WIDTH; j = j + 1) begin
                    for (k = 0; k < c_GEN_POLY_WIDTH; k = k + 1) begin
                        for (w = 0; w < c_GEN_POLY_WIDTH; w = w + 1) begin
                            T1[j][k] = (T0[j][w] & A[w][k]) ^ T1[j][k];
                        end
                    end
                end
                
                for (j = 0; j < c_GEN_POLY_WIDTH; j = j + 1) begin
                    for (k = 0; k < c_GEN_POLY_WIDTH; k = k + 1) begin
                        T0[j][k] = T1[j][k];
                        T1[j][k] = 0;
                        if (c_REVERSE_INPUT_BIT_ORDER) begin
                            S[c_GEN_POLY_WIDTH - 1 - j] = T0[j][k] * c_GEN_POLY[c_GEN_POLY_WIDTH - 1 - k] * i_data[c_DATA_WIDTH - 1'b1 - i] ^ S[c_GEN_POLY_WIDTH - 1 - j];
                        end else begin
                            S[c_GEN_POLY_WIDTH - 1 - j] = T0[j][k] * c_GEN_POLY[c_GEN_POLY_WIDTH - 1 - k] * i_data[i] ^ S[c_GEN_POLY_WIDTH - 1 - j];
                        end
                    end
                end                
            end
        end
        
        for (j = 0; j < c_GEN_POLY_WIDTH; j = j + 1) begin
            for (k = 0; k < c_GEN_POLY_WIDTH; k = k + 1) begin
                for (w = 0; w < c_GEN_POLY_WIDTH; w = w + 1) begin
                    T1[j][k] = T0[j][w] * A[w][k] ^ T1[j][k];
                end
            end
        end
                    
        for (j = 0; j < c_GEN_POLY_WIDTH; j = j + 1) begin
            for (k = 0; k < c_GEN_POLY_WIDTH; k = k + 1) begin
                T0[j][k] = T1[j][k];
                T1[j][k] = 0;
                S[c_GEN_POLY_WIDTH - 1 - j] = T0[j][k] * r_crc[c_GEN_POLY_WIDTH - 1 - k] ^ S[c_GEN_POLY_WIDTH - 1 - j];
            end
        end
                          
        crc = S;
    end
    
endfunction

integer i;

always @(*) begin
    if (c_REVERSE_OUTPUT_BIT_ORDER) begin
        if (c_COMPLEMENT_OUTPUT) begin
            for (i = 0; i < c_GEN_POLY_WIDTH; i = i + 1) begin
                t_crc[i] = ~r_crc[c_GEN_POLY_WIDTH - 1 - i];
            end
        end else begin
            for (i = 0; i < c_GEN_POLY_WIDTH; i = i + 1) begin
                t_crc[i] = r_crc[c_GEN_POLY_WIDTH - 1 - i];
            end
        end
    end else begin
        if (c_COMPLEMENT_OUTPUT) begin
            t_crc = ~r_crc;
        end else begin
            t_crc = r_crc;
        end
    end

    crc_next = crc(i_data, r_crc);
end

always @(posedge i_clk) begin
    if (i_data_valid) begin
        r_crc <= crc_next;
    end
    
    if (i_rst) begin
        r_crc <= c_INITIAL_CRC_VALUE;
    end
end

endmodule
```
