// 8bit ALU Verilog
module alu8bit(
    input  [7:0] A,
    input  [7:0] B,
    input  [1:0] op,
    input        clk,
    output reg [7:0] result
);
    // 각 연산의 출력 와이어 선언
    wire [7:0] and_out, or_out, add_out, sub_out;
    
    // 하위 연산 모듈 인스턴스화
    and8 u_and(.a(A), .b(B), .y(and_out));
    or8  u_or (.a(A), .b(B), .y(or_out));
    add8 u_add(.a(A), .b(B), .y(add_out));
    sub8 u_sub(.a(A), .b(B), .y(sub_out));
    
    // 4:1 멀티플렉서 (조합논리) - op 코드에 따른 출력 선택
    reg [7:0] mux_out;
    always @* begin
        case (op)
            2'b00:  mux_out = and_out;   // AND 연산 결과
            2'b01:  mux_out = or_out;    // OR 연산 결과
            2'b10:  mux_out = add_out;   // ADD 연산 결과
            2'b11:  mux_out = sub_out;   // SUB 연산 결과
            default: mux_out = 8'h00;    // 디폴트 (사용하지 않는 코드의 경우 0)
        endcase
    end
   
    always @(posedge clk) begin
        result <= mux_out;
    end
endmodule
