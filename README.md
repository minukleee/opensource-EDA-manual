# 메인 ALU 모듈 (`alu8bit`)

```verilog
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
            2'b00:   mux_out = and_out;   // AND 연산 결과
            2'b01:   mux_out = or_out;    // OR 연산 결과
            2'b10:   mux_out = add_out;   // ADD 연산 결과
            2'b11:   mux_out = sub_out;   // SUB 연산 결과
            default: mux_out = 8'h00;     // 디폴트 (사용하지 않는 경우 0)
        endcase
    end

    always @(posedge clk) begin
        result <= mux_out;
    end
endmodule

```

# 8-bit AND module

```verilog
module and8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a & b;
endmodule

```

# 8-bit OR module

```verilog
module or8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a | b;
endmodule
```

# 8-bit ADD module

```verilog
module add8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a + b;
endmodule
```


# 8-bit SUB module

```verilog
module sub8(input [7:0] a, input [7:0] b, output [7:0] y);
    assign y = a - b;
endmodule
```



# OpenLane 설정 파일 (config.tcl)

```tcl
set ::env(DESIGN_NAME) "alu8bit"                      ;# 최상위 모듈명 (top module)
set ::env(VERILOG_FILES) [glob $::env(DESIGN_DIR)/src/*.v] ;# Verilog 소스 파일 경로들
set ::env(CLOCK_PORT) "clk"                              ;# 클럭 입력 포트 이름
set ::env(CLOCK_PERIOD) "10.0"                           ;# 클럭 주기 (10ns = 100MHz)
set ::env(PL_TARGET_DENSITY) "0.7"                       ;# 배치 셀 밀도
```

# PDK별 기본 설정 포함

```tcl
set filename "$::env(DESIGN_DIR)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl"
if { [file exists $filename] } { source $filename }
```
