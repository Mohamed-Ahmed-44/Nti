module seq_detector_mealy_nonoverlap (
    input wire clk,
    input wire reset,
    input wire x,
    output reg y
);

parameter S0 = 3'b000, 
          S1 = 3'b001, 
          S2 = 3'b010, 
          S3 = 3'b011, 
          S4 = 3'b100, 
          S5 = 3'b101;

reg [2:0] current_state, next_state;

always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
end

always @(*) begin
next_state = current_state;
y = 1'b0;

case (current_state)
     S0: next_state = x ? S1 : S0;
     S1: next_state = x ? S2 : S0;
     S2: next_state = x ? S2 : S3;
     S3: next_state = x ? S4 : S0;
     S4: next_state = x ? S2 : S5;
     S5: begin
if (x) begin
    next_state = S0;    
    y = 1'b1;
    end else begin
     next_state = S0;
end
end
default: next_state = S0;
endcase
end

endmodule

module seq_detector_moore_nonoverlap (
    input wire clk,
    input wire reset,
    input wire x,
    output reg y
);

parameter S0 = 3'b000, 
          S1 = 3'b001, 
          S2 = 3'b010, 
          S3 = 3'b011, 
          S4 = 3'b100, 
          S5 = 3'b101,
          S6 = 3'b110;

reg [2:0] current_state, next_state;  
 
always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
    end
always @(*) begin
      next_state = current_state;

        case (current_state)
            S0: next_state = x ? S1 : S0;
            S1: next_state = x ? S2 : S0;
            S2: next_state = x ? S2 : S3;
            S3: next_state = x ? S4 : S0;
            S4: next_state = x ? S2 : S5;
            S5: next_state = x ? S6 : S0;
            S6: next_state = x ? S1 : S0; 
            default: next_state = S0;
        endcase
end

always @(*) begin
        if (current_state == S6)
            y = 1'b1;
        else
            y = 1'b0;
    end

endmodule

`timescale 1ns/1ps
module tb_seq_detectors_nonoverlap_combined;
    reg clk, reset, x;
    wire y_mealy, y_moore;
    integer i;
    
    reg [0:23] stream = 24'b1101011010111010100101; 
 
    seq_detector_mealy_nonoverlap uut_m(.clk(clk),.reset(reset),.x(x),.y(y_mealy));
    seq_detector_moore_nonoverlap uut_o(.clk(clk),.reset(reset),.x(x),.y(y_moore));
 
always #5 clk = ~clk;   
initial begin
        clk = 0; reset = 1; x = 0;
        @(negedge clk); @(negedge clk);
        reset = 0;
        $display("bit# x y_mealy y_moore  Mstate(before edge) Ostate(after edge)");
        for (i = 0; i < 24; i = i + 1) begin
            @(negedge clk);
            x = stream[i];               
            #4;                          
            $display("%0d    %b   %b    %b   %b  %b", i, x, y_mealy, y_moore, uut_m.current_state, uut_o.current_state);
        end
        $stop;
    end
endmodule

