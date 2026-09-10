module reset_logic (
    input  wire rst_in,
    output wire rst_out
);
    assign rst_out = rst_in;
endmodule

module write_control (
    input  wire wr_en,
    input  wire full,
    output wire mem_wr_en
);
    assign mem_wr_en = wr_en & ~full;
endmodule

module write_pointer #(parameter ADDR_WIDTH = 4) (
    input  wire clk,
    input  wire rst,
    input  wire mem_wr_en,
    output reg  [ADDR_WIDTH:0]   wr_ptr,
    output wire [ADDR_WIDTH-1:0] wr_addr
);

always @(posedge clk or posedge rst) begin
        if (rst) 
            wr_ptr <= 0;
        else if (mem_wr_en) 
            wr_ptr <= wr_ptr + 1;
end
    
assign wr_addr = wr_ptr[ADDR_WIDTH-1:0];

endmodule

// This prevents reading from the memory if the FIFO is empty,
module read_control (
    input  wire read_en,
    input  wire empty,
    output wire mem_rd_en
);

assign mem_rd_en = read_en & ~empty;

endmodule

module read_pointer #(parameter ADDR_WIDTH = 4) (
    input  wire clk,
    input  wire rst,
    input  wire mem_rd_en,
    output reg  [ADDR_WIDTH:0]   rd_ptr,
    output wire [ADDR_WIDTH-1:0] rd_addr
);

always @(posedge clk or posedge rst) begin
        if (rst) 
            rd_ptr <= 0;
        else if (mem_rd_en) 
            rd_ptr <= rd_ptr + 1;
end
    
assign rd_addr = rd_ptr[ADDR_WIDTH-1:0];

endmodule

module fifo_memory #(
    parameter DATA_WIDTH = 8,
    parameter ADDR_WIDTH = 4
) (
    input  wire clk,
    input  wire mem_wr_en,
    input  wire [ADDR_WIDTH-1:0] wr_addr,
    input  wire [DATA_WIDTH-1:0] data_in,
    input  wire [ADDR_WIDTH-1:0] rd_addr,
    output reg  [DATA_WIDTH-1:0] data_out
);

    localparam DEPTH = 1 << ADDR_WIDTH;
    reg [DATA_WIDTH-1:0] memory [0:DEPTH-1];

always @(posedge clk) begin
        if (mem_wr_en) begin
            memory[wr_addr] <= data_in;
        end
        data_out <= memory[rd_addr];
end
endmodule

module flag_logic #(
    parameter ADDR_WIDTH = 4,
    parameter ALMOST_FULL_THRESH = 14,
    parameter ALMOST_EMPTY_THRESH = 2
) (
    input  wire [ADDR_WIDTH:0] wr_ptr,
    input  wire [ADDR_WIDTH:0] rd_ptr,
    output wire empty,
    output wire full,
    output wire half_full,
    output wire almost_full,
    output wire almost_empty
);
    localparam DEPTH = 1 << ADDR_WIDTH;
    
    wire [ADDR_WIDTH:0] count = wr_ptr - rd_ptr;

    assign empty        = (count == 0);
    assign full         = (count == DEPTH);
    assign half_full    = (count == (DEPTH / 2));
    assign almost_full  = (count >= ALMOST_FULL_THRESH) & !full;
    assign almost_empty = (count <= ALMOST_EMPTY_THRESH) & !empty;
endmodule

// TOP LEVEL MODULE
module sync_fifo #(
    parameter DATA_WIDTH = 8,
    parameter ADDR_WIDTH = 4
) (
    input  wire clk,
    input  wire rst_in,
    input  wire wr_en,
    input  wire read_en,
    input  wire [DATA_WIDTH-1:0] input_data,
    
    output wire [DATA_WIDTH-1:0] output_data,
    output wire empty,
    output wire full,
    output wire half_full,
    output wire almost_full,
    output wire almost_empty
);

    wire rst_internal;
    wire mem_wr_en;
    wire mem_rd_en;
    
    wire [ADDR_WIDTH:0]   wr_ptr;
    wire [ADDR_WIDTH-1:0] wr_addr;
    
    wire [ADDR_WIDTH:0]   rd_ptr;
    wire [ADDR_WIDTH-1:0] rd_addr;

reset_logic u_reset (
        .rst_in  (rst_in),
        .rst_out (rst_internal)
);

write_control u_wr_ctrl (
        .wr_en     (wr_en),
        .full      (full),
        .mem_wr_en (mem_wr_en)
);

write_pointer #(.ADDR_WIDTH(ADDR_WIDTH)) u_wr_ptr (
        .clk       (clk),
        .rst       (rst_internal),
        .mem_wr_en (mem_wr_en),
        .wr_ptr    (wr_ptr),
        .wr_addr   (wr_addr)
);

read_control u_rd_ctrl (
        .read_en   (read_en),
        .empty     (empty),
        .mem_rd_en (mem_rd_en)
);

read_pointer #(.ADDR_WIDTH(ADDR_WIDTH)) u_rd_ptr (
        .clk       (clk),
        .rst       (rst_internal),
        .mem_rd_en (mem_rd_en),
        .rd_ptr    (rd_ptr),
        .rd_addr   (rd_addr)
);

fifo_memory #(
        .DATA_WIDTH(DATA_WIDTH),
        .ADDR_WIDTH(ADDR_WIDTH)
    ) u_memory (
        .clk       (clk),
        .mem_wr_en (mem_wr_en),
        .wr_addr   (wr_addr),
        .data_in   (input_data),
        .rd_addr   (rd_addr),
        .data_out  (output_data)
);

flag_logic #(
        .ADDR_WIDTH(ADDR_WIDTH),
        .ALMOST_FULL_THRESH(14),
        .ALMOST_EMPTY_THRESH(2)
    ) u_flags (
        .wr_ptr       (wr_ptr),
        .rd_ptr       (rd_ptr),
        .empty        (empty),
        .full         (full),
        .half_full    (half_full),
        .almost_full  (almost_full),
        .almost_empty (almost_empty)
);

endmodule


module tb_sync_fifo;

    parameter DATA_WIDTH = 8;
    parameter ADDR_WIDTH = 4;

    reg clk;
    reg rst_in;
    reg wr_en;
    reg read_en;
    reg [DATA_WIDTH-1:0] input_data;

    wire [DATA_WIDTH-1:0] output_data;
    wire empty;
    wire full;
    wire half_full;
    wire almost_full;
    wire almost_empty;

    integer err_count = 0;
    integer i;

sync_fifo #(
        .DATA_WIDTH(DATA_WIDTH),
        .ADDR_WIDTH(ADDR_WIDTH)
    ) dut (
        .clk(clk),
        .rst_in(rst_in),
        .wr_en(wr_en),
        .read_en(read_en),
        .input_data(input_data),
        .output_data(output_data),
        .empty(empty),
        .full(full),
        .half_full(half_full),
        .almost_full(almost_full),
        .almost_empty(almost_empty)
);

always #5 clk = ~clk;

// Function: Generates a predictable data pattern based on the loop index
function [DATA_WIDTH-1:0] gen_data;
        input [31:0] idx;
        begin
            gen_data = idx[7:0] + 8'hA0;
        end
endfunction

// Task: Writes a specific value into the FIFO
task write_data;
        input [31:0] idx;
        begin
            @(negedge clk);
            if (full) $display("WRITE_FAIL: FIFO Full at idx %0d", idx);
            wr_en = 1;
            input_data = gen_data(idx);
            @(negedge clk);
            wr_en = 0;
        end
endtask

task check_read;
        input [31:0] idx;
        reg [DATA_WIDTH-1:0] exp_data;
        begin
            @(negedge clk);
            if (empty) $display("READ_FAIL: FIFO Empty at idx %0d", idx);
            exp_data = gen_data(idx);
            
            if (output_data !== exp_data) begin
                $display("FAIL at idx %0d: expected %h, got %h", idx, exp_data, output_data);
                err_count = err_count + 1;
            end
            
            read_en = 1;
            @(negedge clk);
            read_en = 0;
        end
endtask

task check_flags;
        input exp_empty;
        input exp_full;
        begin
            if (empty !== exp_empty || full !== exp_full) begin
                $display("FLAG_FAIL: expected empty=%b full=%b, got empty=%b full=%b", exp_empty, exp_full, empty, full);
                err_count = err_count + 1;
            end
        end
endtask

initial begin
        clk = 0;
        rst_in = 1;
        wr_en = 0;
        read_en = 0;
        input_data = 0;
        
        #15 rst_in = 0;
        @(negedge clk);
        
        check_flags(1'b1, 1'b0);
        
        for (i = 0; i < 16; i = i + 1) begin
            write_data(i);
        end
        
        check_flags(1'b0, 1'b1);
        
        for (i = 0; i < 16; i = i + 1) begin
            check_read(i);
        end
        
        check_flags(1'b1, 1'b0);
        
        for (i = 0; i < 8; i = i + 1) begin
            write_data(i + 20);
        end
        
        for (i = 0; i < 8; i = i + 1) begin
            check_read(i + 20);
        end

        //Final Self-Checking
        if (err_count == 0)
            $display("STATUS: PASSED");
        else
            $display("STATUS: FAILED with %0d errors", err_count);
            
        $stop;
    end
endmodule
