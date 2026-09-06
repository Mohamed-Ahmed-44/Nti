module seq_detector_mealy (
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

    // 1. Sequential Logic: State Register
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
            S0: begin
                if (x) next_state = S1;
                else   next_state = S0;
            end
            
            S1: begin
                if (x) next_state = S2;
                else   next_state = S0;
            end
            
            S2: begin
                if (x) next_state = S2;
                else   next_state = S3;
            end
            
            S3: begin
                if (x) next_state = S4;
                else   next_state = S0;
            end
            
            S4: begin
                if (x) next_state = S2;
                else   next_state = S5;
            end
            
            S5: begin
                if (x) begin
                    next_state = S1;    
                    y = 1'b1;           
                end else begin
                    next_state = S0;
                end
            end
            
            default: next_state = S0;
        endcase
    end

endmodule

//---------------------------------------------

module seq_detector_moore (
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

    // 1. Sequential Logic: State Register
always @(posedge clk or posedge reset) begin
    if (reset)
       current_state <= S0;
    else
       current_state <= next_state;
    end

    // 2. Combinational Logic: Next State
always @(*) begin
       
next_state = current_state;

        case (current_state)
            S0: next_state = x ? S1 : S0;
            S1: next_state = x ? S2 : S0;
            S2: next_state = x ? S2 : S3;
            S3: next_state = x ? S4 : S0;
            S4: next_state = x ? S2 : S5;
            S5: next_state = x ? S6 : S0;
            S6: next_state = x ? S2 : S0; 
            default: next_state = S0;
        endcase
    end

    // 3. Combinational Logic: Output 
always @(*) begin
        if (current_state == S6)
            y = 1'b1;
        else
            y = 1'b0;
    end

endmodule

//---------------------------------------------

`timescale 1ns / 1ps

module tb_seq_detectors_combined;


reg clk;
reg reset;
reg x;
wire y_mealy;
wire y_moore;


seq_detector_mealy uut_mealy (
        .clk(clk), 
        .reset(reset), 
        .x(x), 
        .y(y_mealy)
);


seq_detector_moore uut_moore (
        .clk(clk), 
        .reset(reset), 
        .x(x), 
        .y(y_moore)
);


always #5 clk = ~clk;

initial begin
     
clk = 0;
reset = 1;
x = 0;  //input stream


$display("Time\t x\t y_mealy\t y_moore\t Mealy_State\t Moore_State");
$monitor("%0t\t %b\t    %b\t\t    %b\t\t     %b\t\t      %b", $time, x, y_mealy, y_moore, uut_mealy.current_state, uut_moore.current_state);

   
#15 reset = 0;

#10 x = 1; 
#10 x = 1; 
#10 x = 0; 
#10 x = 1; 
#10 x = 0; 
#10 x = 1; 
        

#10 x = 1; 
#10 x = 0; 
#10 x = 1; 
#10 x = 0; 
#10 x = 1; 
        

#10 x = 1; 
#10 x = 0; 
#10 x = 1; 
#10 x = 1; 
#10 x = 0; 
#10 x = 1; 
#10 x = 0; 
#10 x = 1; 

#10 x = 0; 
        
#20 $stop;
end
      
endmodule
