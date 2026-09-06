module ram #(parameter ADDR_WIDTH = 8, parameter DATA_WIDTH = 20)(
    input wire clk,
    input wire rst_n,
    input wire wr_en,
    input wire [ADDR_WIDTH-1:0] addr,
    input wire [DATA_WIDTH-1:0] din,
    input wire rd_en,
    output reg [DATA_WIDTH-1:0] dout,
    output reg valid
);

reg [DATA_WIDTH-1:0] mem[0:(1<<ADDR_WIDTH)-1];
integer i;

always @(posedge clk or negedge rst_n) begin            
    if (!rst_n) begin
        dout <= {DATA_WIDTH{1'b0}};
        valid <= 1'b0;

        for (i = 0; i < (1<<ADDR_WIDTH); i = i + 1) begin
            mem[i] <= {DATA_WIDTH{1'b0}};
        end
end
else begin
     if (wr_en) begin
            mem[addr] <= din;
end
            
     if (rd_en) begin
            dout <= mem[addr];
            valid <= 1'b1;
end else begin
            valid <= 1'b0;
        end
end
end
endmodule

/////////////////////////////////////////////////////////////////////////

module sipo_reg #(parameter DATA_WIDTH=20)(
    input wire clk,
    input wire rst_n,
    input wire shift_en,
    input wire serial_in,
    output reg [DATA_WIDTH-1:0] parallel_out
);

always @(posedge clk or negedge rst_n) begin 
    if (!rst_n) begin
        parallel_out <= {DATA_WIDTH{1'b0}};
    end 
    else if (shift_en) begin
        parallel_out <= {parallel_out[DATA_WIDTH-2:0], serial_in};
    end
end
endmodule

////////////////////////////////////////////////////////////////////////

module alu #(parameter DATA_WIDTH=8)(
    input wire [DATA_WIDTH-1:0] in_a,
    input wire [DATA_WIDTH-1:0] in_b,
    input wire [2:0] opcode,
    input wire alu_en,
    output reg [DATA_WIDTH-1:0] alu_out,
    output  a_zero
);

assign a_zero = (in_a == 0) ? 1'b1 : 1'b0;

always @(*) begin
    if (alu_en) begin
        case (opcode)
            3'b000: alu_out = in_a + in_b;    //add              
            3'b001: alu_out = in_a - in_b;    //sub        
            3'b010: alu_out = in_a & in_b;    //and        
            3'b011: alu_out = in_a ^ in_b;    //xor         
            3'b100: alu_out = in_a | in_b;    //or       
            3'b101: alu_out = in_a;                  
            default: alu_out = {DATA_WIDTH{1'b0}};
        endcase
    end else begin
        alu_out = {DATA_WIDTH{1'b0}};
    end
end
endmodule

/////////////////////////////////////////////////////////////////////

module piso_reg #(
    parameter DATA_WIDTH = 20
)(
    input wire clk,
    input wire Rst_n,
    input wire load, 
    input wire [DATA_WIDTH-1:0] Parallel_in,
    output reg serial_out,
    output reg valid
);

reg [DATA_WIDTH-1:0] shift_reg;
reg [4:0] count;
reg busy;

always @(posedge clk or negedge Rst_n) begin
    if (!Rst_n) begin
        serial_out <= 1'b0;
        valid <= 1'b0;
        shift_reg <= 0;
        count <= 0;
        busy <= 1'b0;
    end else begin

        if (load && !busy) begin
            shift_reg <= Parallel_in;
            serial_out <= Parallel_in[DATA_WIDTH-1]; 
            valid <= 1'b1;                      
            count <= DATA_WIDTH - 1;                 
            busy <= 1'b1; 
        end else if (busy) begin 
            if (count > 0) begin
                shift_reg <= {shift_reg[DATA_WIDTH-2:0], 1'b0}; 
                serial_out <= shift_reg[DATA_WIDTH-2];          
                valid <= 1'b1;
                count <= count - 1;
            end else begin
                valid <= 1'b0;
                serial_out <= 1'b0;
                busy <= 1'b0; // Done shifting
            end
        end else begin
            valid <= 1'b0;	
            serial_out <= 1'b0;
        end
    end
end
endmodule

/////////////////////////////////////////////////////////////////////

module top_module2 #(
    parameter ADDR_WIDTH = 8,
    parameter DATA_WIDTH = 20,
    parameter ALU_WIDTH = 8
)(
    input wire clk,
    input wire rst_n,
    input wire ram_wr_en,
    input wire ram_rd_en,
    input wire [ADDR_WIDTH-1:0] ram_addr, 
    input wire [DATA_WIDTH-1:0] ram_din,
    output wire [ALU_WIDTH-1:0] alu_out,
    output wire a_is_zero
);
    
    wire [DATA_WIDTH-1:0] ram_dout;
    wire ram_valid_out; 
    wire serial_link;
    wire valid_to_shift_en;
    wire [DATA_WIDTH-1:0] in_word;

//////////////////////////////////////////////////////////
ram #(
    .ADDR_WIDTH(ADDR_WIDTH),
    .DATA_WIDTH(DATA_WIDTH)
) u_ram (
    .clk(clk),
    .rst_n(rst_n),
    .wr_en(ram_wr_en),
    .addr(ram_addr),       
    .din(ram_din),
    .rd_en(ram_rd_en),    
    .dout(ram_dout),
    .valid(ram_valid_out)
);

//////////////////////////////////////////////////////////
piso_reg #(
    .DATA_WIDTH(DATA_WIDTH) 
) u_piso (
    .clk(clk),
    .Rst_n(rst_n),
    .load(ram_valid_out),  
    .Parallel_in(ram_dout),
    .serial_out(serial_link),
    .valid(valid_to_shift_en) 
);

//////////////////////////////////////////////////////////
sipo_reg #(
    .DATA_WIDTH(DATA_WIDTH) 
) u_sipo (
    .clk(clk),
    .rst_n(rst_n),
    .shift_en(valid_to_shift_en), 
    .serial_in(serial_link),
    .parallel_out(in_word)
);

//////////////////////////////////////////////////////////
alu #(
    .DATA_WIDTH(ALU_WIDTH) 
) u_alu (
    .in_a(in_word[15:8]),
    .in_b(in_word[7:0]),
    .opcode(in_word[18:16]),
    .alu_en(in_word[19]),
    .alu_out(alu_out),
    .a_zero(a_is_zero)
);

endmodule

/////////////////////////////////////////////////////////////////////

`timescale 1ns/1ps

module tb_top_module4();

    parameter ADDR_WIDTH = 8;
    parameter DATA_WIDTH = 20;
    parameter ALU_WIDTH = 8;

    reg clk;
    reg rst_n;
    reg ram_wr_en;
    reg ram_rd_en;
    reg [ADDR_WIDTH-1:0] ram_addr;
    reg [DATA_WIDTH-1:0] ram_din;
    
    wire [ALU_WIDTH-1:0] alu_out;
    wire a_is_zero;

    top_module2 #(
        .ADDR_WIDTH(ADDR_WIDTH),
        .DATA_WIDTH(DATA_WIDTH),
        .ALU_WIDTH(ALU_WIDTH)
    ) uut (
        .clk(clk),
        .rst_n(rst_n),
        .ram_wr_en(ram_wr_en),
        .ram_rd_en(ram_rd_en),
        .ram_addr(ram_addr),
        .ram_din(ram_din),
        .alu_out(alu_out),
        .a_is_zero(a_is_zero)
    );

    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end
   
    initial begin
        rst_n = 0;
        ram_wr_en = 0;
        ram_rd_en = 0;
        ram_addr = 0;
        ram_din = 0;

        #20;
        rst_n = 1;

 
        @(posedge clk);
        ram_wr_en = 1;
        ram_addr = 8'd0; 
        ram_din = {1'b1, 3'b000, 8'd15, 8'd10};
        
        @(posedge clk);
        ram_wr_en = 0;
        

        @(posedge clk);
        ram_rd_en = 1;
        ram_addr = 8'd0;
        
        @(posedge clk);
        ram_rd_en = 0; 

        #250; 
        $display("Time: %0t | Op: ADD | A: 15, B: 10 | ALU Out: %d | a_is_zero: %b", 
                 $time, alu_out, a_is_zero);


        @(posedge clk);
        ram_wr_en = 1;
        ram_addr = 8'd1; 
        ram_din = {1'b1, 3'b001, 8'd20, 8'd5};
        
        @(posedge clk);
        ram_wr_en = 0;
        
        @(posedge clk);
        ram_rd_en = 1;
        ram_addr = 8'd1;
        
        @(posedge clk);
        ram_rd_en = 0;

        #250; 
        $display("Time: %0t | Op: SUB | A: 20, B: 5  | ALU Out: %d | a_is_zero: %b", 
                 $time, alu_out, a_is_zero);

        #50;
        $stop; 
    end
endmodule
