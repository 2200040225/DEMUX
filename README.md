Design code:

//gate level modeling

module demux(
    input wire din,
    input wire [1:0] sel,
    output reg [3:0] dout
);
    always @(*) begin
        dout = 4'b0000; 
        case (sel)
            2'b00: dout[0] = din;
            2'b01: dout[1] = din;
            2'b02: dout[2] = din;
            2'b03: dout[3] = din;
            default: dout = 4'b0000;
        endcase
    end
endmodule

//data flow modeling

module demux_dataflow(
    input wire din,
    input wire [1:0] sel,
    output wire [3:0] dout
);
    assign dout[0] = din & ~sel[1] & ~sel[0];
    assign dout[1] = din & ~sel[1] &  sel[0];
    assign dout[2] = din &  sel[1] & ~sel[0];
    assign dout[3] = din &  sel[1] &  sel[0];
endmodule

//behavioural modeling

module demux_gatelevel(
    input wire din,
    input wire [1:0] sel,
    output wire [3:0] dout
);
    wire not_sel0, not_sel1;

    not (not_sel0, sel[0]);
    not (not_sel1, sel[1]);

    and (dout[0], din, not_sel1, not_sel0);
    and (dout[1], din, not_sel1, sel[0]);
    and (dout[2], din, sel[1], not_sel0);
    and (dout[3], din, sel[1], sel[0]);
    endmodule


Test bench:

module tb_demux;
reg din;
reg [1:0] sel;
wire [3:0] dout_behavioral;
wire [3:0] dout_dataflow;
wire [3:0] dout_gatelevel;
demux U1 (.din(din), .sel(sel), .dout(dout));
initial begin
$monitor("Time=%0t din=%b sel=%b -> dout_behavioral=%b dout_dataflow=%b dout_gatelevel=%b", $time, din, sel, dout_behavioral, dout_dataflow, dout_gatelevel);
din = 1; sel = 2'b00; #10;
din = 1; sel = 2'b01; #10;
din = 1; sel = 2'b10; #10;
din = 1; sel = 2'b11; #10;
din = 0; sel = 2'b00; #10;
$finish;
end
endmodule




