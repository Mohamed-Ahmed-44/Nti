module clk_div #(parameter DIV_RATIO = 8)(
    input wire clk_in, 
    input wire rstn,
    output reg clk_out
);

localparam COUNTER_THRESHOLD = (DIV_RATIO / 2) - 1;
localparam COUNTER_WIDTH = $clog2(COUNTER_THRESHOLD + 1);

    reg [COUNTER_WIDTH-1 : 0] counter;

    always @(posedge clk_in or negedge rstn) begin
        if (!rstn) begin
            counter <= 0;
            clk_out <= 0;
        end 
        else if (counter < COUNTER_THRESHOLD) begin
            counter <= counter + 1;
        end 
        else begin
            clk_out <= ~clk_out;
            counter <= 0;
end
end
endmodule



module light_chaser #(parameter WIDTH = 10)(
    input wire clk,
    input wire Reset, 
    input wire Hold,  
    output reg [WIDTH-1:0] reg_out
);

always @(posedge clk or negedge Reset) begin
        if (!Reset) begin
            reg_out <= {1'b1, {(WIDTH-1){1'b0}}};
        end else if (Hold) begin

            reg_out <= {reg_out[0], reg_out[WIDTH-1:1]};
end
end
endmodule



module top_level_light_chaser #(parameter CLK_DIV_RATIO = 4, parameter CHASER_WIDTH = 10) (
    input wire clk_50MHz,
    input wire Reset,
    input wire Hold,
    output wire [CHASER_WIDTH-1:0] reg_out
);

wire clk_8Hz_wire; 

clk_div #(.DIV_RATIO(CLK_DIV_RATIO)) divider_inst (
        .clk_in(clk_50MHz),
        .rstn(Reset),
        .clk_out(clk_8Hz_wire)
);

light_chaser #(.WIDTH(CHASER_WIDTH)) chaser_inst (
        .clk(clk_8Hz_wire),
        .Reset(Reset),
        .Hold(Hold),
        .reg_out(reg_out)
);
endmodule


`timescale 1ns / 1ps

module tb_top_level_light_chaser();
    reg clk_50MHz; 
    reg Reset;
    reg Hold;
    wire [9:0] reg_out;


top_level_light_chaser #(
        .CLK_DIV_RATIO(4), 
        .CHASER_WIDTH(10)
) dut (
        .clk_50MHz(clk_50MHz),
        .Reset(Reset),
        .Hold(Hold),
        .reg_out(reg_out)
);


    initial begin
        clk_50MHz = 0;
        forever #10 clk_50MHz = ~clk_50MHz;
    end

    initial begin
        // Initialize inputs
        Reset = 0;
        Hold = 1;

        $display("Applying reset...");
        #100;
        

        Reset = 1;

        $display("Running shift...");

        #1000; 
        
        $display("Applying Hold...");
        Hold = 0;
        #500;

        $display("Releasing Hold...");
        Hold = 1;
        #1000;
        
        $display("Simulation complete.");
        $stop;
    end

endmodule
