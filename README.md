# opensource-EDA-manual

## 8bit ALU Verilog

Verilog 코드는 하나의 파일(`.v`)에 모든 모듈을 포함하여 작성하는 것이 일반적입니다.

```verilog
// Main ALU Module
module alu8bit(
    input  [7:0] A,
    input  [7:0] B,
    input  [1:0] op,
    input        clk,
    output reg [7:0] result
);
    // Wires for outputs of each operation
    wire [7:0] and_out, or_out, add_out, sub_out;

    // Instantiating sub-modules
    and8 u_and(.a(A), .b(B), .y(and_out));
    or8  u_or (.a(A), .b(B), .y(or_out));
    add8 u_add(.a(A), .b(B), .y(add_out));
    sub8 u_sub(.a(A), .b(B), .y(sub_out));

    // 4:1 Multiplexer to select the output based on 'op' code
    reg [7:0] mux_out;
    always @* begin
        case (op)
            2'b00:   mux_out = and_out; // AND result
            2'b01:   mux_out = or_out;  // OR result
            2'b10:   mux_out = add_out; // ADD result
            2'b11:   mux_out = sub_out; // SUB result
            default: mux_out = 8'h00;   // Default case
        endcase
    end

    // Register the output on the positive edge of the clock
    always @(posedge clk) begin
        result <= mux_out;
    end
endmodule

// 8-bit AND module
module and8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a & b;
endmodule

// 8-bit OR module
module or8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a | b;
endmodule

// 8-bit ADD module
module add8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a + b;
endmodule

// 8-bit SUB module
module sub8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a - b;
endmodule
