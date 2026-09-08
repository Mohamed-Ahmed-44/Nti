`timescale 1ns / 1ps
module edge_detector_moore (
    input  wire clk,
    input  wire reset,
    input  wire level,
    output wire tick
);
    localparam [1:0] S0 = 2'b00, S1 = 2'b01, S2 = 2'b10;
    reg [1:0] state, next_state;


    always @(posedge clk or posedge reset) begin
        if (reset)
            state <=  S0; 
        else
            state <=  next_state;
    end


    always @(*) begin
        next_state = state; 
        case (state)
            S0: if (level)  next_state = S1;
            S1: if (level)  next_state = S2;
                else        next_state = S0;
            S2: if (~level) next_state = S0;
        endcase
    end


    assign tick = (state == S1);

endmodule


`timescale 1ns / 1ps
module edge_detector_mealy (
    input  wire clk,
    input  wire reset,
    input  wire level,
    output wire tick
);
    localparam S0 = 1'b0, S1 = 1'b1;
    reg state, next_state;


    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= S0;
        else
            state <= next_state;
    end


    always @(*) begin
        next_state = state; 
        case (state)
            S0: if (level)  next_state = S1;
            S1: if (~level) next_state = S0;
        endcase
    end


    assign tick = (state == S0) & level;

endmodule

`timescale 1ns / 1ps

module tb_edge_detectors;

    reg clk;
    reg reset;
    reg level;
    wire tick_moore;
    wire tick_mealy;


edge_detector_moore uut_moore (
        .clk(clk),
        .reset(reset),
        .level(level),
        .tick(tick_moore)
);


edge_detector_mealy uut_mealy (
        .clk(clk),
        .reset(reset),
        .level(level),
        .tick(tick_mealy)
);


    initial begin
        clk = 0;
        forever #5 clk = ~clk; 
    end


initial begin

        reset = 1;
        level = 0;


        #12; 
        reset = 0;


        #11; 
        level = 1;


        #35; 
        level = 0;


        #20;
        
        
       $stop;
    end

    initial begin
        $monitor("Time=%0t | reset=%b | level=%b || moore_tick=%b | mealy_tick=%b", $time, reset, level, tick_moore, tick_mealy);
    end
    
endmodule
