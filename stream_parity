module stream_parity_gen(
       input wire clk,
       input wire reset,
       input wire serial_in,
       output wire  parity_out
);

reg [7:0] shift_reg;
always @(posedge clk)  begin
       if (reset) begin
          shift_reg <= 8'b0;
       end else begin
           shift_reg <= {shift_reg[6:0], serial_in};
end
end

function reg calc_even_parity;
     input [7:0] data;
      begin
       calc_even_parity = ^data;

end
endfunction

assign parity_out = calc_even_parity(shift_reg);

endmodule

module tb_stream_parity_gen();

    reg clk;
    reg reset;
    reg serial_in;
    wire parity_out;

integer i;
stream_parity_gen uut (
        .clk(clk),
        .reset(reset),
        .serial_in(serial_in),
        .parity_out(parity_out)
);
always #5 clk = ~clk;

task send_byte;
 input [7:0] data_in;
 integer bit_x;
 begin
    for(bit_x = 7; bit_x >= 0; bit_x= bit_x - 1 ) begin
        @(negedge clk); 
         serial_in = data_in[bit_x];
end
@(negedge clk); 
$display("Data Sent: %b | Parity Output: %b", data_in, parity_out);
end
endtask


initial begin
        clk = 0;
        reset = 0;
        serial_in = 0;

        #10 reset = 1;
        #10 reset = 0;

for (i = 0; i < 256; i = i + 1) begin 
send_byte(i[7:0]);
end

#100;
$stop;
end
endmodule












