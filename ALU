module alu #(parameter width=8)(
input [width-1:0] in_a,
input [width-1:0] in_b,
input [2:0] opcode,
output reg [width-1:0] alu_out,
output  a_zero);
assign a_zero = (in_a ==0) ? 1'b1 : 1'b0;

always @(*) begin
case (opcode)

         3'b000: alu_out = in_a;                  
         3'b001: alu_out = in_a - in_b;           
         3'b010: alu_out = in_a + in_b;           
         3'b011: alu_out = in_a&in_b;           
         3'b100: alu_out = in_a^in_b;           
         3'b101: alu_out = in_b;                  
         3'b110: alu_out = in_a;                  
         3'b111: alu_out = in_a;
endcase
end
endmodule


//testbecnh
module tb_alu;

    parameter width = 8;
    
    reg  [width-1:0] in_a;
    reg  [width-1:0] in_b;
    reg  [2:0]       opcode;    
    wire [width-1:0] alu_out;
    wire a_zero;

alu #(.width(width)) tbb (
        .in_a(in_a),
        .in_b(in_b),
        .opcode(opcode),
        .alu_out(alu_out),
        .a_zero(a_zero)
);
initial begin
in_a = 8'd0; in_b = 8'd20; opcode = 3'b000;
#10;
in_a = 8'd10; in_b = 8'd5; opcode = 3'b001;
#10;
in_a = 8'b10110011; in_b = 8'b11101101; opcode = 3'b100;
#10;
in_a = 8'd15; in_b = 8'd10; opcode = 3'b010;
#10;
in_a = 8'b11001100; in_b = 8'b10101010; opcode = 3'b011;
#10;
$finish;
end
endmodule
